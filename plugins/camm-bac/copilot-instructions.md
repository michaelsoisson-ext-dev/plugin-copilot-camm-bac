# Copilot Instructions for OneMailMigration (OMMT)

This document provides essential context for AI assistants working on the OMMT codebase.

## Project Overview

**OMMT** is an enterprise-scale Node.js application for migrating email, contacts, and calendars between BAC and Open-Xchange 7.10 platforms.

- **Tech Stack**: Node.js 24+ ESM, Fastify, BullMQ, MariaDB, Redis, Pino, Jaeger
- **Architecture**: Modular, async-first with distributed workers
- **Language**: Code in English, business context in French

## Build & Test Commands

### Setup

```bash
npm install                          # Install dependencies
npm run db:ommt-full:up             # Run all database migrations
docker compose up -d                 # Start local Docker stack
```

### Testing

```bash
npm test                             # Run all tests
npm run test:cst                     # Contact sync tests
npm run test:bac                     # BAC API tests
npm run test:ox710                   # OX 7.10 API tests
npm run test:bpsorder                # SOAP marker tests
npm run test:utils                   # Utility tests
npm run test:mq                      # Queue tests
```

### Linting & Type Checking

```bash
npm run eslint                       # Run ESLint with auto-fix
npm run type:check                   # Type-check with JSDoc
npm run type:update                  # Generate *.d.ts files
```

### Database

```bash
npm run db:ommt-init:up              # Create OMMT schema
npm run db:ommt-topology:up          # Create topology tables
npm run db:mail-sending-init:up      # Create mail campaign tables
npm run db:ommt-topology:seed        # Seed test data
```

### Development

```bash
npm run ommt:service                 # Start main service
npm run ommt:service:trace           # With Jaeger tracing
npm run bullboard                    # BullMQ UI (http://bullboard.localhost)
npm run server                       # Simple HTTP server
```

### Release

```bash
npm run release:minor                # Bump minor version
npm run release:patch                # Bump patch version
npm run release:changelog            # Update changelog only
```

## High-Level Architecture

### Core Layers

```
CLI Commands (/lib/command)
         ↓
Data Loader Orchestrator (/lib/mq)
         ↓
API Clients (bac/, ox/, cst/, soap/)
         ↓
Data Layer (/lib/db)
         ↓
MariaDB + Redis
```

### Worker Architecture

Five independent workers process migrations in parallel:

1. **OneEmail Worker** (`lib/mq/worker-onemail.mjs`) - Sets `email/onemail` flag via SOAP
2. **CST Worker** (`lib/mq/worker-cst.mjs`) - Syncs contacts BAC → OX
3. **AMT Worker** (`lib/mq/worker-amt.mjs`) - Syncs calendars BAC → OX
4. **OxAgenda Worker** (`lib/mq/worker-oxagenda.mjs`) - Sets `email/ox_agenda_contact` flag
5. **Mail Worker** (`lib/mq/worker-mail.mjs`) - Sends email notifications (J-7, J-3, post-migration)

Each worker:

- Processes batches of 100-1000 items
- Implements automatic retry with exponential backoff
- Reports to database via status updates
- Logs with correlation IDs (ISE, batch UID)

### Database Model

**Key Tables**:

- `batch_migration` - Batch definitions and lifecycle
- `user` - User entries per batch
- `user_run` - Status history per worker run
- `user_topology` - Complete user reference data
- `mail_campaigns` - Email campaign tracking

## Key Conventions

### File & Naming

- **Files**: `kebab-case.mjs` (ES modules)
- **Functions**: `camelCase` with action verbs (`migrateBatch`, `validateEmail`)
- **Classes**: `PascalCase` (`MigrationWorker`, `SoapClient`)
- **Constants**: `SCREAMING_SNAKE_CASE` (`MAX_BATCH_SIZE = 1000`)

### Error Handling

Use custom error classes from `/lib/errors`:

```javascript
import { MigrationError, ApiError, DatabaseError } from './lib/errors'

throw new MigrationError('Account migration failed', {
    accountId,
    batchId,
    step: 'email_transfer',
    code: 'TRANSFER_FAILED',
    cause: originalError,
})
```

### Logging (Pino ECS Format)

```javascript
import { getLogger } from './lib/common/logger-pino.mjs'

const log = getLogger({ 'ommt.module': 'worker-cst' })
log.info({ ise: '12345', status: 'in_progress' }, 'Processing contact sync')
log.error({ ise: '12345', error: err.message }, 'Migration failed')
```

### Database (Knex + Objection)

- **Only Knex** for queries (no raw SQL except documented exceptions)
- **Transactions** required for multi-table operations
- **Repository pattern** with one file per entity
- **Migrations** in `/resources/db/migrations`

```javascript
const batch = await BatchMigration.query().insert({
    uid: batchUid,
    status: 'pending',
    flow: 'Onemail_CST_AMT_OxAgenda_Only',
})
```

### Message Queue (BullMQ)

- Producers add jobs to specific queues (CST, AMT, OneEmail, OxAgenda, Mail)
- Workers consume with concurrency limits
- Dead-letter queue for persistent failures
- Automatic retry configured per queue

### Async Patterns

```javascript
// ✅ Parallel operations
const [bacData, oxData] = await Promise.all([bacApi.getAccount(id), oxApi.getMailbox(id)])

// ✅ Batch processing
const BATCH_SIZE = 100
for (let i = 0; i < items.length; i += BATCH_SIZE) {
    const batch = items.slice(i, i + BATCH_SIZE)
    await Promise.all(batch.map((item) => process(item)))
}
```

### Function Design

- **Max 50 lines** per function
- **Max 3 parameters** (use options object for more)
- **Return early** to reduce nesting
- **Single responsibility** principle

### Testing (Jest)

- Tests in `__tests__/` with `*.spec.js` naming
- Use async/await for promises
- Mock external APIs and databases
- Integration tests run actual migrations in test DB

```javascript
describe('MigrationWorker', () => {
    describe('processBatch', () => {
        it('should migrate all contacts in batch', async () => {
            // test implementation
        })
    })
})
```

## Directory Structure Reference

```
lib/
├── command/          # CLI entry points (one file = one command)
├── bac/              # BAC REST API client
├── ox/               # OX 7.10 REST API client
├── cst/              # CST sync API
├── soap/             # SOAP client for Promise flagging (bpsOrder)
├── db/               # Knex migrations, models, repositories
├── mq/               # BullMQ workers and queue orchestration
├── common/           # Logger (Pino), utilities, helpers
├── errors/           # Custom error classes hierarchy
├── metrics/          # Jaeger/OpenTelemetry tracing
├── wassup/           # LDAP/auth integration
└── ldap/             # LDAP directory service

types/                # JSDoc type definitions (*.d.ts)
__tests__/            # Jest tests (unit, integration, helpers)
resources/
├── db/               # SQL migrations and seeds
└── uml/              # Technical documentation (16+ guides)
```

## Important Patterns

### Observability

All critical operations should emit Jaeger traces:

```javascript
import { tracer } from '../metrics/metrics.mjs'

const span = tracer.startSpan('contact_migration', { attributes: { ise } })
try {
    // operation
} finally {
    span.end()
}
```

### Status Tracking

Workers update user status via database after each step:

- Status values: `pending`, `in_progress`, `completed`, `failed`, `retrying`
- Flow codes document the migration stage (e.g., `ONEMAIL_DONE`, `CST_DONE`)
- Every status change is logged with context

### Idempotence

All migrations must be replayable without side effects:

- Check if already processed before applying changes
- Use upsert operations where possible
- Implement recovery logic in `/lib/recovery`

### Rate Limiting

The codebase handles HTTP 429 responses:

- Automatic backoff with exponential delay
- Respects Retry-After headers
- Configurable per API endpoint

## When Adding New Features

2. **Determine the layer**:
    - CLI command? → `/lib/command`
    - New API client? → `/lib/bac`, `/lib/ox`, or `/lib/cst`
    - Data persistence? → `/lib/db` (migrations + repository)
    - Async job? → `/lib/mq` (worker)
    - Utility? → `/lib/common`
    - Error case? → Add class to `/lib/errors`

3. **Add tests** in `__tests__/` before or alongside implementation
4. **Log with context** (ISE, batch UID, correlation ID)
5. **Implement error handling** with custom error classes
6. **Add Jaeger spans** for distributed tracing
7. **Update JSDoc types** in `types/` for IDE support
8. **Document** the feature in `resources/uml/`

## Important References

- **Business Rules**: `AGENTS.md` (French)
- **Code Convention** → `.github/instructions/code-conventions.md`
- **Code Style** → `.github/instructions/javascript-rules.md`
- **Folder Structure** → `.github/instructions/rules/folder-rules.md`
- **Tests** → `.github/instructions/tests-rules.md`
- **Docker** → `.github/instructions/docker.md`
- **Technical Docs**: `resources/uml/README.md`

## Database Migrations

Migrations are versioned and use Knex:

```bash
# Create migration file
knex migrate:make --stub migration_name --knexfile ./lib/db/knexfile.mjs

# Run migrations
npm run db:ommt-full:up

# Rollback last
npm run db:ommt-rollback:step
```

Migrations live in `/resources/db/migrations/` and use timestamps for ordering.

## Docker Stack

The project includes a complete local development stack:

```bash
docker compose up -d

# Services available:
# - OMMT API
# - BullBoard (http://bullboard.localhost)
# - Redis Commander (http://commander.localhost)
# - phpMyAdmin (http://db.localhost)
# - Jaeger Tracing (http://trace.localhost)
# - Portainer (http://portainer.localhost)
```

Configuration via `.env.docker` (Docker network hostnames).

## Performance Considerations

- **Batch size**: 100-1000 items per worker batch
- **Concurrency**: 10 parallel workers per queue
- **Connection pooling**: Reuse DB/HTTP connections
- **Caching**: Redis for frequently accessed data
- **Parallel ops**: Use `Promise.all()` for independent calls

## Monitoring & Debugging

1. **Logs**: Check Pino logs (Kibana with ECS format)
2. **Tracing**: View Jaeger traces (http://trace.localhost)
3. **Queues**: Monitor BullBoard (http://bullboard.localhost)
4. **Database**: phpMyAdmin (http://db.localhost)

## Commit Messages

Use Conventional Commits (enforced by Husky + Commitlint):

```
feat(worker-cst): add batch retry logic
fix(api-bac): handle 429 rate limit errors
docs(architecture): update worker diagram
test(db): add user repository tests
```

---

**Last updated**: 2026-06-18  
For questions about specific modules, check the detailed guides in `resources/uml/`
