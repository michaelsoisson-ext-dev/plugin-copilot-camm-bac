---
name: tdd-cycle
description: 'Start a TDD cycle from a bdd/tdd plan  . Enforces test-driven development with 80%+ coverage from an `issue.md` or `*.plan.md` implementation .Test-driven development with red-green-refactor loop. Use this skill when user wants to build features or fix bugs using TDD, mentions "red-green-refactor", wants integration tests, or asks for test-first development.'
---

# When to use this skill

- the user wants to build features or fix bugs using TDD, mentions "red-green-refactor", wants integration tests, or asks for test-first development.

# Constraints

1. **DO** strictly follow the [**TDD cycle protocol**](#tdd-cycle-protocol)
1. **DO** strictly follow the [phases excution guidelines for red->green->refactor](references/phases-guidelines.md)
1. **DO** return control to the user **ONLY when ALL** of the following are **true**:
    - Every task in your todo list is marked completed
    - Every red phase test has been passed to green by a separate green phase
    - a final refactor phase has been passed
    - Enforce tests-before-code methodology

If any of these conditions are not met, keep going.

4. **DO NOT** use any tool that `manage_todo_list` to track progress

## Test Quality Standards

follow the [principles](references/principles.md) Refer to `test-camm-bac.instructions.md` for comprehensive guidance on:

- Testing Strategy
- Test Structure
- Effective Mocking
- Testing Best Practices
- Test Execution
- Error Testing

## TDD cycle Protocol

1. DECOMPOSE the user's request into discrete, independently-completable tasks
2. CREATE a todo list tracking every cycle
3. For each cycle verify the [Checklist per cycle](#checklist-per-cycle) :
    1. Mark it in-progress
    2. **red phase** write/update a failing test for new & correct expected behavior.
    3. verify red -- run test -- Verify it FAILS
    4. **green phase** write minimal code to pass.
    5. verify green -- run test -- Verify it PASSES
    6. **refactor phase** clean up duplication, improve naming, and enhance structure. Tests must stay green.
    7. next cycle

4. STOP WHEN: TDD cycle end or 8 verify green phase in same cycle reached
   ON STOP: summarize what changed and what still fails
   Never **refactor** while **red** Get to **green** first.

## Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Use descriptive test names that explain what is being tested and the expected outcome
[ ] Keep tests independent - each test should run in isolation without depending on other tests
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] One test a time
[ ] Code is minimal for this test
[ ] No speculative features added
```

## Progress Tracking

Use `manage_todo_list` obsessively:

- Create the full task list BEFORE launching the cycle
- Mark tasks in-progress as you launch a phase
- Mark tasks complete only AFTER validation passes
- Add new tasks if a phase discover additional work needed

This is your memory. Your context window will fill up. The todo list keeps you oriented.
save this todo list in file named `./plan/todo_list_<unique-name>.md`
