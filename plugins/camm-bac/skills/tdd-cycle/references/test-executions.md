---
applyTo: '__tests__/**,**/*.test.js,**/*.spec.js,tests/**'
description: "'Use these guidelines when generating or updating tests.Testing standards or writing JavaScript/TypeScript tests using Jest"
---

# Test Structure

```
__tests__/
├── unit/           # Mocked isolated unit tests  for all business logic (services, utilities)
├── integration/    # Tests with external dependencies  for API endpoints
└── helper/         # Test environment setup assistance
```

- Name test files with `.spec.js` suffix
- Place test files next to the code they test or in a dedicated `__tests__` directory
- Use descriptive test names that explain the expected behavior
- Use nested describe blocks to organize related tests
- Use clear assertions with meaningful messages
- Test behavior, not implementation
- Verify both happy path and error scenarios
- Check important state changes and side effects

## Pattern

- Follow the pattern: `describe('Component/Function/Class', () => { it('should do something', () => {}) })`
- One test file per source file (mirror directory structure)
- Group related tests with `describe()` blocks
- Use `beforeEach()` for common setup, `afterEach()` for cleanup
- Keep tests focused and independent
- Use descriptive test names: `should return 200 when user exists`
- Avoid generic names: DON'T use "test 1", "works", "test case"
- Follow: `[action] [condition] [expected result]` pattern
- Example: `should validate email format and reject invalid addresses`

## Unit Tests

- Test individual functions and classes in isolation
- Write tests for BullMQ job handlers
- Mock external dependencies (database, external APIs)
- Test with multiple input scenarios (valid, invalid, edge cases)
- Verify both success and error paths
- Maintain test coverage of 80% or higher

## Integration Tests

- Test API endpoints with actual HTTP requests
- Use test database fixtures or in-memory database
- Test request/response contract validation
- Verify error responses with correct status codes

### Job Queue Tests

- Test job handlers with mock data
- Verify job retry logic and failure handling
- Test job data validation
- Mock queue operations

# Test Execution

- Run tests before committing: `npm test`
- Use `npm run test:watch` for development
- Generate coverage reports: `npm run test:coverage`
- Integration tests should be separate and clearly marked
- Performance tests for critical paths (database queries, job processing)

- use [Jest](https://jestjs.io/docs/getting-started) with Node.js and ESM modules (.mjs)
- jest config in `jest.config.js`
- Write tests `*.spec.js` with jest for all new features directory

# Effective Mocking

Mock at **system boundaries** only:

- External APIs (payment, email, etc.)
- Databases (sometimes - prefer test DB)
- Time/randomness
- File system (sometimes)

Don't mock:

- Your own classes/modules
- Internal collaborators
- Anything you control

- Use `jest.mock()` for module-level mocks
- Use `jest.spyOn()` for specific function mocks
- Use `mockImplementation()` or `mockReturnValue()` to define mock behavior
- Reset mocks between tests with `jest.resetAllMocks()` in `afterEach`

## Designing for Mockability

At system boundaries, design interfaces that are easy to mock:

**1. Use dependency injection**

Pass external dependencies in rather than creating them internally:

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
    return paymentClient.charge(order.total)
}

// Hard to mock
function processPayment(order) {
    const client = new StripeClient(process.env.STRIPE_KEY)
    return client.charge(order.total)
}
```

**2. Prefer SDK-style interfaces over generic fetchers**

Create specific functions for each external operation instead of one generic function with conditional logic:

```typescript
// GOOD: Each function is independently mockable
const api = {
    getUser: (id) => fetch(`/users/${id}`),
    getOrders: (userId) => fetch(`/users/${userId}/orders`),
    createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
}

// BAD: Mocking requires conditional logic inside the mock
const api = {
    fetch: (endpoint, options) => fetch(endpoint, options),
}
```

The SDK approach means:

- Each mock returns one specific shape
- No conditional logic in test setup
- Easier to see which endpoints a test exercises
- Type safety per endpoint
