---
name: tdd-cycle
description: "Issue to TDD plan phase for launching a TDD cycle . Enforces test-driven development with 80%+ coverage from an `issue.md` or `*.plan.md` implementation .Use this skill when starting a new feature or fix development in a TDD cycle.This skill takes an issue and produces a TDD plan. Do NOT interview the user — just synthesize what you already know."
---

# When to use this skill

- Starting a new feature or fix development cycle
- Defining requirements for AI-powered features
- Create a TDD plan

# Constraints

1. **DO** strictly follow the **TDD cycle protocol**
1. **DO** strictly follow the excution guidelines for red->green->refactor
1. **DO** return control to the user **ONLY when ALL** of the following are **true**:
   - Every task in your todo list is marked completed
   - Every red phase test has been passed to green by a separate green phase
   - a final refactor phase has been passed
   - Enforce tests-before-code methodology

If any of these conditions are not met, keep going.

4. **DO NOT** use any tool that `manage_todo_list` to track progress

## Test Quality Standards

Refer to `test-camm-bac.instructions.md` for comprehensive guidance on:

- Testing Strategy
- Test Structure
- Effective Mocking
- Testing Best Practices
- Test Execution
- Error Testing

## The TDD cycle Protocol

RUG Loop Protocol = **Repeat Until Good** is a loop comprising the required steps :

1. DECOMPOSE the user's request into discrete, independently-completable tasks
2. CREATE a todo list tracking every cycle
3. For each cycle verify the **Checklist per cycle** :
   1. Mark it in-progress
   2. **red phase** write/update a failing test for new & correct expected behavior.
   3. run test -- Verify it FAILS

   4. **green phase** write minimal code to pass.
   5. run test -- Verify it PASSES
   6. **refactor phase** clean up duplication, improve naming, and enhance structure. Tests must stay green.

4. launch a final **tdd-reviewer agent** with a fresh context in order to mark the End-of-Phase TDD Review by a checkpoint
   STOP WHEN: 8 iterations reached
   ON STOP: summarize what changed and what still fails
   Never **refactor** while **red** Get to **green** first.

Rules:

- One test at a time
- Only enough code to pass current test
- Don't anticipate future tests
- Keep tests focused on observable behavior

### Error Handling

1. Test doesn't fail in RED phase:
   - Feature may already exist - investigate
   - Test may be wrong (not testing what you think)
   - Fix before proceeding

2. Test doesn't pass in GREEN phase:
   - Debug implementation
   - Don't skip to refactor
   - Keep iterating until green
   - Tests fail in REFACTOR phase:

3. Undo refactor
   - Commit was premature
   - Refactor in smaller steps
   - Unrelated tests break:

4. Stop and investigate
   - May indicate coupling issue
   - Fix before proceeding

### Checklist Per Cycle

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
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

# Red phase execution guidelines

1. **Analyse requirements** - Break down issue into testable behaviours
2. **Write the simplest failing test** - Start with the most basic scenario from issue. NEVER write multiple tests at once. You will iterate on RED, GREEN, REFACTOR cycle with one test at a time
3. **Verify the test fails** - Run the test to confirm it fails for the expected reason
4. **Link test to issue** - Reference issue number in test names and comments

## Red Phase Checklist

- [ ] Gitlab issue context retrieved and analysed
- [ ] Test clearly describes expected behaviour from issue requirements
- [ ] Test fails for the right reason (missing implementation)
- [ ] Test name references issue number and describes behaviour
- [ ] Test follows AAA pattern
- [ ] Edge cases from issue discussion considered
- [ ] No production code written yet

- [ ] All public functions have unit tests
- [ ] All API endpoints have integration tests
- [ ] Error paths tested (not just happy path)
- [ ] Mocks used for external dependencies
- [ ] Tests are independent (no shared state)

# Green Phase Execution Guidelines

1. **Review TDD requirements** - Confirm implementation aligns acceptance criteria
2. **Run the failing test** - Confirm exactly what needs to be implemented

3. **Write minimal code** - Add just enough to satisfy issue requirements and make test pass
4. **Run all tests** - Ensure new code doesn't break existing functionality
5. **Update issue progress** - Comment on implementation status if needed

## Green Phase Checklist

- [ ] Implementation aligns with Gitlab issue requirements
- [ ] All tests are passing (green bar)
- [ ] No more code written than necessary for issue scope
- [ ] Existing tests remain unbroken
- [ ] Implementation is simple and direct
- [ ] Issue acceptance criteria satisfied
- [ ] Ready for refactoring phase

---

## Constraints

- **DO NOT** Do not write/update tests, only implementation.
- **DO NOT** implement features or fix not mentioned in the current issue. Stay in scope

# Refactor Phase Execution Guidelines

1. **Review issue completion** - Ensure issue acceptance criteria are fully met
2. **Ensure green tests** - All tests must pass before refactoring
3. **Small incremental changes** - Refactor in tiny steps, running tests frequently
4. **Apply one improvement at a time** - Focus on single refactoring technique
5. **Document security decisions** - Add comments for security-critical code
6. **Update issue** - Comment on final implementation and close issue if complete

## Refactor Phase Checklist

- [ ] GitLab issue acceptance criteria fully satisfied
- [ ] Code duplication eliminated
- [ ] Names clearly express intent aligned with issue domain
- [ ] Methods have single responsibility
- [ ] Security vulnerabilities addressed per issue requirements
- [ ] Performance considerations applied
- [ ] All tests remain green
- [ ] Code coverage maintained or improved
- [ ] Issue marked as complete or follow-up issues created
- [ ] Documentation updated as specified in issue

## Common TDD Failure Modes with AI (AVOID THESE)

**Missing test coverage for features**: TDD cycle focus on making existing tests pass and won't implement features that don't have corresponding tests. Ensure every requirement in your specification has test coverage before expecting the implementation to include it.

**Skipping the red phase**: AI might suggest implementing code before writing tests.

**Over-implementation**: AI might generate more code than needed to pass the current test. Review implementations critically and remove unnecessary complexity.

**Testing implementation details**: Tests should verify behavior, not implementation. If refactoring requires changing tests, they might be too tightly coupled to implementation details.

**Incomplete test coverage**: AI might miss edge cases or error conditions. Review generated tests critically and ask for additional tests covering boundary conditions, error scenarios, and edge cases.

## Best practices for TDD with AI

**Validate test quality**: After AI generates a test, review it to ensure it fails for the right reason. Run the test before implementing to verify it catches the missing functionality.

**Maintain incremental progress**: Take small steps through the TDD cycle. Write one test, implement minimal code, refactor, then repeat. Small iterations prevent large mistakes and keep the codebase working.

**Run tests frequently**: Execute tests immediately after changes. Don't accumulate multiple changes before testing. Frequent test runs provide rapid feedback and catch issues early.

**Use test coverage as a guide**: High coverage doesn't guarantee quality, but low coverage indicates untested behavior. Ask AI to suggest tests for uncovered code paths.
