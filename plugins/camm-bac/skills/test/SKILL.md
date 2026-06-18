---
name: test
description: Generate comprehensive unit tests for JavaScript code using Jest, covering happy paths, edge cases, and error conditions. Use this skill whenever the user asks to write tests, create test cases, improve test coverage, test a function or API endpoint, write integration tests, test error handling, or assess code testability. Always use this skill for test generation requests, test strategy questions, or code that needs test coverage verification.
---

# JavaScript Testing with Jest

## When to Use This Skill

- Writing unit tests for functions, services, or modules
- Creating integration or e2e tests for API endpoints
- Testing error handling and edge cases
- Improving code coverage or test coverage assessment
- Writing tests following project coding standards and conventions
- Adopting a QA engineer mindset to verify behavior comprehensively

## What to Ask For

1. **What to test**: Function/service name ("user creation endpoint", "email validation", "job handler")
2. **Test type**: Unit test, integration test, or e2e test
3. **Scenarios**: Happy path, error cases, edge cases, boundary conditions

## Project Context

- **Architecture & Conventions**: Always read `.github/copilot-instructions.md` first to understand the project's architecture and test commands
- **Testing Standards**: Refer to `references/testing-standards.md` for comprehensive guidance on:
  - Test structure and organization
  - Naming conventions and best practices
  - Mocking and stubbing patterns
  - Async testing techniques
  - Test data management
  - Error testing strategies

## Quick Guidelines

- **Framework**: Use Jest for all tests
- **Coverage**: Aim for 80%+ on critical paths, test both success and error scenarios
- **Structure**: Mirror directory structure, use nested `describe` blocks, keep tests focused
- **Naming**: Use descriptive names: `should [action] when [condition]`
- **Behavior**: Verify meaningful behavior, not implementation details
- **Setup**: Use `beforeEach()` for common setup, `afterEach()` for cleanup and mock reset

## Test Types

### Unit Tests
- Test individual functions/classes in isolation
- Mock external dependencies (database, APIs, file system)
- Verify success and error paths
- Run quickly without external resources

### Integration Tests
- Test API endpoints with HTTP requests
- Use test database fixtures or in-memory database
- Verify request/response contracts
- Test realistic workflows

### Job Queue Tests
- Test BullMQ job handlers with mock data
- Verify retry logic and failure handling
- Test data validation
- Mock queue operations

## Getting Started

1. **Identify what to test** (function, service, endpoint, handler)
2. **Choose test type** (unit, integration, or job queue test)
3. **Check** `references/testing-standards.md` for detailed patterns and examples
4. **Write tests** following the structure: Arrange → Act → Assert
5. **Run tests**: `npm test` or `npm run test:watch`
6. **Verify coverage**: `npm run test:coverage`

For detailed guidance on test structure, naming conventions, mocking strategies, and best practices, see `references/testing-standards.md`.
