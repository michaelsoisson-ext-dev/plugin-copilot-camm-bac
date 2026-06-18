# Error Handling Patterns

## Custom Error Hierarchy

Use custom error classes from `lib/errors/` to provide clear error context and enable targeted error handling.

### Error Classes

The project defines a hierarchy of custom error classes:

```javascript
import {
  MigrationError,    // Business logic errors during migration
  ApiError,          // External API call failures (BAC, OX, CST)
  DatabaseError,     // Database operation failures
  ValidationError,   // Input validation failures
  SoapError,         // SOAP-specific protocol errors
  QueueError,        // Job queue operation failures
} from "./lib/errors";
```

## Throwing Errors with Context

Always throw errors with rich context to aid debugging:

```javascript
// ✅ Good: Include context about what failed
throw new MigrationError("Account migration failed", {
  accountId,
  batchId,
  step: "email_transfer",
  code: "TRANSFER_FAILED",
  cause: originalError,
});
```

## Error Handling in Services

```javascript
import { MigrationError, ApiError, DatabaseError } from "./lib/errors";

async function migrateAccount(accountId) {
  try {
    const account = await fetchAccount(accountId);

    if (!account) {
      throw new MigrationError("Account not found", {
        accountId,
        code: "ACCOUNT_NOT_FOUND",
      });
    }

    // Migration logic...
  } catch (error) {
    if (error instanceof ApiError) {
      // External API failed—may be retryable
      return retryMigration(accountId);
    }

    if (error instanceof DatabaseError) {
      // Database error—log and propagate
      logger.error("Database operation failed", { accountId, error: error.message });
      throw error;
    }

    // Unknown error—wrap and propagate
    throw new MigrationError("Migration failed", {
      accountId,
      cause: error,
    });
  }
}
```

## Validation Errors

Validation errors should be thrown early at request boundaries:

```javascript
import { ValidationError } from "./lib/errors";

async function updateUser(userId, data) {
  // Validate early
  if (!userId) {
    throw new ValidationError("User ID required");
  }

  if (!data?.email) {
    throw new ValidationError("Email is required");
  }

  if (!isValidEmail(data.email)) {
    throw new ValidationError("Invalid email format", { email: data.email });
  }

  // Database operation
  try {
    const user = await db.users.update(userId, data);
    return user;
  } catch (error) {
    throw new DatabaseError("Failed to update user", {
      userId,
      cause: error,
    });
  }
}
```

## Error Recovery and Retry Logic

Implement retry logic for transient failures:

```javascript
async function retryMigration(accountId, maxAttempts = 3) {
  let lastError;

  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await migrateAccount(accountId);
    } catch (error) {
      lastError = error;

      if (error instanceof ApiError && attempt < maxAttempts) {
        // API error—may be transient, retry with exponential backoff
        const delayMs = Math.pow(2, attempt) * 1000;
        await sleep(delayMs);
        continue;
      }

      // Not retryable or final attempt
      throw error;
    }
  }

  throw new MigrationError("Max retry attempts exceeded", {
    accountId,
    attempts: maxAttempts,
    cause: lastError,
  });
}
```

## Job Queue Error Handling

Handle errors in message queue consumers:

```javascript
import { MigrationWorker } from "./lib/mq/worker";
import { logger } from "./lib/logger";

const worker = new MigrationWorker({
  concurrency: 5,
  maxJobTime: 300000, // 5 minutes
});

worker.on("job:complete", (job, result) => {
  logger.info("Job completed", { jobId: job.id, result });
});

worker.on("job:failed", async (job, error) => {
  logger.error("Job failed", {
    jobId: job.id,
    error: error.message,
    attempt: job.attempt,
  });

  // Save for recovery
  await recovery.save({
    type: "job_failure",
    job,
    error: error.message,
    timestamp: new Date(),
  });
});

worker.start();
```

## Logging Errors

Never log sensitive data (passwords, tokens, PII):

```javascript
import { getLogger } from "../common/logger-pino.mjs";

const log = getLogger({ module: "user-service" });

async function processUser(userId) {
  try {
    const user = await db.users.findById(userId);
    log.info({ userId, status: "processing" }, "Processing user");

    // ✅ Good: Log non-sensitive context
    log.error(
      { userId, step: "validation", errorCode: error.code },
      "Validation failed"
    );

    // ❌ Bad: Never log sensitive data
    // log.error({ userId, password: user.password }, "Auth failed");
  } catch (error) {
    log.error(
      { userId, errorType: error.constructor.name },
      "User processing failed"
    );
  }
}
```

## Custom Error Class Definition

```javascript
// lib/errors/AppError.mjs
export class AppError extends Error {
  constructor(message, context = {}) {
    super(message);
    this.name = this.constructor.name;
    this.context = context;
    Error.captureStackTrace(this, this.constructor);
  }

  toString() {
    return `${this.name}: ${this.message}`;
  }

  toJSON() {
    return {
      name: this.name,
      message: this.message,
      context: this.context,
    };
  }
}

export class MigrationError extends AppError {}
export class ApiError extends AppError {}
export class DatabaseError extends AppError {}
export class ValidationError extends AppError {}
export class SoapError extends AppError {}
export class QueueError extends AppError {}
```

## Error Handling in HTTP Routes

Centralize error handling in route handlers:

```javascript
import { Router } from "fastify";
import { ValidationError, DatabaseError } from "../lib/errors";

export async function registerUserRoutes(fastify) {
  const router = Router();

  router.post("/users", async (request, reply) => {
    try {
      const { email, password } = request.body;

      // Validate input
      if (!email || !password) {
        throw new ValidationError("Email and password required");
      }

      // Create user
      const user = await userService.createUser(email, password);

      reply.status(201).send({ success: true, data: user });
    } catch (error) {
      if (error instanceof ValidationError) {
        reply.status(400).send({
          success: false,
          error: error.message,
          code: "VALIDATION_ERROR",
        });
      } else if (error instanceof DatabaseError) {
        reply.status(500).send({
          success: false,
          error: "Database operation failed",
          code: "DB_ERROR",
        });
      } else {
        reply.status(500).send({
          success: false,
          error: "Internal server error",
          code: "INTERNAL_ERROR",
        });
      }
    }
  });

  fastify.register(router);
}
```

## Best Practices Summary

1. **Throw Early**: Validate input and throw at boundaries
2. **Rich Context**: Include relevant context in error objects
3. **Specific Classes**: Use specific error classes for targeted handling
4. **No Sensitive Data**: Never log passwords, tokens, or PII
5. **Recovery**: Implement retry logic for transient failures
6. **Propagation**: Re-wrap errors with additional context as they propagate
7. **Consistency**: Use the same error patterns throughout the codebase
