---
description: TDD implementation (tests first, then code)
allowed-tools: ["Read", "Write", "Bash", "Task"]
---

# Test-Driven Implementation

Implement features using TDD: write tests first, then make them pass.

## The TDD Loop

```
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│  WRITE   │──▶│   RUN    │──▶│ CONFIRM  │──▶│  COMMIT  │
│  TESTS   │   │  TESTS   │   │   FAIL   │   │  TESTS   │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
                                    │
                                    ▼
┌──────────┐   ┌──────────┐   ┌──────────┐
│  COMMIT  │◀──│   RUN    │◀──│IMPLEMENT │
│   CODE   │   │  TESTS   │   │   CODE   │
└──────────┘   └──────────┘   └──────────┘
                    ▲              │
                    └──────────────┘
                    (iterate until pass)
```

## Instructions

### Phase 1: Load Plan

If `plan.md` exists, read it for:
- What to implement
- Which phase we're on
- Files to modify
- Tests to write

If no plan: Ask what to implement or suggest running `/project:plan` first.

### Phase 2: Write Tests First

**CRITICAL**: We are doing TDD. Write tests BEFORE implementation.

1. Create test file(s) for the feature
2. Write tests based on expected behavior
3. Use REAL input/output examples
4. **DO NOT** create mock implementations

```
# Example test structure
def test_feature_success_case():
    # Arrange
    input_data = {...}
    expected_output = {...}

    # Act
    result = feature_function(input_data)

    # Assert
    assert result == expected_output

def test_feature_error_handling():
    # Test error cases too
```

### Phase 3: Confirm Tests Fail

Run the test suite:

```bash
# Python
pytest tests/test_new_feature.py -v

# JavaScript
npm test -- --testPathPattern=new-feature
```

**Verify tests fail for the right reason** (not implemented, not syntax error).

### Phase 4: Commit Tests

```bash
git add tests/
git commit -m "test: add tests for [feature]"
```

### Phase 5: Implement

Now write the implementation code:
1. Focus on making tests pass
2. Don't over-engineer
3. Follow existing patterns (from /project:plan research)

### Phase 6: Run Tests Until Pass

```bash
# Run tests
pytest tests/test_new_feature.py -v

# If fail: fix and repeat
# If pass: continue
```

### Phase 7: Commit Implementation

```bash
git add src/
git commit -m "feat: implement [feature]"
```

### Phase 8: Full Test Suite

Run ALL tests to ensure no regressions:

```bash
pytest  # or npm test
```

If failures: fix before proceeding.

## Arguments

$ARGUMENTS

- Feature name or description
- If matches a phase in plan.md, implement that phase

## Output

```
╔════════════════════════════════════════════════════════════╗
║                 IMPLEMENTATION COMPLETE                    ║
╠════════════════════════════════════════════════════════════╣
║ Feature: [name]                                            ║
║ Tests:   [X] written, [X] passing                          ║
║ Files:   [list of modified files]                          ║
║ Commits: 2 (tests + implementation)                        ║
╠════════════════════════════════════════════════════════════╣
║ Next: /project:pre-deploy or continue to next phase        ║
╚════════════════════════════════════════════════════════════╝
```
