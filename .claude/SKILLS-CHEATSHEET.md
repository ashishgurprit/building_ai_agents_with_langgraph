# Skills & Commands Cheat Sheet

> Quick reference for 11 skills + 10 workflow commands

---

# PART 1: WORKFLOW COMMANDS

> Invoke with `/project:command` - structured multi-step workflows

---

## /project:explore
**Purpose:** Understand codebase without writing code

```
┌─────────────────────────────────────┐
│  1. Analyze folder structure        │
│  2. Identify key files & patterns   │
│  3. Map dependencies                │
│  4. Document findings               │
│  NO CODE WRITING - Research only    │
└─────────────────────────────────────┘
```

**Use when:** New to codebase, before planning features

---

## /project:plan
**Purpose:** Research-first planning, creates plan.md

```
┌─────────────────────────────────────┐
│  1. Parse the request               │
│  2. Research existing code          │
│  3. Identify integration points     │
│  4. List files to modify            │
│  5. Write plan.md for approval      │
│  NO CODE WRITING - Plan only        │
└─────────────────────────────────────┘
```

**Use when:** Starting new feature, need structured approach

---

## /project:scaffold
**Purpose:** Generate boilerplate files

```
┌─────────────────────────────────────┐
│  1. Detect project type/framework   │
│  2. Analyze existing conventions    │
│  3. Generate files matching style   │
│  4. Include tests + types           │
└─────────────────────────────────────┘
```

**Use when:** New component, new module, new API endpoint

---

## /project:implement
**Purpose:** TDD workflow (tests first)

```
┌──────────┐   ┌──────────┐   ┌──────────┐
│  WRITE   │──▶│   RUN    │──▶│  FAIL    │
│  TESTS   │   │  TESTS   │   │  (RED)   │
└──────────┘   └──────────┘   └────┬─────┘
                                   │
┌──────────┐   ┌──────────┐   ┌────▼─────┐
│  COMMIT  │◀──│  PASS    │◀──│ WRITE    │
│   ALL    │   │ (GREEN)  │   │  CODE    │
└──────────┘   └──────────┘   └──────────┘
```

**Use when:** Building new functionality with tests

---

## /project:test-fix
**Purpose:** Auto-fix all failing tests

```
┌─────────────────────────────────────┐
│  1. Run full test suite             │
│  2. Parse failures                  │
│  3. Fix each failure                │
│  4. Re-run until all pass           │
│  5. Commit when green               │
└─────────────────────────────────────┘
```

**Use when:** Tests failing, need automated fixes

---

## /project:review
**Purpose:** Code review with security checks

```
┌─────────────────────────────────────┐
│  1. Get staged/unstaged changes     │
│  2. Check for security issues       │
│  3. Verify best practices           │
│  4. Check test coverage             │
│  5. Report findings                 │
└─────────────────────────────────────┘
```

**Use when:** Before commit, before PR

---

## /project:fix-issue
**Purpose:** Resolve GitHub issue end-to-end

```
┌─────────────────────────────────────┐
│  1. Fetch issue via gh CLI          │
│  2. Analyze problem                 │
│  3. Find relevant code              │
│  4. Implement fix with tests        │
│  5. Create PR                       │
└─────────────────────────────────────┘
```

**Use when:** `gh issue view 123` → fix → PR

---

## /project:pre-deploy
**Purpose:** Pre-deployment checklist

```
┌─────────────────────────────────────┐
│  ✓ Tests passing                    │
│  ✓ Lint clean                       │
│  ✓ Security scan                    │
│  ✓ Migrations ready                 │
│  ✓ Env vars set                     │
│  ✓ Rollback plan                    │
│  ✓ LESSONS.md checks applied        │
└─────────────────────────────────────┘
```

**Use when:** Before deploying to staging/production

---

## /project:post-mortem
**Purpose:** Capture lessons after debugging

```
┌─────────────────────────────────────┐
│  1. What was the issue?             │
│  2. What was the root cause?        │
│  3. How was it fixed?               │
│  4. How to prevent in future?       │
│  5. Add to LESSONS.md               │
└─────────────────────────────────────┘
```

**Use when:** After fixing bugs, "aha" moments

---

## /project:sync
**Purpose:** Sync with Streamlined Development master

```
┌─────────────────────────────────────┐
│  1. Check current version           │
│  2. Compare with master             │
│  3. Show changelog                  │
│  4. Pull new lessons & commands     │
│  5. Update version marker           │
└─────────────────────────────────────┘
```

**Use when:** Get latest lessons from master repo

---

## Commands Quick Reference

| Command | Phase | Key Action |
|---------|-------|------------|
| `/project:explore` | Ideation | Research codebase |
| `/project:plan` | Architecting | Create plan.md |
| `/project:scaffold` | Coding | Generate boilerplate |
| `/project:implement` | Coding | TDD loop |
| `/project:test-fix` | Testing | Fix all failures |
| `/project:review` | Re-coding | Quality check |
| `/project:pre-deploy` | Staging | Checklist |
| `/project:post-mortem` | Production | Capture lessons |
| `/project:fix-issue` | Any | GitHub issue → PR |
| `/project:sync` | Any | Update from master |

---

# PART 2: AUTO-DISCOVERED SKILLS

> No command needed - Claude applies these automatically

---

## 1. Design System
**Triggers:** `component`, `styling`, `CSS`, `UI`, `UX`

```
Colors     → var(--color-primary-500)
Spacing    → var(--space-4)
Typography → var(--text-lg)
Radius     → var(--radius-md)
```

**Use when:** Building UI components, styling, theming

---

## 2. API Patterns
**Triggers:** `endpoint`, `REST`, `API`, `request`, `response`

```
GET    /resources          → List
GET    /resources/:id      → Read
POST   /resources          → Create
PUT    /resources/:id      → Update
DELETE /resources/:id      → Delete
```

**Use when:** Building APIs, handling HTTP requests

---

## 3. Testing Strategies
**Triggers:** `test`, `spec`, `coverage`, `mock`, `fixture`

```
Unit        → 70% (fast, isolated)
Integration → 20% (service boundaries)
E2E         → 10% (critical paths only)
```

**Use when:** Writing tests, setting up test infrastructure

---

## 4. Deployment Patterns
**Triggers:** `deploy`, `release`, `staging`, `production`

```
Pre-deploy  → Tests pass? Migrations ready?
Smoke test  → Health check, critical paths
Rollback    → Previous version tagged?
```

**Use when:** Deploying, release management

---

## 5. Google Engineering
**Triggers:** `code review`, `design doc`, `CL`, `SRE`

```
Small CLs   → <200 lines, single purpose
Design Doc  → Context → Goals → Non-Goals → Options
Code Review → Correctness → Style → Consistency
SRE         → SLIs → SLOs → Error Budgets
```

**Use when:** Code reviews, architecture decisions, reliability

---

## 6. Atlassian Practices
**Triggers:** `sprint`, `agile`, `DACI`, `incident`, `retro`

```
DACI        → Driver, Approver, Contributor, Informed
DoD         → Tests + Review + Docs + Deployed
Incident    → Detect → Respond → Recover → Learn
Health Mon  → Team satisfaction signals
```

**Use when:** Agile ceremonies, decision-making, incidents

---

## 7. Canva Practices
**Triggers:** `design-first`, `i18n`, `a11y`, `feature flag`

```
Design-First   → Validate before building
Components     → Composable, single-purpose
i18n           → t('key'), no hardcoded strings
a11y           → Semantic HTML, keyboard nav
Feature Flags  → Gradual rollout, kill switch
```

**Use when:** Product development, internationalization, accessibility

---

## 8. Security (OWASP)
**Triggers:** `auth`, `injection`, `XSS`, `CSRF`, `security`

```
Injection   → Parameterized queries ONLY
Auth        → bcrypt + rate limit + MFA
Access      → Check permissions EVERY request
Headers     → CSP, HSTS, X-Frame-Options
Secrets     → Never in code, use env vars
```

**Use when:** Authentication, authorization, data protection

---

## 9. Performance
**Triggers:** `optimize`, `slow`, `cache`, `bundle`, `latency`

```
Frontend    → Code split, lazy load, virtualize
Backend     → N+1 fix, connection pool, async
Caching     → Redis, HTTP headers, memoize
Measure     → Lighthouse, EXPLAIN ANALYZE
```

**Use when:** Optimization, profiling, caching strategies

---

## 10. CI/CD Templates
**Triggers:** `pipeline`, `GitHub Actions`, `Docker`, `deploy`

```
CI Pipeline → Test → Lint → Build → Deploy
Docker      → Multi-stage, non-root user
Compose     → Services + healthchecks + volumes
Pre-commit  → trailing-whitespace, ruff, eslint
```

**Use when:** Setting up pipelines, containerization

---

## 11. Database Patterns
**Triggers:** `SQL`, `migration`, `index`, `schema`, `query`

```
Naming      → tables: plural, cols: singular
Indexes     → Foreign keys, WHERE, ORDER BY
N+1 Fix     → joinedload() or subqueryload()
Migrations  → Nullable first → backfill → constraint
```

**Use when:** Schema design, query optimization, migrations

---

## Quick Lookup Table

| Skill | Primary Use Case | Key Pattern |
|-------|------------------|-------------|
| design-system | UI/UX | Use tokens, not hardcoded values |
| api-patterns | REST APIs | Consistent endpoints & errors |
| testing-strategies | Tests | 70/20/10 pyramid |
| deployment-patterns | Releases | Pre-deploy checklist |
| google-engineering | Code quality | Small CLs, design docs |
| atlassian-practices | Agile/Teams | DACI, DoD, retros |
| canva-practices | Product dev | Design-first, a11y |
| security-owasp | Security | OWASP Top 10 prevention |
| performance | Speed | Measure first, then optimize |
| cicd-templates | DevOps | Pipeline templates |
| database-patterns | Data | Indexing, safe migrations |

---

## How Skills Auto-Activate

```
You: "Add a login form with validation"
     ↓
Claude detects: "form", "validation"
     ↓
Auto-applies: design-system + security-owasp
     ↓
Result: Accessible form with CSRF protection
```

---

## Customize Skills

```bash
# Edit any skill for your project
vi .claude/skills/design-system/colors.md

# Add project-specific patterns
vi .claude/skills/api-patterns/SKILL.md
```

---

*Generated from Streamlined Development v1.0.0*
