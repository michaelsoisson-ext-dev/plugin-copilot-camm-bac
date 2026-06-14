---
applyTo: "**/*.test.js,**/*.spec.js,tests/**"
description: "Testing standards or writing JavaScript/TypeScript tests using Jest, including mocking strategies, test structure, and common patterns."
---

# Testing Standards

Apply the repository-wide guidance from `./github/copilot-instructions.md` to all tests.

## Testing Strategy

- Write unit tests for all business logic (services, utilities)
- Write integration tests for API endpoints
- Write tests for BullMQ job handlers
- Test error scenarios, edge cases, and boundary conditions
- Maintain test coverage of 80% or higher
- Use descriptive test names that explain what is being tested

### Unit Tests

- Test individual functions and classes in isolation
- Mock external dependencies (database, external APIs)
- Test with multiple input scenarios (valid, invalid, edge cases)
- Verify both success and error paths

### Integration Tests

- Test API endpoints with actual HTTP requests
- Use test database fixtures or in-memory database
- Test request/response contract validation
- Verify error responses with correct status codes

### Job Queue Tests

- Test job handlers with mock data
- Verify job retry logic and failure handling
- Test job data validation
- Mock queue operations

## Test Structure

- Name test files with `.test.ts` or `.test.js` suffix
- Place test files next to the code they test or in a dedicated `__tests__` directory
- Use descriptive test names that explain the expected behavior
- Use nested describe blocks to organize related tests
- Follow the pattern: `describe('Component/Function/Class', () => { it('should do something', () => {}) })`

```
__tests__/
├── unit/           # Mocked isolated unit tests
├── integration/    # Tests with external dependencies
└── helper/         # Test environment setup assistance
```

### Framework

- use [Jest](https://jestjs.io/docs/getting-started) with Node.js and ESM modules (.mjs)
- Write tests `*.spec.js` with jest for all new features directory

### Testing Async Code

- Always return promises or use async/await syntax in tests
- Use `resolves`/`rejects` matchers for promises
- Set appropriate timeouts for slow tests with `jest.setTimeout()`

```javascript
test("the data is peanut butter", async () => {
  const data = await fetchData();
  expect(data).toBe("peanut butter");
});

test("the fetch fails with an error", async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (error) {
    expect(error).toMatch("error");
  }
});
```

### Nomenclature

```javascript
// Nomenclature
describe("MigrationWorker", () => {
  describe("processBatch", () => {
    it("should migrate all emails in batch", async () => {});
    it("should handle partial failures", async () => {});
    it("should retry failed items", async () => {});
  });
});
```

### Effective Mocking

- Mock external dependencies (APIs, databases, etc.) to isolate your tests
- Use `jest.mock()` for module-level mocks
- Use `jest.spyOn()` for specific function mocks
- Use `mockImplementation()` or `mockReturnValue()` to define mock behavior
- Reset mocks between tests with `jest.resetAllMocks()` in `afterEach`

## Testing Best Practices

### Test Naming

- Use descriptive test names: `should return 200 when user exists`
- Avoid generic names: DON'T use "test 1", "works", "test case"
- Follow: `[action] [condition] [expected result]` pattern
- Example: `should validate email format and reject invalid addresses`

### Test Organization

- One test file per source file (mirror directory structure)
- Group related tests with `describe()` blocks
- Use `beforeEach()` for common setup, `afterEach()` for cleanup
- Keep tests focused and independent

### Assertions & Verification

- Use clear assertions with meaningful messages
- Test behavior, not implementation
- Verify both happy path and error scenarios
- Check important state changes and side effects

### Mocking & Stubbing

- Mock external dependencies: database, APIs, file system
- Use consistent mocking patterns across tests
- Mock at the module boundary, not within implementations
- Clear mock setup in `beforeEach()` to avoid test pollution

### Test Data

- Use realistic test data
- Create fixtures for common test scenarios
- Use factories for complex object creation
- Document test data assumptions

## Test Execution

- Run tests before committing: `npm test`
- Use `npm run test:watch` for development
- Generate coverage reports: `npm run test:coverage`
- Integration tests should be separate and clearly marked
- Performance tests for critical paths (database queries, job processing)

## Error Testing

- Test validation errors return 400 status code
- Test not found errors return 404 status code
- Test authorization errors return 403 status code
- Test server errors return 500 status code
- Verify error messages are meaningful but don't expose internals
- Test error handling in job queue failure scenarios
