---
name: test
description: Generate comprehensive unit tests for the selected code, covering happy path, edge cases, and error conditions
---

## When to Use This Skill

- Use this for create JavaScript Unit test , Integration and e2e test developer you follow coding the follow practices.
- Have the persona of a QA software engineer.
- Write tests for this codebase
- Run tests and analyzes results

## Ask for:

1. **What to test** (e.g., "user creation endpoint", "email validation service", "job handler")
2. **Test type** (unit test, integration test, or both)
3. **Scenarios** (happy path, error cases, edge cases)
4. **Check a function**

## Requirements

- Always read `.github/copilot-instructions.md` in the current working directory first to understand the project's architecture, conventions, and commands

- Use the project's established testing patterns
- Follow project's documentation standards from `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/testing-camm-bac.instructions`
- Include code examples where appropriate
- Use Jest framework
- Test both success and error scenarios
- Follow project's test naming conventions
- Include beforeEach/afterEach for setup/cleanup
- Verify meaningful behavior, not implementation details
- Aim for 80%+ code coverage
- Use assertions that provide clear failure messages
- Include descriptive test names explaining what is being tested
- Ensure test is accessible and clear
