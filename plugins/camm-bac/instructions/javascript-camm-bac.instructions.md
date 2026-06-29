---
applyTo: "**/*.{js,ts}"
description: "JavaScript/Node.js development standards for strict API backend"
---

# JavaScript/Node.js Coding Standards

Apply the repository-wide guidance from `./github/copilot-instructions.md` to all code.

## General Guidelines

- Follow the project's established conventions and patterns consistently
- Prefer clear, readable code over clever abstractions
- Use Node.js idiomatic style and modern JavaScript practices (ES6+)
- you need to follow coding best practices.
- Write clean, maintainable, and well-documented code
- Follow modern JavaScript/ES6+ standards and patterns
- Follow `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/coding-conventions.md` for project-specific guidelines .

## Requirements

- Use meaningful generic parameter names
- Add constraints to generic types when appropriate
- Use default generic parameters for better ergonomics
- Implement generic factories and builders
- Use generic types for container and utility functions

- Use esm javascript all new files
- files with .mjs extension should be defaut module format
- Follow the existing naming conventions
- Write tests with jest for all new features in \***\*tests\*\*** directory
- compiler options are in jsconfig.json
- options for the features provided by the JavaScript language service in ./jsconfig.json
- Use JSDoc comments for type annotations and documentation

## Coding-style

### Naming Conventions

- **Functions**: `camelCase` + action verbs (`migrateAccount`, `validateEmail`)
- **Classes**: `PascalCase` (`MigrationWorker`, `EmailRepository`)
- **Files**: `kebab-case` with `.mjs` extension (`migration-worker.mjs`)
- **Constants**: `SCREAMING_SNAKE_CASE` (`MAX_BATCH_SIZE`)

```javascript
// Constants - SCREAMING_SNAKE_CASE
const MAX_BATCH_SIZE = 1000;
const SOAP_TIMEOUT_MS = 30000;
const RETRY_ATTEMPTS = 3;

// Functions - camelCase avec verbes d'action
async function migrateBatchToOx(batchId) {}
function validateEmailAddress(email) {}
async function retryFailedMigration(migrationId) {}

// Classes - PascalCase
class MigrationWorker {}
class SoapClient {}
class EmailRepository {}

// Files - kebab-case
// migration-worker.js
// email-repository.js
// soap-client.js
```

### Function Design

- **Size:** Maximum 50 lines per function
- **Parameters:** Maximum 3 parameters (use an options object for more)
- **Single Responsibility:** One function = one clear action
- **Return Early:** Validate and exit early to reduce complexity

```javascript
// ✅ Bon exemple
async function processMigrationBatch(batchId, options = {}) {
    // Validation early
    if (!batchId) {
        throw new ValidationError('Batch ID is required')
    }

    // Return early si déjà traité
    const batch = await db.batches.findById(batchId)
    if (batch.status === 'completed') {
        return { status: 'skipped', reason: 'already_completed' }
    }

    // Logique principale
    const result = await migrateBatch(batch, options)
    return result
}

// ❌ Mauvais exemple
async function processMigrationBatch(batchId, retries, timeout, skipValidation) {
    if (batchId) {
        const batch = await db.batches.findById(batchId)
        if (batch) {
            if (batch.status !== 'completed') {
                // ... logique imbriquée
            }
        }
    }
}

// ✅  async/await
async function fetchUserData(userId) {
    try {
        const user = await db.users.findById(userId)
        const emails = await db.emails.findByUserId(userId)
        return { user, emails }
    } catch (error) {
        throw new DatabaseError('Failed to fetch user data', { userId, cause: error })
    }
}

// Utiliser Promise.all pour parallélisme
const [bacData, oxData] = await Promise.all([bacApi.getAccount(accountId), oxApi.getMailbox(mailboxId)])

## Function Best Practices

// ✅ Bon : validation early, return early
async function processEmail(emailData) {
    if (!emailData?.id) {
        throw new ValidationError('Email ID required')
    }

    if (emailData.isAlreadyMigrated) {
        return { status: 'skipped', reason: 'already_migrated' }
    }

    const result = await migrateEmail(emailData)
    return result
}

// ❌ Mauvais : nesting profond
async function processEmail(emailData) {
    if (emailData?.id) {
        if (!emailData.isAlreadyMigrated) {
            const result = await migrateEmail(emailData)
            return result
        }
    }
}

```

## Error Handling

### Hiérarchie des Erreurs Personnalisées

- use classes in `/lib/errors`:

```javascript
import {
  MigrationError, // Erreurs métier migration
  ApiError, // Erreurs API externes (BAC, OX, CST)
  DatabaseError, // Erreurs base de données
  ValidationError, // Erreurs de validation
  SoapError, // Erreurs SOAP spécifiques
  QueueError, // Erreurs message queue
} from "./lib/errors";

// Enrichir avec contexte
throw new MigrationError("Account migration failed", {
  accountId,
  batchId,
  step: "email_transfer",
  code: "TRANSFER_FAILED",
  cause: originalError,
});
```

```javascript
// Utiliser les erreurs personnalisées
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

    // ... migration logic
  } catch (error) {
    if (error instanceof ApiError) {
      // Retry logic
      return retryMigration(accountId);
    }
    throw error;
  }
}
```

## Database Pattern (Knex + Objection)

### Principes

- **Knex Only**: No raw SQL except in documented exceptional cases
- **Transactions**: Required for multi-table operations
- **Migrations**: Versioned in `/lib/db/migrations`
- **Models**: model and entities in `/lib/db/migrations`
- **Seed**: seed input data in `/lib/db/seed`
- **Repository Pattern**: One file per entity in `/lib/db/`

### Knex Configuration

```javascript
// knexfile.js pattern
module.exports = {
  client: "mysql2",
  connection: {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    charset: "utf8mb4",
  },
  pool: {
    min: 2,
    max: 10,
    acquireTimeoutMillis: 30000,
    idleTimeoutMillis: 600000,
  },
  migrations: {
    directory: "./resources/db/migrations",
    tableName: "knex_migrations",
  },
};
```

### Usage

```javascript
import { BatchMigration } from "./models/entities.mjs";

const batch = await BatchMigration.query().insert({
  uid: batchUid,
  status: "pending",
  flow: "Onemail_CST_AMT_OxAgenda_Only",
});

// Repository pattern
class MigrationRepository {
  constructor(knex) {
    this.db = knex;
    this.table = "migrations";
  }

  async create(migration) {
    const [id] = await this.db(this.table).insert(migration).returning("id");
    return id;
  }

  async updateStatus(id, status, metadata = {}) {
    return this.db(this.table)
      .where({ id })
      .update({
        status,
        metadata: JSON.stringify(metadata),
        updated_at: this.db.fn.now(),
      });
  }

  async findPendingBatches(limit = 100) {
    return this.db(this.table).where({ status: "pending" }).orderBy("created_at", "asc").limit(limit);
  }
}
```

## Message Queue Pattern

```javascript
// Producer
import { MigrationQueue } from "./lib/mq/queue";

async function enqueueMigrationBatch(accounts) {
  const queue = new MigrationQueue();

  const jobs = accounts.map((account) => ({
    type: "migrate_account",
    data: account,
    priority: account.isPriority ? 10 : 5,
    attempts: 3,
  }));

  await queue.addBatch(jobs);

  logger.info("Batch enqueued", {
    count: jobs.length,
    queueName: queue.name,
  });
}

// Consumer/Worker
import { MigrationWorker } from "./lib/mq/worker";

const worker = new MigrationWorker({
  concurrency: 5,
  maxJobTime: 300000, // 5 minutes
});

worker.on("job:complete", (job, result) => {
  logger.info("Job completed", { jobId: job.id, result });
});

worker.on("job:failed", async (job, error) => {
  logger.error("Job failed", { jobId: job.id, error: error.message });

  // Sauvegarder pour recovery
  await recovery.save({
    type: "job_failure",
    job,
    error: error.message,
  });
});

worker.start();
```

## Logging

- use pino logger

```javascript
import { getLogger } from "../common/logger-pino.mjs";

const log = getLogger({ "ommt.module": "<module-file-name>" });
log.info({ ise: job.id, status: "in_progress" }, "Processing migration");
log.error({ ise: job.id, error: err.message }, "Migration failed");
```

## Error Handling Pattern

```javascript
import { CstError, AmtError, SoapError, DbError } from "../errors/OmmtError.mjs";

try {
  await migrateAccount(accountId);
} catch (error) {
  throw new CstError("Contact migration failed", {
    accountId,
    step: "vcard_import",
    cause: error,
  });
}
```

## SOAP Wrapper Pattern

```javascript
import soap from "soap";
const xml = require("fs").readFileSync("myservice.wsdl", "utf8");

const url = "http://example.com/wsdl?wsdl";
const args = { name: "value" };

// async/await
const client = await soap.createClientAsync(url);
const result = await client.MyFunctionAsync(args);
console.log(result[0]);
```

## Request Patterns

```javascript
// Repository pattern
class EmailRepository {
  constructor(knex) {
    this.db = knex;
    this.table = "emails";
  }

  async findById(id) {
    return this.db(this.table).where({ id }).first();
  }

  async findByBatchId(batchId) {
    return this.db(this.table).where({ batch_id: batchId }).orderBy("created_at", "desc");
  }

  async createBatch(emails) {
    const trx = await this.db.transaction();

    try {
      const ids = await trx(this.table).insert(emails).returning("id");

      await trx.commit();
      return ids;
    } catch (error) {
      await trx.rollback();
      throw new DatabaseError("Batch insert failed", {
        count: emails.length,
        cause: error,
      });
    }
  }
}
```
