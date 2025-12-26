---
description: Auto-fix all test failures
allowed-tools: ["Read", "Write", "Bash", "Task"]
---

# Test Fix Loop

Run tests and automatically fix failures until all pass.

## Instructions

### Phase 1: Run Full Test Suite

```bash
# Detect test framework
if [ -f "pytest.ini" ] || [ -f "pyproject.toml" ]; then
    pytest -v --tb=short
elif [ -f "package.json" ]; then
    npm test
fi
```

### Phase 2: For Each Failure

1. **Parse the error**:
   - Which test failed?
   - What was the error message?
   - What was expected vs actual?

2. **Analyze the cause**:
   - Is it a code bug or test bug?
   - What file needs to change?

3. **Fix the issue**:
   - Make minimal change to fix
   - Don't change test unless test is wrong

4. **Re-run specific test**:
   ```bash
   pytest tests/test_file.py::test_function -v
   ```

5. **If still failing**: Iterate on fix

6. **If passing**: Move to next failure

### Phase 3: Final Verification

After all failures addressed:

```bash
# Run full suite again
pytest -v  # or npm test
```

### Phase 4: Report

```
╔════════════════════════════════════════════════════════════╗
║                    TEST FIX COMPLETE                       ║
╠════════════════════════════════════════════════════════════╣
║ Initial:  12 tests, 3 failures                             ║
║ Fixed:    3 issues                                         ║
║ Final:    12 tests, 0 failures ✓                           ║
╠════════════════════════════════════════════════════════════╣
║ Changes made:                                              ║
║   • src/utils.py: Fixed null handling in parse_id()       ║
║   • src/api.py: Added missing error response              ║
║   • src/db.py: Fixed UUID conversion                      ║
╚════════════════════════════════════════════════════════════╝
```

## Arguments

$ARGUMENTS

- No args: Fix all failures
- Test name/pattern: Focus on specific tests
- `--no-commit`: Don't auto-commit fixes
