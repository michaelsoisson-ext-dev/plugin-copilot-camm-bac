---
applyTo: "**/*.test.js,**/*.spec.js,tests/**"
description: "Testing standards and practices for Node.js backend"
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

## Test Structure

```
__tests__/
├── unit/           # Mocked isolated unit tests
├── integration/    # Tests with external dependencies
└── helper/         # Test environment setup assistance
```

### Framework

- use [Jest](https://jestjs.io/docs/getting-started) with Node.js and ESM modules (.mjs)
- Write tests `*.spec.js` with jest for all new features directory

### Testing Asynchronous Code - Async/Await

Alternatively, you can use async and await in your tests. To write an async test, use the async keyword in front of the function passed to test. For example, the same fetchData scenario can be tested with:

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

### Truthiness

In tests, you sometimes need to distinguish between undefined, null, and false, but you sometimes do not want to treat these differently. Jest contains helpers that let you be explicit about what you want.

- toBeNull matches only null
- toBeUndefined matches only undefined
- toBeDefined is the opposite of toBeUndefined
- toBeTruthy matches anything that an if statement treats as true
- toBeFalsy matches anything that an if statement treats as false

For example:

```javascript
test("null", () => {
  const n = null;
  expect(n).toBeNull();
  expect(n).toBeDefined();
  expect(n).not.toBeUndefined();
  expect(n).not.toBeTruthy();
  expect(n).toBeFalsy();
});

test("zero", () => {
  const z = 0;
  expect(z).not.toBeNull();
  expect(z).toBeDefined();
  expect(z).not.toBeUndefined();
  expect(z).not.toBeTruthy();
  expect(z).toBeFalsy();
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

### Mocks

```javascript
jest.mock("./lib/bac/api", () => ({
  getAccount: jest.fn(),
  listEmails: jest.fn(),
}));
```

### Setup/Teardown

```javascript
beforeEach(async () => {
  await db.migrate.latest();
  await db.seed.run();
});

afterEach(async () => {
  await db.migrate.rollback();
  jest.clearAllMocks();
});
```

### Exemples

```javascript
describe("MigrationWorker2", () => {
  let worker;
  let mockQueue;

  beforeEach(() => {
    mockQueue = {
      add: jest.fn(),
      process: jest.fn(),
    };
    worker = new MigrationWorker(mockQueue);
  });

  afterEach(() => {
    jest.clearAllMocks();
  });

  describe("processBatch", () => {
    it("should process all jobs in batch", async () => {
      const batch = [{ id: 1 }, { id: 2 }];
      await worker.processBatch(batch);

      expect(mockQueue.add).toHaveBeenCalledTimes(2);
    });

    it("should handle partial failures gracefully", async () => {
      mockQueue.add.mockRejectedValueOnce(new Error("Queue full"));

      const batch = [{ id: 1 }, { id: 2 }];
      const result = await worker.processBatch(batch);

      expect(result.failed).toHaveLength(1);
      expect(result.succeeded).toHaveLength(1);
    });
  });
});
```

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

## Tools & Framework

- **Test Runner**: Jest or Mocha
- **Assertions**: Expect library or similar
- **Mocking**: Sinon, jest.mock, or similar
- **HTTP Testing**: Supertest for API endpoints
- **Database Testing**: Use test database or in-memory alternatives

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
