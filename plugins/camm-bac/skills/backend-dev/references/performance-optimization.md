# Performance Optimization

## Batch Processing

Process large datasets in batches of 100-1000 items to balance memory usage and I/O efficiency.

### Good Pattern: Batch Processing
```javascript
// ✅ Good: batch process
async function migrateAccounts(accountIds) {
  const BATCH_SIZE = 100;

  for (let i = 0; i < accountIds.length; i += BATCH_SIZE) {
    const batch = accountIds.slice(i, i + BATCH_SIZE);
    await Promise.all(batch.map((id) => migrateAccount(id)));
  }
}
```

### Anti-Pattern: Sequential Processing
```javascript
// ❌ Bad: Sequential processing (slow!)
async function migrateAccounts(accountIds) {
  for (const id of accountIds) {
    await migrateAccount(id); // Waits for each one
  }
}
```

### Batch Configuration
```javascript
// lib/config.mjs
export const BATCH_SIZES = {
  DEFAULT: 100,
  SMALL: 50,
  LARGE: 1000,
  DB_INSERT: 500,
  API_CALLS: 50, // Lower for API rate limiting
};
```

## Connection Pooling

### Database Connection Pool

Reuse database connections via a pool. Configure appropriately for your workload:

```javascript
// knexfile.js
module.exports = {
  client: "mysql2",
  connection: {
    // connection settings...
  },
  pool: {
    min: 2,              // Maintain at least 2 idle connections
    max: 10,             // Allow up to 10 concurrent connections
    acquireTimeoutMillis: 30000,    // Wait 30s for available connection
    idleTimeoutMillis: 600000,      // Close idle connections after 10min
    validateConnection: (connection) => {
      // Optional: validate connection before reuse
      return connection.query("SELECT 1");
    },
  },
};
```

### HTTP Connection Pooling

Reuse HTTP connections for external APIs:

```javascript
import http from "http";
import https from "https";

// Create agents with connection pooling
const httpAgent = new http.Agent({
  keepAlive: true,
  maxSockets: 50,
});

const httpsAgent = new https.Agent({
  keepAlive: true,
  maxSockets: 50,
});

// Use in requests
const response = await fetch(url, {
  agent: url.startsWith("https") ? httpsAgent : httpAgent,
});
```

## Caching with Redis

### Redis Caching Pattern

Use Redis for frequently accessed data with appropriate TTL:

```javascript
import Redis from "ioredis";

const redis = new Redis({
  host: process.env.REDIS_HOST,
  port: process.env.REDIS_PORT,
  password: process.env.REDIS_PASSWORD,
});

async function getCachedUser(userId, ttl = 3600) {
  // Try cache first
  const cached = await redis.get(`user:${userId}`);
  if (cached) {
    return JSON.parse(cached);
  }

  // Cache miss—fetch from database
  const user = await db.users.findById(userId);

  // Store in cache with TTL (1 hour)
  if (user) {
    await redis.setex(`user:${userId}`, ttl, JSON.stringify(user));
  }

  return user;
}

// Invalidate cache after updates
async function updateUser(userId, data) {
  const user = await db.users.update(userId, data);

  // Clear cache
  await redis.del(`user:${userId}`);

  return user;
}
```

### Cache Invalidation Strategies

```javascript
// Time-based expiration (TTL)
async function getUser(userId) {
  const key = `user:${userId}`;
  let user = await redis.get(key);

  if (!user) {
    user = await db.users.findById(userId);
    // Cache for 1 hour
    await redis.setex(key, 3600, JSON.stringify(user));
  }

  return JSON.parse(user);
}

// Event-based invalidation
async function updateUser(userId, data) {
  const user = await db.users.update(userId, data);

  // Immediately invalidate cache
  await redis.del(`user:${userId}`);

  // Publish invalidation event
  await redis.publish("user:updated", JSON.stringify({ userId }));

  return user;
}

// Pattern-based invalidation
async function invalidateUserCache(userId) {
  // Clear all cache keys matching pattern
  const keys = await redis.keys(`user:${userId}:*`);
  if (keys.length > 0) {
    await redis.del(...keys);
  }
}
```

## Parallel Processing

### Promise.all for Independent Operations

Use `Promise.all()` to execute independent async operations concurrently:

```javascript
// ✅ Parallel: Executes both calls concurrently
async function getMigrationData(accountId) {
  const [bacData, oxData] = await Promise.all([
    bacApi.getAccount(accountId),
    oxApi.getMailbox(accountId),
  ]);

  return { bacData, oxData };
}

// ❌ Sequential: Waits for first call before starting second
async function getMigrationData(accountId) {
  const bacData = await bacApi.getAccount(accountId);
  const oxData = await oxApi.getMailbox(accountId); // Waits for above

  return { bacData, oxData };
}
```

### Promise.allSettled for Error Handling

Use when some operations might fail and you want results from all:

```javascript
async function migrateAccounts(accountIds) {
  const results = await Promise.allSettled(
    accountIds.map((id) => migrateAccount(id))
  );

  const successful = [];
  const failed = [];

  results.forEach((result, index) => {
    if (result.status === "fulfilled") {
      successful.push(result.value);
    } else {
      failed.push({
        accountId: accountIds[index],
        error: result.reason,
      });
    }
  });

  return { successful, failed };
}
```

## Query Optimization

### Avoid N+1 Queries

```javascript
// ❌ Bad: N+1 queries
async function getUsersWithEmails() {
  const users = await db("users").select();
  const results = [];

  for (const user of users) {
    const emails = await db("emails").where({ user_id: user.id });
    results.push({ ...user, emails });
  }

  return results;
}

// ✅ Good: Single query with joins
async function getUsersWithEmails() {
  return db("users")
    .select("users.*", db.raw("json_agg(emails.*) as emails"))
    .leftJoin("emails", "users.id", "emails.user_id")
    .groupBy("users.id");
}

// ✅ Alternative: Batch loading
async function getUsersWithEmails(userIds) {
  const users = await db("users").whereIn("id", userIds);
  const emailsByUserId = await db("emails")
    .whereIn("user_id", userIds)
    .then((emails) => {
      const map = {};
      emails.forEach((email) => {
        if (!map[email.user_id]) map[email.user_id] = [];
        map[email.user_id].push(email);
      });
      return map;
    });

  return users.map((user) => ({
    ...user,
    emails: emailsByUserId[user.id] || [],
  }));
}
```

### Select Only Needed Columns

```javascript
// ❌ Bad: Fetch all columns
const users = await db("users").select();

// ✅ Good: Select only needed columns
const users = await db("users").select("id", "email", "name");
```

### Pagination for Large Result Sets

```javascript
async function getUsers(page = 1, pageSize = 50) {
  const offset = (page - 1) * pageSize;

  const [users, total] = await Promise.all([
    db("users")
      .select("id", "email", "name")
      .orderBy("created_at", "desc")
      .limit(pageSize)
      .offset(offset),
    db("users").count("* as count").first(),
  ]);

  return {
    data: users,
    pagination: {
      page,
      pageSize,
      total: total.count,
      pages: Math.ceil(total.count / pageSize),
    },
  };
}
```

### Database Indexing

Create indexes on frequently queried columns:

```javascript
// Migration file
export async function up(knex) {
  await knex.schema.table("users", (table) => {
    table.index("email"); // Index on email column
    table.index("status"); // Index on status column
    table.index(["status", "created_at"]); // Composite index
  });
}

export async function down(knex) {
  await knex.schema.table("users", (table) => {
    table.dropIndex("email");
    table.dropIndex("status");
    table.dropIndex(["status", "created_at"]);
  });
}
```

## Monitoring Performance

### Log Key Metrics

```javascript
import { getLogger } from "./common/logger-pino.mjs";

const log = getLogger({ module: "migration-service" });

async function processMigrationBatch(batchId) {
  const startTime = Date.now();
  let processedCount = 0;
  let failedCount = 0;

  try {
    const batch = await getBatch(batchId);

    for (const account of batch.accounts) {
      try {
        await migrateAccount(account);
        processedCount++;
      } catch (error) {
        failedCount++;
        log.error({ accountId: account.id, error: error.message });
      }
    }

    const duration = Date.now() - startTime;
    log.info(
      {
        batchId,
        processedCount,
        failedCount,
        duration,
        throughput: Math.round((processedCount / duration) * 1000),
      },
      "Batch processed"
    );
  } catch (error) {
    log.error({ batchId, error: error.message }, "Batch processing failed");
  }
}
```

## Performance Checklist

- ✅ Batch process large datasets (100-1000 items per batch)
- ✅ Reuse database connections via connection pool
- ✅ Cache frequently accessed data with Redis and TTL
- ✅ Use `Promise.all()` for independent operations
- ✅ Avoid N+1 queries (use joins or batch loading)
- ✅ Select only needed columns
- ✅ Paginate large result sets
- ✅ Index frequently queried columns
- ✅ Monitor key metrics (throughput, error rate, latency)
- ✅ Use parallel processing where possible
