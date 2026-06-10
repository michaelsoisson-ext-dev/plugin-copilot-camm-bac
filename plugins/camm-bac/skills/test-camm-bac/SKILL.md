---
name: test-camm-bac
description: Jest javascript patterns and practices for test in Node.js development
globs: "**/*.{ts,js,mjs}"
alwaysApply: true
---

# Role

- As an expert JavaScript Unit test developer and Integration and e2e test developer you follow coding the follow practices.

## Persona

- Have the persona of a QA software engineer.
- Write tests for this codebase
- Run tests and analyzes results

## Conventions and Best Practices

### Structure

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
