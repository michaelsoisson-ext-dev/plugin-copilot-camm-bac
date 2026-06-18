# JavaScript/Node.js Coding Standards

## General Guidelines

- Follow the project's established conventions and patterns consistently
- Prefer clear, readable code over clever abstractions
- Use Node.js idiomatic style and modern JavaScript practices (ES6+)
- Write clean, maintainable, and well-documented code
- Follow modern JavaScript/ES6+ standards and patterns

## Requirements

- Use meaningful generic parameter names
- Add constraints to generic types when appropriate
- Use default generic parameters for better ergonomics
- Implement generic factories and builders
- Use generic types for container and utility functions
- Use ESM (ECMAScript modules) for all new files
- Files should use `.mjs` extension as the default module format
- Follow the existing naming conventions
- Write tests with Jest for all new features in `tests/` directory
- Use JSDoc comments for type annotations and documentation
- Compiler options are in `jsconfig.json`

## Naming Conventions

### Functions
- Use `camelCase` with action verbs
- Examples: `migrateAccount`, `validateEmail`, `retryFailedMigration`

```javascript
async function processMigrationBatch(batchId) {}
function validateEmailAddress(email) {}
async function retryFailedMigration(migrationId) {}
```

### Classes
- Use `PascalCase`
- Examples: `MigrationWorker`, `EmailRepository`, `SoapClient`

```javascript
class MigrationWorker {}
class EmailRepository {}
class SoapClient {}
```

### Files
- Use `kebab-case` with `.mjs` extension for ESM modules
- Examples: `migration-worker.mjs`, `email-repository.mjs`, `soap-client.mjs`

### Constants
- Use `SCREAMING_SNAKE_CASE`
- Examples: `MAX_BATCH_SIZE`, `SOAP_TIMEOUT_MS`, `RETRY_ATTEMPTS`

```javascript
const MAX_BATCH_SIZE = 1000;
const SOAP_TIMEOUT_MS = 30000;
const RETRY_ATTEMPTS = 3;
```

### Folders
- Use `kebab-case`
- Examples: `src/routes`, `lib/db`, `tests/unit`

## Function Design

### Size and Complexity
- **Maximum 50 lines per function**: Keeps functions focused and testable
- **Maximum 3 parameters**: Use an options object for more parameters
- **Single Responsibility**: One function = one clear action
- **Return Early**: Validate and exit early to reduce nesting complexity

### Good Pattern: Early Returns
```javascript
// ✅ Good: Clear responsibility, returns early, max 3 params
async function createUser(email, password, options = {}) {
  if (!email) throw new ValidationError("Email required");
  if (await userExists(email)) throw new ValidationError("Email exists");

  const hash = await hashPassword(password);
  const user = await db.users.insert({ email, password_hash: hash });
  return user;
}
```

### Anti-Pattern: Deep Nesting
```javascript
// ❌ Bad: Too many params, nested logic, mixed concerns
async function processUser(email, password, skipValidation, timeout, retries, logLevel) {
  if (!skipValidation) {
    if (email) {
      if (password) {
        // deeply nested logic
      }
    }
  }
}
```

## Async/Await Patterns

### Always Use async/await
- Prefer `async/await` over `.then()` chains
- Improves readability and error handling
- Reduces callback complexity

```javascript
// ✅ Good: Clear async/await
async function fetchUserData(userId) {
  try {
    const user = await db.users.findById(userId);
    const emails = await db.emails.findByUserId(userId);
    return { user, emails };
  } catch (error) {
    throw new DatabaseError("Failed to fetch user data", { userId, cause: error });
  }
}
```

### Parallel Operations with Promise.all
- Use `Promise.all()` for independent async operations
- Reduces total execution time

```javascript
// ✅ Parallel: Executes both calls concurrently
const [bacData, oxData] = await Promise.all([
  bacApi.getAccount(accountId),
  oxApi.getMailbox(mailboxId)
]);
```

## Function Best Practices

### Validation Early Pattern
```javascript
// ✅ Good: validation early, return early
async function processEmail(emailData) {
  // Validate early
  if (!emailData?.id) {
    throw new ValidationError("Email ID required");
  }

  // Check state
  if (emailData.isAlreadyMigrated) {
    return { status: "skipped", reason: "already_migrated" };
  }

  // Business logic
  const result = await migrateEmail(emailData);
  return result;
}
```

### Anti-Pattern: Deep Nesting
```javascript
// ❌ Bad: nesting profond
async function processEmail(emailData) {
  if (emailData?.id) {
    if (!emailData.isAlreadyMigrated) {
      const result = await migrateEmail(emailData);
      return result;
    }
  }
}
```

## Import/Export Patterns

- Use ESM syntax for all new files
- Use named exports for functions and classes
- Use default export only when appropriate (single main export)

```javascript
// ✅ Good: Named exports
export async function migrateAccount(accountId) {}
export class MigrationWorker {}
export const BATCH_SIZE = 100;
```

## Code Style Guidelines

- Use meaningful variable names
- Prefer explicit over implicit
- Add comments for complex business logic
- Use JSDoc for public APIs

```javascript
/**
 * Migrates a batch of accounts to the new system
 * @param {string[]} accountIds - Array of account IDs to migrate
 * @param {Object} options - Migration options
 * @param {number} options.batchSize - Number of accounts per batch (default: 100)
 * @param {boolean} options.skipValidation - Skip pre-migration validation
 * @returns {Promise<Object>} Migration result with success/failure counts
 * @throws {ValidationError} If account IDs are invalid
 * @throws {MigrationError} If migration fails
 */
export async function migrateBatch(accountIds, options = {}) {
  const { batchSize = 100, skipValidation = false } = options;
  // Implementation...
}
```
