---
description: Pre-deployment checklist with lessons applied
allowed-tools: ["Read", "Bash", "Task"]
---

# Pre-Deployment Checks

Comprehensive checks before deploying, incorporating lessons learned.

## Instructions

### Phase 1: Load Lessons

Read lessons from:
1. `.claude/LESSONS.md` (master lessons)
2. `.claude/LESSONS-PROJECT.md` (project-specific)

Extract all prevention checklist items.

### Phase 2: Environment Validation

Check configuration:

```bash
# Required environment variables
echo "=== Environment Check ==="

# List based on project type
REQUIRED_VARS="DATABASE_URL"

# Add based on detected features
if grep -q "stripe" package.json pyproject.toml 2>/dev/null; then
    REQUIRED_VARS="$REQUIRED_VARS STRIPE_SECRET_KEY STRIPE_WEBHOOK_SECRET"
fi

if grep -q "firebase" package.json pyproject.toml 2>/dev/null; then
    REQUIRED_VARS="$REQUIRED_VARS FIREBASE_CREDENTIALS"
fi

for var in $REQUIRED_VARS; do
    if [ -z "${!var}" ]; then
        echo "❌ Missing: $var"
    else
        echo "✓ Set: $var"
    fi
done
```

### Phase 3: Run Contract Tests

If `tests/test_contracts.py` or similar exists:

```bash
echo "=== Contract Tests ==="
pytest tests/test_contracts.py -v --tb=short 2>/dev/null || \
npm test -- --testPathPattern=contract 2>/dev/null || \
echo "No contract tests found"
```

### Phase 4: Run Integration Tests

```bash
echo "=== Integration Tests ==="
pytest tests/test_integration.py -v --tb=short 2>/dev/null || \
npm test -- --testPathPattern=integration 2>/dev/null || \
echo "No integration tests found"
```

### Phase 5: Startup Validation

If project has startup validation:

```bash
echo "=== Startup Validation ==="
# Python
python -c "from src.startup_validation import validate_startup_environment; validate_startup_environment()" 2>/dev/null || \

# Node
node -e "require('./src/startup-validation').validate()" 2>/dev/null || \

echo "No startup validation found (consider adding)"
```

### Phase 6: Lessons Checklist

Review each prevention item from lessons. Output checklist:

**Authentication & Identity** (from lessons)
- [ ] Tested with REAL auth provider IDs (not mock UUIDs)
- [ ] Credential format matches deployment platform
- [ ] Auth headers pass through proxy/CDN

**Database** (from lessons)
- [ ] ID column types match auth provider format
- [ ] Migrations run separately from app startup
- [ ] Connection string verified

**API Contracts** (from lessons)
- [ ] Frontend/backend IDs match (contract test)
- [ ] Error responses are structured and displayed
- [ ] CORS configured for production domain

**Payments** (if applicable, from lessons)
- [ ] Webhook URL registered in dashboard
- [ ] Webhook handler is transactional
- [ ] Test vs live mode keys verified

### Phase 7: Generate Report

**All Checks Pass:**
```
╔════════════════════════════════════════════════════════════╗
║                 DEPLOYMENT READY ✓                         ║
╠════════════════════════════════════════════════════════════╣
║ Environment:    ✓ All required vars set                    ║
║ Contract Tests: ✓ 5/5 passed                               ║
║ Integration:    ✓ 8/8 passed                               ║
║ Startup:        ✓ Validation passed                        ║
║ Lessons:        ✓ 12/12 items verified                     ║
╠════════════════════════════════════════════════════════════╣
║ Ready to deploy!                                           ║
║                                                            ║
║ After deploy, run smoke tests:                             ║
║   ./scripts/smoke-test.sh production                       ║
╚════════════════════════════════════════════════════════════╝
```

**Issues Found:**
```
╔════════════════════════════════════════════════════════════╗
║                 DEPLOYMENT BLOCKED ✗                       ║
╠════════════════════════════════════════════════════════════╣
║ ❌ Contract Tests: 1 failure                               ║
║    - test_plan_ids_match: 'pack_10' not in backend         ║
║                                                            ║
║ ⚠️  Warning: No integration tests for Firebase UIDs        ║
║    (See LESSON: Firebase UID ≠ UUID)                       ║
╠════════════════════════════════════════════════════════════╣
║ Fix issues before deploying                                ║
╚════════════════════════════════════════════════════════════╝
```

## Arguments

$ARGUMENTS

- `--skip-tests`: Only check environment, skip tests
- `--verbose`: Show all checks, not just failures
- `--fix`: Attempt to fix common issues
