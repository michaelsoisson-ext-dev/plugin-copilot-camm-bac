# Red phase execution guidelines

1. **Analyse requirements** - Break down issue into testable behaviours
2. **Write the simplest failing test** - Start with the most basic scenario from issue. NEVER write multiple tests at once. You will iterate on RED, GREEN, REFACTOR cycle with one test at a time
3. **Verify the test fails** - Run the test to confirm it fails for the expected reason
4. **Link test to issue** - Reference issue number in test names and comments

## Constraints

- **DO NOT** Do not implements the tests just write it

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

---

# Green Phase Execution Guidelines

1. **Review TDD requirements** - Confirm implementation aligns acceptance criteria
2. **Run the failing test** - Confirm exactly what needs to be implemented

3. **Write minimal code** - Add just enough to satisfy issue requirements and make test pass
4. **Run all tests** - Ensure new code doesn't break existing functionality
5. **Update issue progress** - Comment on implementation status if needed

## Constraints

- **DO NOT** Do not write/update tests, only implementation.
- **DO NOT** implement features or fix not mentioned in the current issue. Stay in scope

## Green Phase Checklist

- [ ] Implementation aligns with issue requirements
- [ ] All tests are passing (green bar)
- [ ] No more code written than necessary for issue scope
- [ ] Existing tests remain unbroken
- [ ] Implementation is simple and direct
- [ ] Issue acceptance criteria satisfied
- [ ] Ready for refactoring phase

---

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

# Error Handling

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
