# Testing Standards for JavaScript/Node.js

## Table of Contents
1. [Testing Strategy](#testing-strategy)
2. [Test Structure](#test-structure)
3. [Best Practices](#best-practices)
4. [Testing Async Code](#testing-async-code)
5. [Effective Mocking](#effective-mocking)
6. [Test Naming & Organization](#test-naming--organization)
7. [Error Testing](#error-testing)
8. [Test Execution](#test-execution)

## Testing Strategy

Write comprehensive tests covering all three layers:

### Unit Tests
- Test individual functions and classes in isolation
- Mock external dependencies (database, external APIs, file system)
- Test with multiple input scenarios: valid, invalid, edge cases
- Verify both success and error paths
- Run quickly without external dependencies

### Integration Tests
- Test API endpoints with actual HTTP requests
- Use test database fixtures or in-memory database
- Test request/response contract validation
- Verify error responses with correct status codes
- Test realistic workflows involving multiple components

### Job Queue Tests
- Test BullMQ job handlers with mock data
- Verify job retry logic and failure handling
- Test job data validation
- Mock queue operations
- Test timeout and error recovery

### Coverage Requirements
- Maintain test coverage of 80% or higher on critical paths
- Cover error scenarios, edge cases, and boundary conditions
- Focus on behavior coverage, not line coverage
- Document why untested code is excluded

## Test Structure

### File Organization

Name test files with `.test.js` or `.spec.js` suffix and organize by type:

```
project/
├── src/
│   ├── services/
│   │   └── user.service.js
│   └── controllers/
│       └── user.controller.js
├── __tests__/
│   ├── unit/
│   │   ├── services/
│   │   │   └── user.service.test.js
│   │   └── controllers/
│   │       └── user.controller.test.js
│   ├── integration/
│   │   └── api/
│   │       └── user.api.test.js
│   └── helper/
│       ├── fixtures.js
│       ├── db-setup.js
│       └── factories.js
```

### Test Structure Pattern

Use descriptive organization with nested `describe` blocks:

```javascript
describe('UserService', () => {
  describe('createUser', () => {
    beforeEach(() => {
      // Setup common state
    });

    afterEach(() => {
      // Cleanup
      jest.resetAllMocks();
    });

    it('should create user with valid email', async () => {
      // Arrange
      const userData = { email: 'test@example.com', password: 'secure123' };
      
      // Act
      const result = await userService.createUser(userData);
      
      // Assert
      expect(result).toHaveProperty('id');
      expect(result.email).toBe('test@example.com');
    });

    it('should reject invalid email format', async () => {
      const userData = { email: 'invalid', password: 'secure123' };
      
      await expect(userService.createUser(userData))
        .rejects
        .toThrow('Invalid email format');
    });
  });
});
```

### Framework

- Use **Jest** for all tests
- Write tests in `.spec.js` or `.test.js` format
- Use ESM modules (.mjs) with Node.js
- Configure Jest via `jest.config.js` or `package.json`

## Best Practices

### Test Naming

Follow the **[action] [condition] [expected result]** pattern:

✅ **Good test names:**
- `should return 200 when user exists`
- `should validate email format and reject invalid addresses`
- `should handle partial failures in batch migration`
- `should retry failed job with exponential backoff`

❌ **Poor test names:**
- `test 1`, `works`, `test case`
- `endpoint test`, `should work`
- `validates stuff`, `handles error`

### Test Organization

- One test file per source file (mirror directory structure)
- Group related tests with `describe()` blocks (3-4 levels deep max)
- Use `beforeEach()` for common setup, `afterEach()` for cleanup
- Keep tests focused and independent (no test order dependencies)
- Reset mocks between tests to avoid pollution

### Assertions & Verification

```javascript
// ✅ Good: Clear, specific assertions
expect(result.status).toBe(201);
expect(result.data).toHaveProperty('id');
expect(error).toMatch(/invalid email/i);

// ❌ Poor: Vague assertions
expect(result).toBeTruthy();
expect(response).toBeDefined();
```

- Test behavior, not implementation details
- Verify important state changes and side effects
- Check both happy path and error scenarios
- Use meaningful assertion messages

### Mocking & Stubbing

```javascript
// Module-level mock
jest.mock('../lib/database', () => ({
  query: jest.fn()
}));

// Function spy
const querySpy = jest.spyOn(db, 'query');

// Return value mock
jest.mocked(userRepository.findById).mockResolvedValue({
  id: '123',
  email: 'test@example.com'
});

// Reset after each test
afterEach(() => {
  jest.resetAllMocks();
});
```

**Mocking Rules:**
- Mock external dependencies: database, APIs, file system, external services
- Use consistent mocking patterns across tests
- Mock at the module boundary, not within implementations
- Clear mock setup in `beforeEach()` to avoid test pollution
- Verify mock calls when testing integration points

### Test Data

```javascript
// Use realistic test data
const userData = {
  email: 'user@example.com',
  username: 'john_doe',
  status: 'active'
};

// Create fixtures for common scenarios
export const fixtures = {
  validUser: { email: 'valid@example.com', password: 'Pass123!' },
  invalidUser: { email: 'bad', password: '123' },
  premiumUser: { email: 'premium@example.com', plan: 'pro' }
};

// Use factories for complex objects
class UserFactory {
  static create(overrides = {}) {
    return {
      id: randomId(),
      email: 'user@example.com',
      ...overrides
    };
  }
}
```

**Data Best Practices:**
- Use realistic test data that represents actual usage
- Create fixtures for common test scenarios
- Use factories for complex object creation
- Document test data assumptions
- Keep test data DRY (don't repeat)

## Testing Async Code

### Promises

```javascript
// Using resolves/rejects matchers
test('should fetch data successfully', async () => {
  expect.assertions(1);
  const data = await fetchData();
  expect(data).toBe('expected value');
});

test('should handle fetch errors', async () => {
  expect.assertions(1);
  await expect(fetchData()).rejects.toThrow('Network error');
});
```

### Async/Await

```javascript
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  try {
    await fetchData();
    fail('Should have thrown');
  } catch (error) {
    expect(error).toMatch('error');
  }
});
```

### Timeouts

```javascript
// Set appropriate timeouts for slow tests
jest.setTimeout(10000); // 10 seconds for this test file

test('slow database operation', async () => {
  jest.setTimeout(15000); // 15 seconds for this test
  const result = await slowDatabaseQuery();
  expect(result).toBeDefined();
});
```

## Effective Mocking

### Mock External Dependencies

```javascript
// Mock database
jest.mock('../lib/database');
const db = require('../lib/database');
db.query.mockResolvedValue({ rows: [] });

// Mock external API
jest.mock('axios');
const axios = require('axios');
axios.get.mockResolvedValue({ data: {} });

// Mock file system
jest.mock('fs');
const fs = require('fs');
fs.readFileSync.mockReturnValue('file contents');
```

### Verify Mock Interactions

```javascript
// Verify mock was called
expect(db.query).toHaveBeenCalled();

// Verify mock called with specific arguments
expect(userRepository.save).toHaveBeenCalledWith(
  expect.objectContaining({ email: 'test@example.com' })
);

// Verify call count
expect(api.fetch).toHaveBeenCalledTimes(1);

// Get call arguments
const calls = db.query.mock.calls;
expect(calls[0][0]).toBe('SELECT * FROM users');
```

## Test Naming & Organization

### Nomenclature Examples

```javascript
// Services
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with valid email', async () => {});
    it('should reject duplicate email', async () => {});
    it('should hash password before storage', async () => {});
  });

  describe('findById', () => {
    it('should return user when exists', async () => {});
    it('should throw error when not found', async () => {});
  });
});

// Controllers
describe('UserController', () => {
  describe('POST /users', () => {
    it('should return 201 when user created', async () => {});
    it('should return 400 when validation fails', async () => {});
  });
});

// Utilities
describe('emailValidator', () => {
  it('should accept valid email addresses', () => {});
  it('should reject invalid email addresses', () => {});
});
```

## Error Testing

### HTTP Status Codes

```javascript
test('should return 400 for validation errors', async () => {
  const response = await request(app)
    .post('/users')
    .send({ email: 'invalid' });
  
  expect(response.status).toBe(400);
});

test('should return 404 when resource not found', async () => {
  const response = await request(app)
    .get('/users/nonexistent');
  
  expect(response.status).toBe(404);
});

test('should return 403 for authorization errors', async () => {
  const response = await request(app)
    .delete('/users/123')
    .set('Authorization', 'invalid-token');
  
  expect(response.status).toBe(403);
});

test('should return 500 for server errors', async () => {
  db.query.mockRejectedValue(new Error('DB error'));
  
  const response = await request(app)
    .get('/users');
  
  expect(response.status).toBe(500);
});
```

### Error Messages

```javascript
test('should provide meaningful error messages', async () => {
  try {
    await userService.createUser({ email: 'invalid' });
    fail('Should have thrown');
  } catch (error) {
    // Verify error is meaningful but doesn't expose internals
    expect(error.message).toBe('Invalid email format');
    expect(error.message).not.toContain('SQL');
    expect(error.message).not.toContain('database');
  }
});

test('should handle job queue failures gracefully', async () => {
  queue.mockRejectedValue(new Error('Queue full'));
  
  const result = await jobHandler.process(data);
  
  expect(result.status).toBe('failed');
  expect(result.error).toBe('Queue full');
});
```

## Test Execution

### Run Tests

```bash
# Run all tests
npm test

# Watch mode for development
npm run test:watch

# Coverage report
npm run test:coverage

# Run specific test file
npm test -- user.service.test.js

# Run tests matching pattern
npm test -- --testNamePattern="should create user"
```

### CI/CD Integration

```bash
# Run tests before committing
npm test

# Generate coverage report for CI
npm run test:coverage

# Exit with error if coverage below threshold
npm run test:coverage -- --collectCoverageFrom="src/**/*.js" --coverageThreshold='{"global":{"branches":80,"functions":80,"lines":80,"statements":80}}'
```

### Performance Tests

- Write performance tests for critical paths
- Test database query performance with realistic data volumes
- Test job processing with large batch sizes
- Monitor test execution time and flag regressions

## Quick Reference

| Concept | Pattern |
|---------|---------|
| **Setup** | `beforeEach()` / `afterEach()` |
| **Async Test** | `async () => { }` or `return promise` |
| **Mock Module** | `jest.mock('../module')` |
| **Spy on Function** | `jest.spyOn(obj, 'method')` |
| **Mock Return** | `mockReturnValue()` / `mockResolvedValue()` |
| **Reset Mocks** | `jest.resetAllMocks()` |
| **Verify Called** | `toHaveBeenCalled()` / `toHaveBeenCalledWith()` |
| **Test Data** | Fixtures and factories |
| **Error Handling** | `rejects.toThrow()` / `try/catch` |
| **Coverage** | `npm run test:coverage` |
