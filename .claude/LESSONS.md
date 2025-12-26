# Master Lessons Learned

> Consolidated lessons from ALL projects using Streamlined Development.
> This file is synced to all projects and evolves over time.
>
> Version: 1.0.0 | Last Updated: 2024-12-26

---

## Quick Reference Checklist

Before ANY deployment:

### Identity & Auth
- [ ] Tested with REAL auth provider IDs (not mock UUIDs)
- [ ] Credential format matches platform (base64/raw/file)
- [ ] Auth headers pass through proxy/CDN

### Database
- [ ] ID column types handle auth provider formats
- [ ] Migrations run BEFORE app startup
- [ ] Connection pool appropriately sized

### API
- [ ] Frontend/backend IDs match (contract test)
- [ ] Error responses structured and displayed
- [ ] CORS configured for production domain

### Payments
- [ ] Webhook handlers are transactional
- [ ] Returns 500 on any partial failure
- [ ] Test vs live keys correct

---

## Categories

- [Authentication & Identity](#authentication--identity)
- [Database & Data Types](#database--data-types)
- [API Contracts](#api-contracts)
- [Environment & Configuration](#environment--configuration)
- [Deployment & Infrastructure](#deployment--infrastructure)
- [Payment Integration](#payment-integration)
- [Testing Strategies](#testing-strategies)

---

## Authentication & Identity

### LESSON: Firebase UID ≠ UUID
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: `badly formed hexadecimal UUID string` in database operations

**Root Cause**: Firebase UIDs (e.g., `un42YcgdaeQreBdzKP0PAOyRD4n2`) are alphanumeric strings, not valid UUIDs. PostgreSQL UUID columns reject them.

**Solution**:
```python
import uuid

FIREBASE_NAMESPACE = uuid.UUID('6ba7b810-9dad-11d1-80b4-00c04fd430c8')

def firebase_uid_to_uuid(user_id: str) -> uuid.UUID:
    """Convert any user ID to valid UUID deterministically."""
    try:
        return uuid.UUID(user_id)  # Already valid UUID
    except ValueError:
        return uuid.uuid5(FIREBASE_NAMESPACE, user_id)  # Convert
```

**Prevention**:
- [ ] Test database ops with REAL Firebase UIDs, not mock UUIDs
- [ ] Add `test_firebase_uid_database_roundtrip()` integration test
- [ ] Use conversion helper in ALL database operations

---

### LESSON: Firebase Credentials Format Varies by Platform
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: Firebase init fails silently or with JSON parse errors

**Root Cause**: Different platforms expect different formats:
- Railway: Base64-encoded JSON
- Vercel: Raw JSON (escaped)
- Local: File path

**Solution**:
```python
import base64, json, os

def parse_firebase_credentials(env_value: str) -> dict:
    """Parse Firebase credentials from multiple formats."""
    # Try base64 first (Railway)
    try:
        decoded = base64.b64decode(env_value)
        return json.loads(decoded)
    except:
        pass

    # Try raw JSON (Vercel)
    try:
        return json.loads(env_value)
    except:
        pass

    # Try file path (local)
    if os.path.exists(env_value):
        with open(env_value) as f:
            return json.load(f)

    raise ValueError(f"Cannot parse Firebase credentials")
```

**Prevention**:
- [ ] Add startup validation for credential format
- [ ] Document expected format per platform in README
- [ ] Test credential parsing in CI

---

### LESSON: Auth Headers Lost Through Proxy
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: 401 errors only in production, works locally

**Root Cause**: Nginx/CDN strips or doesn't forward Authorization header

**Solution**:
```nginx
location /api {
    proxy_pass http://backend;
    proxy_set_header Authorization $http_authorization;
    proxy_pass_request_headers on;
}
```

**Prevention**:
- [ ] Smoke test auth endpoint after deployment
- [ ] Test with actual proxy in staging
- [ ] Verify header passthrough in proxy config

---

## Database & Data Types

### LESSON: ID Format Assumptions Break at Integration
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: Unit tests pass, production fails with ID errors

**Root Cause**: Unit tests use `uuid.uuid4()` which produces valid UUIDs. Real auth systems use provider-specific formats that aren't UUIDs.

**Solution**: Test with realistic ID formats:
```python
# In tests
SAMPLE_REALISTIC_IDS = [
    "un42YcgdaeQreBdzKP0PAOyRD4n2",  # Firebase
    "auth0|abc123def456",             # Auth0
    "google-oauth2|123456789",        # Google OAuth
    str(uuid.uuid4()),                # Actual UUID
]

@pytest.mark.parametrize("user_id", SAMPLE_REALISTIC_IDS)
def test_user_operations(user_id):
    # Test with each ID format
    result = create_user(user_id)
    assert result.success
```

**Prevention**:
- [ ] Integration tests MUST use realistic ID formats
- [ ] Parameterize tests with all expected ID formats
- [ ] Add sample IDs from each auth provider to test fixtures

---

### LESSON: Migrations Block Startup
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: App hangs on startup, times out

**Root Cause**: Auto-migration acquires database lock, blocks if another process holds it (like a previous failed deployment)

**Solution**:
1. Run migrations as separate job, not on app startup
2. Add timeout to migration lock acquisition
3. Add `--skip-migrations` flag

```python
# In startup
if not os.environ.get("SKIP_MIGRATIONS"):
    try:
        with timeout(30):
            run_migrations()
    except TimeoutError:
        logger.error("Migration lock timeout - run manually")
```

**Prevention**:
- [ ] Never auto-run migrations in production startup
- [ ] Separate migration step in CI/CD pipeline
- [ ] Add migration health check endpoint

---

## API Contracts

### LESSON: Frontend/Backend ID Mismatch
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: Checkout buttons do nothing, no console errors

**Root Cause**: Frontend sent `pack_10`, backend expected `credits_10`. No validation = silent failure.

**Solution**: Contract tests
```python
# tests/test_contracts.py
def test_frontend_backend_plan_ids_match():
    """Verify frontend plan IDs exist in backend."""
    # These should match frontend constants
    frontend_ids = ["credits_10", "credits_25", "credits_50"]
    backend_ids = list(PRICE_IDS.keys())

    for fid in frontend_ids:
        assert fid in backend_ids, \
            f"Frontend uses '{fid}' but backend doesn't recognize it"
```

**Prevention**:
- [ ] Contract tests in CI (run before deploy)
- [ ] Share types between FE/BE (TypeScript, OpenAPI)
- [ ] Backend returns helpful error for unknown IDs

---

### LESSON: Silent API Failures
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: UI does nothing on interaction

**Root Cause**: API returned error but frontend didn't handle/display it

**Solution**: Structured error handling on both sides
```typescript
// Frontend
async function callApi(endpoint: string, data: any) {
  try {
    const response = await fetch(endpoint, { ... });
    const result = await response.json();

    if (!response.ok) {
      // ALWAYS show error to user
      toast.error(result.detail || result.error || 'Request failed');
      return null;
    }
    return result;
  } catch (e) {
    toast.error('Network error - please try again');
    return null;
  }
}
```

**Prevention**:
- [ ] E2E test for error display
- [ ] Require error handling in code review
- [ ] Backend always returns `{detail: string}` on error

---

## Environment & Configuration

### LESSON: Validate Configuration at Startup
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: App starts but features fail at runtime

**Root Cause**: Missing/invalid env vars not caught until user hits the feature

**Solution**: Startup validation that fails fast
```python
def validate_startup_environment():
    """Validate all config at startup. Fail fast."""
    errors = []

    # Required vars
    for var in ["DATABASE_URL", "STRIPE_SECRET_KEY"]:
        if not os.environ.get(var):
            errors.append(f"Missing required: {var}")

    # Format validation
    try:
        parse_firebase_credentials(os.environ.get("FIREBASE_CREDENTIALS", ""))
    except ValueError as e:
        errors.append(f"Invalid FIREBASE_CREDENTIALS: {e}")

    # Stripe price IDs exist
    for plan in ["credits_10", "credits_25"]:
        if not PRICE_IDS.get(plan):
            errors.append(f"Missing Stripe price for: {plan}")

    if errors:
        for e in errors:
            logger.error(f"[STARTUP] {e}")
        if os.environ.get("FAIL_ON_CONFIG_ERROR"):
            sys.exit(1)

    return len(errors) == 0
```

**Prevention**:
- [ ] Add `validate_startup_environment()` to every project
- [ ] CI runs startup in validation mode
- [ ] Log all config issues prominently

---

## Payment Integration

### LESSON: Webhook Success ≠ Operation Success
**Source**: ContentSage | **Date**: 2024-12-26

**Symptom**: Stripe shows payment success, user has no credits

**Root Cause**: Webhook handler returned 200 (Stripe happy) before database update completed (or failed)

**Solution**: Transactional webhook handling
```python
@app.post("/webhook")
async def stripe_webhook(request: Request):
    try:
        event = stripe.Webhook.construct_event(
            await request.body(),
            request.headers.get("stripe-signature"),
            WEBHOOK_SECRET
        )

        # ALL operations must succeed
        async with database.transaction():
            await process_checkout(event)
            await update_user_credits(event)
            await log_transaction(event)

        # Only return 200 if everything worked
        return {"status": "success"}

    except Exception as e:
        logger.error(f"Webhook failed: {e}")
        # Return 500 so Stripe retries
        return JSONResponse(status_code=500, content={"error": str(e)})
```

**Prevention**:
- [ ] Webhook handlers must be transactional
- [ ] Return 500 on ANY failure (Stripe will retry)
- [ ] Add reconciliation job: compare Stripe payments vs database

---

## Testing Strategies

### LESSON: Unit Tests Don't Catch Integration Issues
**Source**: ContentSage | **Date**: 2024-12-26

**Evidence**: All unit tests passed, but production had:
- Firebase UID format errors
- Nginx proxy auth failures
- Frontend/backend contract mismatches

**Solution**: Layered testing strategy

```
┌─────────────────────────────────────────────────────────────┐
│                    TESTING PYRAMID                          │
├─────────────────────────────────────────────────────────────┤
│                        /\                                   │
│                       /  \    E2E (user flows)              │
│                      /    \   10% of tests                  │
│                     /──────\                                │
│                    /        \                               │
│                   /          \  Integration                 │
│                  /            \ (service boundaries)        │
│                 /──────────────\ 20% of tests               │
│                /                \                           │
│               /                  \  Contract Tests          │
│              /                    \ (FE/BE agreement)       │
│             /────────────────────────\ 20% of tests         │
│            /                          \                     │
│           /                            \  Unit Tests        │
│          /                              \ (logic)           │
│         /────────────────────────────────\ 50% of tests     │
│                                                             │
│  + Post-Deploy Smoke Tests (health, auth, critical paths)   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

| Layer | What It Catches | Example |
|-------|----------------|---------|
| Unit | Logic bugs | `test_calculate_discount()` |
| Contract | FE/BE mismatch | `test_plan_ids_match()` |
| Integration | Service boundaries | `test_firebase_to_database()` |
| E2E | Full user flows | `test_complete_purchase()` |
| Smoke | Deployment issues | `curl /health` after deploy |

**Prevention**:
- [ ] Require all 5 layers before production
- [ ] Smoke tests auto-run after deploy
- [ ] Block deploy if contract tests fail

---

## Template for New Lessons

```markdown
### LESSON: [Short Title]
**Source**: [Project Name] | **Date**: YYYY-MM-DD

**Symptom**: [What the user/developer observed]

**Root Cause**: [Why it happened]

**Solution**:
```code if applicable```

**Prevention**:
- [ ] [Actionable checklist item]
- [ ] [Another prevention measure]
```

---

## Contributing

To add lessons to this master file:

```bash
# After debugging in your project
/project:post-mortem  # Captures lesson locally

# Then contribute to master
~/streamlined-development/scripts/contribute-lesson.sh
```
