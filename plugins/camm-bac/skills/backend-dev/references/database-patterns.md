# Database Patterns with Knex and Objection

## Core Principles

- **Knex Only**: Use Knex for all queries—no raw SQL except in documented exceptional cases
- **Transactions**: Required for multi-table operations to maintain data consistency
- **Migrations**: Versioned in `/lib/db/migrations`
- **Models**: Define models and entities in `/lib/db/models`
- **Seed Data**: Add seed input data in `/lib/db/seed`
- **Repository Pattern**: One file per entity in `/lib/db/repositories`

## Knex Configuration

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

### Connection Pool Settings
- `min: 2` - Maintain at least 2 idle connections
- `max: 10` - Maximum 10 concurrent connections
- `acquireTimeoutMillis: 30000` - Wait up to 30 seconds for available connection
- `idleTimeoutMillis: 600000` - Close idle connections after 10 minutes

## Models and Entities

### Using Objection ORM
```javascript
import { BatchMigration } from "./models/entities.mjs";

const batch = await BatchMigration.query().insert({
  uid: batchUid,
  status: "pending",
  flow: "Onemail_CST_AMT_OxAgenda_Only",
});
```

## Repository Pattern

The Repository Pattern isolates data access logic from business logic. One repository file per entity.

```javascript
class MigrationRepository {
  constructor(knex) {
    this.db = knex;
    this.table = "migrations";
  }

  /**
   * Create a new migration record
   * @param {Object} migration - Migration data
   * @returns {Promise<number>} Inserted ID
   */
  async create(migration) {
    const [id] = await this.db(this.table)
      .insert(migration)
      .returning("id");
    return id;
  }

  /**
   * Update migration status and metadata
   * @param {number} id - Migration ID
   * @param {string} status - New status (pending, in_progress, completed, failed)
   * @param {Object} metadata - Additional metadata
   */
  async updateStatus(id, status, metadata = {}) {
    return this.db(this.table)
      .where({ id })
      .update({
        status,
        metadata: JSON.stringify(metadata),
        updated_at: this.db.fn.now(),
      });
  }

  /**
   * Find pending batches for processing
   * @param {number} limit - Maximum number of batches to return
   * @returns {Promise<Array>} Array of pending migration records
   */
  async findPendingBatches(limit = 100) {
    return this.db(this.table)
      .where({ status: "pending" })
      .orderBy("created_at", "asc")
      .limit(limit);
  }

  /**
   * Find migration by ID
   * @param {number} id - Migration ID
   * @returns {Promise<Object|null>} Migration record or null
   */
  async findById(id) {
    return this.db(this.table)
      .where({ id })
      .first();
  }
}
```

## Transaction Pattern

Transactions ensure data consistency when multiple tables are modified.

```javascript
class EmailRepository {
  constructor(knex) {
    this.db = knex;
    this.table = "emails";
  }

  /**
   * Create a batch of emails in a single transaction
   * @param {Array} emails - Array of email objects
   * @returns {Promise<number[]>} Array of inserted IDs
   * @throws {DatabaseError} If insert fails
   */
  async createBatch(emails) {
    const trx = await this.db.transaction();

    try {
      const ids = await trx(this.table)
        .insert(emails)
        .returning("id");

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

  /**
   * Find emails by batch ID
   * @param {number} batchId - Batch ID
   * @returns {Promise<Array>} Email records sorted by creation date
   */
  async findByBatchId(batchId) {
    return this.db(this.table)
      .where({ batch_id: batchId })
      .orderBy("created_at", "desc");
  }

  /**
   * Find email by ID
   * @param {number} id - Email ID
   * @returns {Promise<Object|null>} Email record or null
   */
  async findById(id) {
    return this.db(this.table)
      .where({ id })
      .first();
  }
}
```

## Migration Files

Migrations should be versioned and tracked by Knex.

```javascript
// migrations/20240101000000_create_migrations_table.mjs
export async function up(knex) {
  return knex.schema.createTable("migrations", (table) => {
    table.increments("id").primary();
    table.uuid("uid").unique().notNullable();
    table.string("status").defaultTo("pending");
    table.string("flow").notNullable();
    table.json("metadata").nullable();
    table.timestamps(true, true); // created_at, updated_at
  });
}

export async function down(knex) {
  return knex.schema.dropTable("migrations");
}
```

## Best Practices

### Always Use Parameterized Queries
Knex automatically parameterizes queries, preventing SQL injection:

```javascript
// ✅ Safe: Knex handles parameterization
const user = await this.db("users")
  .where({ email, status: "active" })
  .first();

// ❌ Never: Raw SQL concatenation (security risk)
const user = await this.db.raw(`SELECT * FROM users WHERE email = '${email}'`);
```

### Transaction Rules
1. Always use transactions for multi-table operations
2. Always rollback on error
3. Don't nest transactions unnecessarily
4. Keep transactions short (avoid long-running operations)

### Query Optimization
- Add indexes on frequently queried columns
- Avoid N+1 queries (use batch loading)
- Use `select()` to fetch only needed columns
- Use pagination for large result sets

```javascript
// ✅ Good: Indexed query with pagination
async function findActiveUsers(page = 1, pageSize = 50) {
  const offset = (page - 1) * pageSize;
  return this.db("users")
    .where({ status: "active" })
    .select("id", "email", "name") // Only needed columns
    .orderBy("created_at", "desc")
    .limit(pageSize)
    .offset(offset);
}
```

### Connection Pooling
- Reuse database connections via connection pool
- Never create a new connection per query
- Configure pool size based on load (typically 2-10)
- Monitor connection usage in production

## Seed Data

```javascript
// lib/db/seed/seed.mjs
async function seedDatabase(knex) {
  // Clear existing data
  await knex("migrations").del();

  // Insert seed data
  await knex("migrations").insert([
    {
      uid: "batch-001",
      status: "pending",
      flow: "Onemail_CST_AMT_OxAgenda_Only",
      metadata: JSON.stringify({ source: "seed" }),
    },
    {
      uid: "batch-002",
      status: "completed",
      flow: "Onemail_CST_AMT_OxAgenda_Only",
      metadata: JSON.stringify({ source: "seed", completedAt: new Date() }),
    },
  ]);
}

export default seedDatabase;
```
