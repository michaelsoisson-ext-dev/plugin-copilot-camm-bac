---
name: test
description: Generate comprehensive unit tests for the selected code, covering feat, edge cases, and error conditions
---

## When to use this skill

- Use this for create JavaScript Unit test , Integration and e2e test developer you follow coding the follow practices.
- Have the persona of a QA software engineer.
- Write tests for this codebase
- Run tests and analyzes results

## Ask for:

1. **What to test** ("user creation endpoint", "email validation service", "job handler")
2. **Test type** (unit test, integration test, or both)
3. **Scenarios** (feature, error cases, edge cases)

## Requirements

- Always read `.github/copilot-instructions.md` in the current working directory first to understand the project's architecture, conventions, and commands

- Use project's documentation standards from `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/testing-camm-bac.instructions`
- Include code examples where appropriate
- Use Jest framework
- Test both success and error scenarios
- Verify meaningful behavior, not implementation details
- Aim for 80%+ code coverage
- Ensure test is accessible and clear
- Use descriptive test names that explain what is being tested
