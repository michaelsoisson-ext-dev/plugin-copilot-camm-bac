# Common TDD Failure Modes with AI (AVOID THESE)

**Missing test coverage for features**: TDD cycle focus on making existing tests pass and won't implement features that don't have corresponding tests. Ensure every requirement in your specification has test coverage before expecting the implementation to include it.

**Skipping the red phase**: AI might suggest implementing code before writing tests.

**Over-implementation**: AI might generate more code than needed to pass the current test. Review implementations critically and remove unnecessary complexity.

**Testing implementation details**: Tests should verify behavior, not implementation. If refactoring requires changing tests, they might be too tightly coupled to implementation details.

**Incomplete test coverage**: AI might miss edge cases or error conditions. Review generated tests critically and ask for additional tests covering boundary conditions, error scenarios, and edge cases.

# Best practices for TDD with AI

**Validate test quality**: After AI generates a test, review it to ensure it fails for the right reason. Run the test before implementing to verify it catches the missing functionality.

**Maintain incremental progress**: Take small steps through the TDD cycle. Write one test, implement minimal code, refactor, then repeat. Small iterations prevent large mistakes and keep the codebase working.

**Run tests frequently**: Execute tests immediately after changes. Don't accumulate multiple changes before testing. Frequent test runs provide rapid feedback and catch issues early.

**Use test coverage as a guide**: High coverage doesn't guarantee quality, but low coverage indicates untested behavior. Ask AI to suggest tests for uncovered code paths.
