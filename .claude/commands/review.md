---
description: Review code changes for quality, security, and best practices
allowed-tools: ["Read", "Bash", "Grep", "Glob"]
---

# Code Review

Comprehensive review of code changes before commit/PR.

## Instructions

### Phase 1: Get Changes

```bash
# Unstaged changes
git diff

# Staged changes
git diff --cached

# All changes
git status
```

### Phase 2: Review Each File

For each modified file, check:

**Correctness**
- [ ] Logic is correct
- [ ] Edge cases handled
- [ ] No obvious bugs
- [ ] Error handling present

**Code Quality**
- [ ] Follows project conventions
- [ ] No code duplication
- [ ] Functions are focused (single responsibility)
- [ ] Naming is clear and consistent
- [ ] No dead code or TODOs left behind

**Security (OWASP Top 10)**
- [ ] No hardcoded secrets
- [ ] Input validation present
- [ ] No SQL injection risks
- [ ] No XSS vulnerabilities
- [ ] Auth/authz properly checked
- [ ] Sensitive data not logged

**Performance**
- [ ] No N+1 queries
- [ ] No unnecessary loops
- [ ] No memory leaks
- [ ] Appropriate caching

**Testing**
- [ ] Changes have tests
- [ ] Tests are meaningful
- [ ] Edge cases tested

**Documentation**
- [ ] Complex logic explained
- [ ] Public APIs documented
- [ ] README updated if needed

### Phase 3: Check Against Lessons

Review `.claude/LESSONS.md` for relevant patterns:
- Any lessons apply to these changes?
- Following prevention checklists?

### Phase 4: Generate Report

## Output Format

```
╔════════════════════════════════════════════════════════════╗
║                    CODE REVIEW                             ║
╠════════════════════════════════════════════════════════════╣
║ Files Reviewed: 5                                          ║
║ Lines Changed: +127 / -34                                  ║
╠════════════════════════════════════════════════════════════╣
║ CRITICAL (Must Fix)                                        ║
║   ✗ src/api/auth.ts:45 - SQL injection risk               ║
║   ✗ src/config.ts:12 - Hardcoded API key                  ║
╠════════════════════════════════════════════════════════════╣
║ WARNINGS (Should Fix)                                      ║
║   ⚠ src/utils/parse.ts:23 - Missing null check            ║
║   ⚠ src/components/Form.tsx - No error handling           ║
╠════════════════════════════════════════════════════════════╣
║ SUGGESTIONS (Nice to Have)                                 ║
║   → src/services/user.ts - Consider caching               ║
║   → src/api/routes.ts - Could extract middleware          ║
╠════════════════════════════════════════════════════════════╣
║ POSITIVE                                                   ║
║   ✓ Good test coverage                                     ║
║   ✓ Clear naming conventions                               ║
║   ✓ Proper error messages                                  ║
╠════════════════════════════════════════════════════════════╣
║ LESSONS APPLICABLE                                         ║
║   • Firebase UID ≠ UUID - Check ID handling               ║
║   • Contract tests - Verify FE/BE IDs match               ║
╠════════════════════════════════════════════════════════════╣
║ VERDICT: CHANGES REQUESTED (2 critical issues)            ║
╚════════════════════════════════════════════════════════════╝
```

### Phase 5: Offer Fixes

If issues found:
```
Would you like me to fix the critical and warning issues?
```

## Arguments

$ARGUMENTS

- No args: Review all uncommitted changes
- `--staged`: Only staged changes
- `--file path/to/file`: Specific file
- `--pr 123`: Review PR (requires gh CLI)
