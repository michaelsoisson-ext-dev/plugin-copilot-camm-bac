---
name: backend-dev
description: Build Node.js/Fastify backend APIs, services, and database layers following JavaScript standards. Use this skill when creating REST endpoints, implementing services, writing database queries, designing data access patterns, handling transactions, or structuring scalable backend architecture. Include this skill for feature implementation, API design, service orchestration, async job processing with BullMQ, or any backend development task. Integrates project-specific coding standards, error handling patterns, database design, and security practices.
---

# Backend API Development

Build production-grade Node.js/Fastify backend systems following architectural patterns for security, performance, and scalability. All code follows project JavaScript/Node.js standards.

## When to Use

- Creating REST/SOAP API endpoints and routes
- Implementing business logic services and repositories
- Designing database schemas and migrations
- Building async job processors with job queues
- Structuring error handling and validation layers
- Planning backend architecture and data flows
- Writing database operations with transactions
- Designing cache strategies and performance optimizations

## Tech Stack (Project Default)

| Component     | Technology      | Use Case                                        |
| ------------- | --------------- | ----------------------------------------------- |
| **Runtime**   | Node.js 24+ ESM | Server execution, async operations              |
| **Framework** | Fastify         | HTTP routing, request handling, middleware      |
| **Database**  | MariaDB + Knex  | Data persistence, migrations, queries           |
| **Queue**     | BullMQ + Redis  | Async jobs, background workers, task scheduling |
| **Logging**   | Pino            | Structured logging, performance tracing         |
| **Testing**   | Jest            | Unit & integration tests, mocking               |
| **Types**     | JSDoc + .d.ts   | Static type checking without TypeScript         |

**Note:** Project may use different tech stack. Adapt patterns to your framework (Express, NestJS, etc.) and database (PostgreSQL, MongoDB, etc.).

## Development Workflow

### 1. **Plan Phase**

Understand requirements and design decisions:

- What resources/entities does the API manage?
- What's the data model and relationships?
- Which operations are sync vs async (job queue)?
- Security requirements (auth, validation, rate limiting)?
- Performance constraints (caching, indexing, scaling)?
- External integrations (APIs, databases, message queues)?

### 2. **Implement Phase**

Build in layers following project architecture:

**Layer 1: Routes** - Entry points, HTTP method handling

- Parse URL parameters, validate request format
- Delegate to controller for business logic
- Return response with appropriate status code

**Layer 2: Controllers** - Request validation and orchestration

- Validate input (type, format, constraints)
- Extract parameters from request body/query/params
- Call service methods for business logic
- Format and return response

**Layer 3: Services** - Business logic and orchestration

- Implement core business rules
- Orchestrate multi-step workflows
- Handle transactions and rollbacks
- May trigger async jobs

**Layer 4: Repositories** - Data access only

- Pure database queries (no business logic)
- Handle transactions
- Manage relationships and constraints
- Log query metrics

**Layer 5: Models/Entities** - Data definitions

- Define schema structure and relationships
- Add constraints and validation rules
- Document field meanings

### 3. **Test Phase**

Write tests for all layers:

- **Unit tests**: Services and repositories with mocked dependencies
- **Integration tests**: Full API endpoints with test database
- **Fixtures**: Create test data (happy path, edge cases, errors)
- **Cleanup**: Rollback test data after each test
- **Coverage**: Aim for >80% on critical paths (auth, data mutation, error handling)

### 4. **Document Phase**

- Add JSDoc annotations on exported functions (parameters, return types, errors)
- Update README with new endpoints and features
- Document architecture decisions where non-obvious
- Add inline comments for complex business logic

### 5. **Review Phase**

Verify against standards checklist (see **Code Standards** section below)

## Code Standards

All code must follow project JavaScript/Node.js coding standards. Key principles:

### Naming Conventions (See `references/javascript-standards.md`)

- **Functions**: `camelCase` with action verbs (`createUser`, `validateEmail`, `migrateAccount`)
- **Classes**: `PascalCase` (`UserService`, `EmailRepository`, `MigrationWorker`)
- **Constants**: `SCREAMING_SNAKE_CASE` (`MAX_BATCH_SIZE`, `TIMEOUT_MS`)
- **Files**: `kebab-case` with `.mjs` for ESM or `.js` for CommonJS (`user-service.js`)
- **Folders**: `kebab-case` (`src/routes`, `lib/db`, `tests/unit`)

### Function Design Rules

- **Size**: Maximum 50 lines per function
- **Parameters**: Maximum 3 (use options object for more)
- **Responsibility**: One function = one clear action
- **Return Early**: Validate and exit early to reduce nesting
- **Async**: Use `async/await` (not `.then()` chains)

**Example:**

```javascript
// ✅ Good: Clear responsibility, returns early, max 3 params
async function createUser(email, password, options = {}) {
  if (!email) throw new ValidationError("Email required");
  if (await userExists(email)) throw new ValidationError("Email exists");

  const hash = await hashPassword(password);
  const user = await db.users.insert({ email, password_hash: hash });
  return user;
}

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

### Error Handling

Always use custom error classes from `lib/errors/`:

- `ValidationError` - Invalid input
- `DatabaseError` - Database operation failed
- `ApiError` - External API call failed
- `MigrationError` - Business logic error
- `QueueError` - Job queue error

**Example:**

```javascript
import { ValidationError, DatabaseError } from "./lib/errors";

async function updateUser(userId, data) {
  if (!userId) throw new ValidationError("User ID required");

  try {
    const user = await db.users.update(userId, data);
    return user;
  } catch (error) {
    throw new DatabaseError("Failed to update user", { userId, cause: error });
  }
}
```

### Database Patterns (Knex)

**Repository Pattern** - One class per entity:

```javascript
class UserRepository {
  constructor(db) {
    this.db = db;
  }

  async findById(id) {
    return this.db("users").where({ id }).first();
  }

  async create(userData) {
    const trx = await this.db.transaction();
    try {
      const [id] = await trx("users").insert(userData).returning("id");
      await trx.commit();
      return id;
    } catch (error) {
      await trx.rollback();
      throw new DatabaseError("Insert failed", { cause: error });
    }
  }
}
```

**Rules:**

- Use Knex for all queries (no raw SQL unless documented)
- Transactions required for multi-table operations
- Migrations versioned in `lib/db/migrations` or project equivalent
- Indexes on frequently queried columns

### Async Job Queue Pattern (BullMQ or project equivalent)

```javascript
// Producer: Add job to queue
const queue = new JobQueue("migration");
await queue.add("migrate_account", { accountId, priority: 10 }, { attempts: 3 });

// Consumer: Process job
queue.process("migrate_account", async (job) => {
  const { accountId } = job.data;
  const result = await migrateAccount(accountId);
  return result;
});
```

**Rules:**

- Use batching for large operations (100-1000 items per batch)
- Set retry attempts (typically 3)
- Set priority for important jobs
- Log job start, progress, completion
- Use exponential backoff for retries

### Performance Requirements

- **Batch Processing**: Process in batches of 100-1000 items
- **Connection Pooling**: Reuse DB and HTTP connections
- **Caching**: Use Redis for frequently accessed data with TTL
- **Parallel Processing**: Use `Promise.all()` for independent operations
- **Query Optimization**: Add indexes, avoid N+1 queries, use batch loading
- **Monitoring**: Log key metrics (response time, error rate, queue depth)

```javascript
// ✅ Batch processing
async function migrateAccounts(accountIds) {
  const BATCH_SIZE = 100;
  for (let i = 0; i < accountIds.length; i += BATCH_SIZE) {
    const batch = accountIds.slice(i, i + BATCH_SIZE);
    await Promise.all(batch.map((id) => migrateAccount(id)));
  }
}

// ❌ Avoid sequential processing
async function migrateAccounts(accountIds) {
  for (const id of accountIds) {
    await migrateAccount(id); // Slow!
  }
}
```

## Security Checklist

- ✅ **Secrets**: All API keys, passwords, tokens in environment variables (never hardcoded)
- ✅ **Input Validation**: Always validate at request boundaries
- ✅ **Sanitization**: Clean user input before storage or API calls
- ✅ **Auth**: JWT tokens or session-based, never log tokens/passwords
- ✅ **Errors**: Generic messages to clients, detailed logs server-side (don't leak info)
- ✅ **SQL**: Use parameterized queries (Knex handles this)
- ✅ **Rate Limiting**: Protect endpoints from abuse
- ✅ **CORS**: Restrict origins appropriately
- ✅ **Logging**: Never log sensitive data (passwords, tokens, PII)

## Testing Guidelines

**Unit Tests** (Services, Repositories):

- Mock external dependencies
- Test happy path and error cases
- Verify error messages and codes
- No database needed (use mocks)

**Integration Tests** (API Endpoints):

- Full request → response flow
- Use test database with fixtures
- Test authentication and authorization
- Clean up test data (rollback transactions)

**Fixtures:**

- Create test data for each scenario
- Use factories for consistent data
- Clean up after each test

**Coverage:**

- Aim for >80% on critical paths (auth, data mutation, error handling)
- 100% on error conditions
- Document why untested code is excluded

## References

**For detailed guidance, see:**

- `references/javascript-standards.md` - Naming, function design, async patterns
- `references/database-patterns.md` - Knex, transactions, migrations, repositories
- `references/error-handling.md` - Custom error classes, error recovery
- `references/security-practices.md` - Secrets, validation, sanitization
- `references/performance-optimization.md` - Caching, batching, connection pooling

**Files reference project-specific standards if available:**

- Project coding conventions
- Project architecture guide
- Project testing setup
