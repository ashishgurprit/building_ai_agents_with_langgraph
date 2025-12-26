---
description: Capture lessons learned after debugging
allowed-tools: ["Read", "Write", "Bash"]
---

# Post-Mortem: Capture Lessons

Analyze the debugging session and capture lessons for future reference.

## When to Use

- After fixing a production issue
- After debugging a tricky problem
- When you discover a non-obvious gotcha
- After any "aha" moment

## Instructions

### Phase 1: Identify the Issue

Review the recent conversation/work:

1. **What was the symptom?** (Error message, unexpected behavior)
2. **What was the expected behavior?**
3. **How long did it take to debug?**

### Phase 2: Find Root Cause

1. **Why did it happen?** (The real cause, not symptoms)
2. **Why wasn't it caught earlier?** (Missing test? Bad assumption?)

### Phase 3: Document Solution

1. **How was it fixed?**
2. **Is there a code pattern to follow?**
3. **Is there a code pattern to avoid?**

### Phase 4: Create Prevention Measures

1. **What test would catch this?**
2. **What validation would prevent this?**
3. **What checklist item would help?**

### Phase 5: Categorize

Choose the most relevant category:
- Authentication & Identity
- Database & Data Types
- API Contracts
- Environment & Configuration
- Deployment & Infrastructure
- Payment Integration
- Testing Strategies
- Performance
- Security

### Phase 6: Write Lesson

Add to `.claude/LESSONS-PROJECT.md`:

```markdown
### LESSON: [Short Descriptive Title]
**Date**: [today's date]

**Symptom**: [What you observed]

**Root Cause**: [Why it happened]

**Solution**:
```[code if applicable]```

**Prevention**:
- [ ] [Test to add]
- [ ] [Validation to add]
- [ ] [Checklist item]
```

### Phase 7: Offer to Contribute

If this lesson applies beyond this project:

```
This lesson might help other projects too.
Contribute to master? (y/n)

If yes, run: ~/streamlined-development/scripts/contribute-lesson.sh
```

## Arguments

$ARGUMENTS

If provided, use as context for what issue to analyze.
If empty, analyze the most recent debugging in this conversation.

## Output

```
╔════════════════════════════════════════════════════════════╗
║                  LESSON CAPTURED                           ║
╠════════════════════════════════════════════════════════════╣
║ Title:    [lesson title]                                   ║
║ Category: [category]                                       ║
║ Saved to: .claude/LESSONS-PROJECT.md                       ║
╠════════════════════════════════════════════════════════════╣
║ Prevention measures:                                       ║
║   • [Test to add]                                          ║
║   • [Validation to add]                                    ║
╠════════════════════════════════════════════════════════════╣
║ Contribute to master for all projects?                     ║
║ Run: ~/streamlined-development/scripts/contribute-lesson.sh║
╚════════════════════════════════════════════════════════════╝
```
