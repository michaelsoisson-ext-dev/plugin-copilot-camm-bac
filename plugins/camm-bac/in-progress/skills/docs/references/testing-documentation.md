# Testing Documentation Standards

Comprehensive guide for documenting testing standards, strategies, and practices in JavaScript/TypeScript projects using Jest.

## Table of Contents
1. [Testing Strategy Documentation](#testing-strategy-documentation)
2. [Test Structure Documentation](#test-structure-documentation)
3. [Test Naming & Organization](#test-naming--organization)
4. [Common Testing Patterns](#common-testing-patterns)
5. [Documentation Examples](#documentation-examples)

---

## Testing Strategy Documentation

### What to Document

When documenting a testing strategy, cover these key areas:

**Unit Testing Strategy**
- Scope: Individual functions and classes tested in isolation
- Dependencies: Mock external dependencies (database, APIs, file system)
- Coverage: Aim for 80%+ code coverage
- Example: "Unit tests focus on business logic in services and utilities with all external dependencies mocked"

**Integration Testing Strategy**
- Scope: API endpoints with actual HTTP requests
- Setup: Use test database fixtures or in-memory alternatives
- Validation: Verify request/response contracts and error responses
- Example: "Integration tests verify the full request/response lifecycle including database interactions"

**Job Queue Testing Strategy**
- Scope: BullMQ job handlers and retry logic
- Setup: Mock queue operations, provide test data
- Validation: Verify error handling and retry scenarios
- Example: "Job queue tests verify handler logic, retry mechanisms, and failure scenarios"

### Testing Levels Template

```markdown
## Testing Levels

### Unit Tests
- **What**: Individual components in isolation with mocked dependencies
- **Where**: `src/**/__tests__/unit/` or alongside source files as `*.test.js`
- **How**: Mock external dependencies, test with multiple input scenarios
- **Coverage**: Aim for high coverage of business logic

### Integration Tests
- **What**: Components working together with real dependencies
- **Where**: `src/**/__tests__/integration/`
- **How**: Use test database, verify HTTP contracts
- **Coverage**: Critical user flows and API endpoints

### End-to-End Tests
- **What**: Full application workflows from user perspective
- **Where**: `e2e/` directory if present
- **How**: Run against deployed test environment
- **Coverage**: Happy paths and critical error scenarios
```

---

## Test Structure Documentation

### Directory Organization Template

Document your project's test structure like this:

```markdown
## Test File Organization

Tests follow a mirrored structure alongside source code:

```
src/
├── services/
│   ├── user-service.js
│   └── __tests__/
│       └── unit/
│           └── user-service.test.js
├── api/
│   ├── routes.js
│   └── __tests__/
│       └── integration/
│           └── routes.test.js
└── jobs/
    ├── handlers.js
    └── __tests__/
        └── unit/
            └── handlers.test.js
```

**Naming conventions:**
- Test files: `.test.js` or `.spec.js` suffix
- Describe blocks: Component/Function/Class name (PascalCase)
- Test cases: Descriptive sentences starting with "should"
```

### Test Framework Documentation

```markdown
## Framework & Setup

**Testing Framework**: Jest with Node.js and ESM modules (.mjs)
**Test Runner**: `npm test`
**Watch Mode**: `npm run test:watch`
**Coverage Report**: `npm run test:coverage`

### Configuration

Tests run using `jest.config.js` with these key settings:
- Module format: ESM (testEnvironment may need adjustment)
- Timeout: 5000ms by default (override with `jest.setTimeout()` for slow tests)
- Coverage threshold: 80% for statements, branches, functions, lines
```

---

## Test Naming & Organization

### Naming Patterns to Document

**Good Test Names Pattern**: `[action] [condition] [expected result]`

```markdown
### Test Naming Examples

❌ **Bad**: `test1`, `works`, `test case`
✅ **Good**: 
- `should return 200 when user exists`
- `should handle partial failures gracefully`
- `should retry failed items with exponential backoff`
- `should validate email format and reject invalid addresses`
- `should create job with metadata when queue is healthy`
```

### Organization with Describe Blocks

```markdown
### Organizing Tests

Use nested `describe()` blocks to create clear hierarchies:

```javascript
describe('MigrationWorker', () => {
  describe('processBatch', () => {
    it('should migrate all emails in batch', async () => {});
    it('should handle partial failures', async () => {});
    it('should retry failed items', async () => {});
  });

  describe('validateBatch', () => {
    it('should reject empty batches', () => {});
    it('should validate all items before processing', () => {});
  });
});
```

This structure makes tests easy to navigate and understand at a glance.
```

---

## Common Testing Patterns

### Async Testing Pattern

```markdown
## Testing Async Code

Always return promises or use `async/await` in tests:

```javascript
// Pattern 1: async/await
test("should fetch and return data", async () => {
  const data = await fetchData();
  expect(data).toBe("expected value");
});

// Pattern 2: resolves matcher
test("should resolve with data", async () => {
  await expect(fetchData()).resolves.toBe("expected value");
});

// Pattern 3: rejects matcher
test("should reject with error", async () => {
  await expect(fetchData()).rejects.toMatch("error");
});

// Pattern 4: try/catch with assertions
test("the fetch fails with an error", async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (error) {
    expect(error).toMatch("error");
  }
});
```

For slow tests, increase timeout:
```javascript
jest.setTimeout(10000); // 10 seconds
```
```

### Mocking Pattern

```markdown
## Effective Mocking Strategy

### Module-Level Mocks
```javascript
jest.mock('../db', () => ({
  find: jest.fn(),
  save: jest.fn(),
}));
```

### Function-Specific Mocks
```javascript
jest.spyOn(database, 'query')
  .mockReturnValue(Promise.resolve([{ id: 1 }]));
```

### Reset Between Tests
```javascript
describe('Service', () => {
  afterEach(() => {
    jest.resetAllMocks();
  });

  it('test 1', () => { /* ... */ });
  it('test 2', () => { /* ... */ });
});
```

Key principles:
- Mock at module boundaries, not within implementations
- Use `mockReturnValue()` for simple returns
- Use `mockImplementation()` for complex behavior
- Reset mocks in `afterEach()` to prevent test pollution
```

### Error Testing Pattern

```markdown
## Testing Error Scenarios

Verify correct HTTP status codes for different error types:

```javascript
describe('API Error Handling', () => {
  it('should return 400 for validation errors', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'invalid' });
    expect(response.status).toBe(400);
  });

  it('should return 404 for not found', async () => {
    const response = await request(app).get('/api/users/999');
    expect(response.status).toBe(404);
  });

  it('should return 403 for unauthorized', async () => {
    const response = await request(app)
      .delete('/api/users/1')
      .set('Authorization', 'invalid');
    expect(response.status).toBe(403);
  });

  it('should return 500 for server errors', async () => {
    jest.spyOn(db, 'query').mockRejectedValue(new Error('DB error'));
    const response = await request(app).get('/api/users');
    expect(response.status).toBe(500);
  });
});
```

Error messages should be meaningful but not expose internal implementation details.
```

---

## Documentation Examples

### Example: Testing README Template

```markdown
# Testing Guide

This project uses [Jest](https://jestjs.io/) for testing with Node.js and ESM modules.

## Running Tests

```bash
npm test                    # Run all tests
npm run test:watch        # Watch mode for development
npm run test:coverage     # Generate coverage report
```

## Test Structure

- **Unit tests**: `src/**/__tests__/unit/` - Isolated tests with mocked dependencies
- **Integration tests**: `src/**/__tests__/integration/` - Tests with real dependencies
- **Helpers**: `src/**/__tests__/helper/` - Test setup and utilities

## Writing Tests

### Naming Convention
- Describe blocks: Component/Function name
- Test names: "should [action] [condition]"

```javascript
describe('UserService', () => {
  describe('findById', () => {
    it('should return user when exists', async () => {
      const user = await service.findById('123');
      expect(user).toBeDefined();
    });

    it('should return null when not found', async () => {
      const user = await service.findById('999');
      expect(user).toBeNull();
    });
  });
});
```

### Mocking Database

```javascript
jest.mock('../db', () => ({
  find: jest.fn(),
  save: jest.fn(),
}));

describe('UserService', () => {
  afterEach(() => jest.resetAllMocks());

  it('should save user data', async () => {
    db.save.mockResolvedValue({ id: '123', name: 'John' });
    const result = await service.create('John');
    expect(db.save).toHaveBeenCalled();
  });
});
```

### Testing Async Code

```javascript
it('should handle errors gracefully', async () => {
  await expect(service.fetchData())
    .rejects.toThrow('Connection failed');
});
```

## Coverage Goals

Maintain 80%+ coverage for:
- Statements
- Branches
- Functions
- Lines

## Best Practices

1. **Test behavior, not implementation** - Focus on what the code does, not how
2. **Keep tests independent** - No test should rely on another test's state
3. **Use fixtures for complex data** - Extract common test data into helpers
4. **Test error paths** - Don't only test the happy path
5. **Mock external dependencies** - Isolate the code under test
```

### Example: API Testing Documentation

```markdown
# API Testing Guide

Integration tests verify your API endpoints work correctly end-to-end.

## Test Database Setup

Each test runs with a clean database using fixtures:

```javascript
const setupTestDb = async () => {
  await db.clear();
  await db.seed({
    users: [{ id: '1', name: 'Test User', email: 'test@example.com' }],
  });
};

beforeEach(setupTestDb);
```

## Testing Endpoints

```javascript
const request = require('supertest');
const app = require('../app');

describe('GET /api/users/:id', () => {
  it('should return 200 with user data when exists', async () => {
    const response = await request(app)
      .get('/api/users/1')
      .expect(200)
      .expect('Content-Type', /json/);
    
    expect(response.body).toEqual({
      id: '1',
      name: 'Test User',
      email: 'test@example.com',
    });
  });

  it('should return 404 when user does not exist', async () => {
    await request(app)
      .get('/api/users/999')
      .expect(404);
  });
});
```

## Request/Response Contracts

Document expected request/response formats for each endpoint:

```markdown
### POST /api/users/batch-migrate

**Request body:**
```json
{
  "batchId": "batch-123",
  "items": [
    { "id": "user-1", "action": "migrate" },
    { "id": "user-2", "action": "migrate" }
  ]
}
```

**Response (200):**
```json
{
  "status": "processing",
  "successCount": 1,
  "failureCount": 1,
  "failures": [
    { "id": "user-2", "reason": "already_migrated" }
  ]
}
```

**Error Responses:**
- `400`: Invalid request body
- `404`: Batch not found
- `500`: Processing error
```
```

---

## Quick Reference Table

| Aspect | Guidelines |
|--------|-----------|
| **Test Files** | `.test.js` or `.spec.js` suffix |
| **Coverage Target** | 80%+ for all metrics |
| **Test Timeout** | 5000ms default, override with `jest.setTimeout()` |
| **Async Pattern** | Use `async/await` or `resolves`/`rejects` matchers |
| **Mocking** | Mock external dependencies, reset in `afterEach()` |
| **Naming** | "should [action] [condition]" |
| **Organization** | Mirror source directory structure |
| **Status Codes** | 400 (validation), 404 (not found), 403 (unauthorized), 500 (server error) |
| **Error Messages** | Meaningful but don't expose internals |

---

## When to Reference This

Use this documentation when:
- Writing test documentation or READMEs for projects
- Creating testing guides for team onboarding
- Documenting testing patterns and best practices
- Explaining Jest setup and configuration
- Showing testing examples to developers
- Creating API testing documentation
- Documenting test coverage goals and strategies
