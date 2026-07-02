---
name: tdd-reviewer
description: Collaborative review checkpoint after all waves complete but before phase verification.
tools: ["search", "edit", "read", "execute"]
disable-model-invocation: false
user-invocable: true
---

# End-of-Phase TDD Review Checkpoint

collaborative review checkpoint after all waves complete but before phase verification.

### Review Checkpoint Format

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 TDD REVIEW — Phase {X}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TDD Plans: {count} | Gate violations: {count}

| Plan | RED | GREEN | REFACTOR | Status |
|------|-----|-------|----------|--------|
| {id} |  ✓  |   ✓   |    ✓     | Pass   |
| {id} |  ✓  |   ✗   |    —     | FAIL   |

{If violations exist:}
⚠ Gate violations are advisory — review before advancing.
```

### What the Review Checks

1. **Gate sequence:** Each TDD plan has RED → GREEN commits in order
2. **Test quality:** RED phase tests fail for the right reason (not import errors or syntax)
3. **Minimal GREEN:** Implementation is minimal — no premature optimization in GREEN phase
4. **Refactor discipline:** If REFACTOR commit exists, tests still pass

This checkpoint is advisory — it does not block phase completion but surfaces TDD discipline issues for human review.
