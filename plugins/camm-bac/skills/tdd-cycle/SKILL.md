---
name: tdd-cycle
description: 'Start a TDD cycle from a bdd/tdd plan. Enforces test-driven development with 80%+ coverage taking user input data ( bdd/tdd plan). Test-driven development with red-green-refactor loop. Use this skill when user wants to build features or fix bugs using TDD, mentions "red-green-refactor", wants integration tests or asks for test-first development.'
---

# Constraints

1. **DO** strictly follow the [**TDD cycle protocol**](#tdd-cycle-protocol)
2. **DO** strictly follow the [phases excution guidelines for red->green->refactor](references/phases-guidelines.md)
3. **DO** return control to the user **ONLY when ALL** of the following are **true**:
    - Every task in your todo list is marked completed
    - Every red phase test has been passed to green by a separate green phase
    - a final refactor phase has been passed
    - Enforce tests-before-code methodology

If any of these conditions are not met, keep going.

4. **DO NOT** use any tool that `manage_todo_list` to track progress

## Test Quality Standards

follow the [principles](references/principles.md) and Refer to [test-execution.md](references/test-executions.md) for comprehensive guidance on:

- [Testing Strategy](references/test-executions.md#test-strategy)
- [Test Structure](references/test-executions.md#test-structure)
- [Effective Mocking](references/test-executions.md#effective-mocking)
- [Test Execution](references/test-executions.md#test-execution)

test execution : `bash env-cmd jest  ./__tests__/ox/ox-710.spec.js `

## TDD cycle Protocol

1. DECOMPOSE the input BDD/TDD plan or the user's request into discrete, independently-completable tasks
2. CREATE a todo list tracking every cycle
3. For each cycle verify the [Checklist per cycle](#checklist-per-cycle) :
    1. Mark it in-progress
    2. **red phase** write/update a failing test for new & correct expected behavior.
    3. verify red -- run test -- Verify it FAILS
    4. create a [Git checkpoint](#git-checkpoints) commit immediately with pattern `test: <basic scenario> for <feature or bug>`
    5. **green phase** write minimal code to pass.
    6. create a [Git checkpoint](#git-checkpoints) commit immediately with pattern `fix: <bug>` or `feat: <feature>`
    7. verify green -- run test -- Verify it PASSES
    8. **refactor phase** clean up duplication, improve naming, and enhance structure. Tests must stay green.
    9. create a [Git checkpoint](#git-checkpoints) commit immediately with pattern `refactor: clean up after <feature or bug> implementation`
    10. next cycle

4. STOP WHEN: TDD cycle end or 8 verify green phase in same cycle reached
   ON STOP: summarize what changed and what still fails
   Never **refactor** while **red** Get to **green** first.

## Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Use descriptive test names that explain what is being tested and the expected outcome
[ ] Keep tests independent - each test should run in isolation without depending on other tests
[ ] Create a checkpoint git commit after each TDD stage
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] One test a time
[ ] Code is minimal for this test
[ ] No speculative features added
```

## Progress Tracking

Use `manage_todo_list` and `git commit` like a checkpoint obsessively:

- Create the full task list BEFORE launching the cycle
- Mark tasks in-progress as you launch a phase
- Mark tasks complete only AFTER validation passes
- Create a [Git checkpoint](#git-checkpoints) commit after each TDD stage
- Add new tasks if a phase discover additional work needed

This is your memory. Your context window will fill up. The todo list keeps you oriented.
save this todo list in file named `.ai-camm-bac./plans/todo_list_<unique-name>.md`

### Git Checkpoints

- Create a checkpoint commit after each TDD stage
- Do not squash or rewrite these checkpoint commits until the workflow is complete
- Each checkpoint commit message must describe the stage and the exact evidence captured
- Count only commits created on the current active branch for the current task
- Do not treat commits from other branches, earlier unrelated work, or distant branch history as valid checkpoint evidence
- Before treating a checkpoint as satisfied, verify that the commit is reachable from the current `HEAD` on the active branch and belongs to the current task sequence
- The preferred compact workflow is:
    - one commit for failing test added and RED validated
    - one commit for minimal fix applied and GREEN validated
    - one optional commit for refactor complete
- Separate evidence-only commits are not required if the test commit clearly corresponds to RED and the fix commit clearly corresponds to GREEN
- Squash merges are allowed only after the workflow evidence has been preserved . If checkpoint commits will be squashed, copy the RED/GREEN/refactor summary into the MR body, squash commit body, or evidence report so reviewers can still answer what was verified and how.
