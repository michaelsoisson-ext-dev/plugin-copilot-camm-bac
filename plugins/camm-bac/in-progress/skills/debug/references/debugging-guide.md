# Debugging Guide for API & Backend Issues

Comprehensive debugging strategies for the camm-bac backend project (Node.js, MariaDB, BullMQ).

## Table of Contents
1. [Debugging Workflow](#debugging-workflow)
2. [Information Gathering](#information-gathering)
3. [Database Debugging](#database-debugging)
4. [Job Queue Debugging](#job-queue-debugging)
5. [Performance Debugging](#performance-debugging)
6. [Common Issues & Patterns](#common-issues--patterns)

---

## Debugging Workflow

### Phase 1: Information Gathering

**Always start by collecting:**

1. **Issue Description**
   - What's happening (actual behavior)
   - What's expected (desired behavior)
   - When did it start?
   - Is it consistent or intermittent?

2. **Environment Context**
   - Development, staging, or production?
   - Node.js version
   - Database version (MariaDB)
   - Recent code changes (use git history)
   - Container/Docker configuration

3. **Error Information**
   - Full error message and stack trace
   - Error logs (structured and console)
   - Timestamps of when issue occurred
   - Any related log entries (database, job queue)

4. **Reproduction Steps**
   - Exact steps to trigger the issue
   - Frequency (always, sometimes, one-time)
   - Can it be reproduced locally?
   - Do specific inputs trigger it?

### Phase 2: Local Reproduction

**Best case: reproduce locally first**
- Clone the issue scenario
- Use test database with issue data
- Trace execution with console logs or debugger
- Verify the issue occurs
- This confirms the issue isn't environment-specific

If production-only:
- Review production logs carefully
- Compare production vs. staging configurations
- Check for data-dependent issues
- Look at recent deployments

### Phase 3: Root Cause Analysis

**Systematic investigation:**

1. **Examine error messages and stack traces**
   - Which function threw the error?
   - What inputs caused it?
   - Are there try/catch blocks that might be swallowing errors?

2. **Review recent code changes**
   - Git diff against the last working version
   - Did any changes touch related code?
   - Are new dependencies causing issues?

3. **Check configuration**
   - Environment variables set correctly?
   - Database connection parameters valid?
   - Queue configuration correct?
   - Node options appropriate?

4. **Isolate the failing component**
   - Is it API logic, database, or job queue?
   - Test component in isolation
   - Mock external dependencies if needed

5. **Check for resource issues**
   - Memory usage (memory leaks?)
   - CPU usage (blocking operations?)
   - Database connection limits exceeded?
   - File descriptor limits?

### Phase 4: Solution & Prevention

**Once root cause is identified:**

1. **Propose a fix**
   - Address the root cause, not symptoms
   - Keep changes minimal and focused
   - Ensure backward compatibility

2. **Add test case**
   - Unit test for the bug scenario
   - Integration test if applicable
   - Prevent regression

3. **Document findings**
   - Add to project knowledge base
   - Update relevant documentation
   - Note similar patterns to watch for

---

## Information Gathering

### Logs to Collect

**Structured Logs**
```javascript
// Good logs include context
logger.error('User migration failed', {
  userId: '123',
  batchId: 'batch-456',
  error: err.message,
  stack: err.stack,
  timestamp: new Date().toISOString()
});
```

**Check logs for:**
- Error messages (full text, not truncated)
- Stack traces (complete, with line numbers)
- Contextual information (IDs, inputs, state)
- Related warnings or debug logs
- Performance metrics (if applicable)

### Questions to Ask

1. **What changed recently?**
   - Code deployments
   - Configuration changes
   - Database schema changes
   - Dependency updates

2. **Is it user-specific or widespread?**
   - Single user affected?
   - All users?
   - Specific data patterns?

3. **What's the impact?**
   - Does it block other operations?
   - Is it cascading to other services?
   - How many users/records affected?

4. **Is there a pattern?**
   - Time-based (specific time of day)?
   - Data-based (certain values trigger it)?
   - Load-based (happens under high load)?

---

## Database Debugging

### Connection Issues

**Check connection configuration:**
```javascript
// Verify connection string
const connectionString = process.env.DB_CONNECTION_URL;
// Should have: user, password, host, port, database

// Test connectivity
const connection = await db.getConnection();
await connection.ping(); // Succeeds if connection works
```

**Common issues:**
- Credentials wrong (user/password)
- Host unreachable (network/firewall)
- Port wrong
- Database doesn't exist
- Connection pool exhausted

### Query Performance Issues

**Identify slow queries:**

```bash
# Check MariaDB slow query log
SHOW VARIABLES LIKE 'slow_query%';
SHOW VARIABLES LIKE 'long_query_time';

# Enable if not enabled
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1; # Log queries > 1 second
```

**Analyze query performance:**

```sql
-- Use EXPLAIN to understand query execution
EXPLAIN SELECT * FROM users WHERE email = 'test@example.com';

-- Look for:
-- - Full table scan (type: ALL)?
-- - Using filesort?
-- - Large rows examined?
-- - Small rows returned?

-- Good signs:
-- - type: const, eq_ref, ref (uses index)
-- - Small rows examined
-- - Extra: NULL or "Using index"
```

**Common performance issues:**
- Missing indexes on frequently queried columns
- N+1 queries (query in loop, should use JOIN)
- Selecting unnecessary columns
- No pagination on large result sets
- Inefficient WHERE clauses

### Query Errors

**SQL errors to debug:**

```javascript
try {
  const result = await db.query(sql, params);
} catch (error) {
  // Log the actual SQL
  console.error('SQL Error:', error.message);
  console.error('Query:', sql);
  console.error('Params:', params);
  console.error('Error code:', error.code); // ER_DUP_ENTRY, etc.
}
```

**Common SQL errors:**
- `ER_NO_SUCH_TABLE`: Table doesn't exist
- `ER_BAD_FIELD_ERROR`: Column name wrong
- `ER_DUP_ENTRY`: Duplicate key on unique column
- `ER_SYNTAX_ERROR`: Malformed SQL

### Checking Data Issues

**Verify data integrity:**

```sql
-- Check for NULL values where not expected
SELECT COUNT(*) FROM users WHERE email IS NULL;

-- Check for duplicate keys
SELECT email, COUNT(*) as count FROM users 
GROUP BY email HAVING count > 1;

-- Check value ranges
SELECT MIN(age), MAX(age), AVG(age) FROM users;

-- Check for orphaned records
SELECT * FROM orders 
WHERE user_id NOT IN (SELECT id FROM users);
```

---

## Job Queue Debugging

### Queue Status

**Check BullMQ queue state:**

```javascript
// Get queue stats
const stats = await queue.getStats();
console.log('Active jobs:', stats.active);
console.log('Completed jobs:', stats.completed);
console.log('Failed jobs:', stats.failed);
console.log('Waiting jobs:', stats.waiting);

// Get job details
const job = await queue.getJob(jobId);
console.log('Status:', job.status);
console.log('Data:', job.data);
console.log('Progress:', job.progress);
console.log('Attempts:', job.attempts);
console.log('Error:', job.failedReason);
```

### Job Processing Failures

**Debug failed jobs:**

```javascript
// Get failed jobs
const failedJobs = await queue.getFailed(0, -1); // All failed jobs

failedJobs.forEach(job => {
  console.log('Job ID:', job.id);
  console.log('Data:', job.data);
  console.log('Failed reason:', job.failedReason);
  console.log('Stack:', job.stacktrace);
  console.log('Attempts:', job.attempts);
});
```

**Common job queue issues:**
- Job timeout (long_process timeout exceeded)
- Worker crashed (unhandled exception)
- Job data invalid (wrong format, missing fields)
- Retry logic exhausted (max attempts reached)
- Redis connection lost
- Queue initialization failed

### Worker Issues

**Check worker health:**

```javascript
const worker = new Worker('queueName', async (job) => {
  // Log job start
  console.log('Processing job:', job.id, job.data);
  
  try {
    // Do work
    await processJob(job.data);
    console.log('Job completed:', job.id);
  } catch (error) {
    console.error('Job failed:', job.id, error.message);
    throw error; // Let BullMQ handle retry
  }
});

worker.on('error', (err) => {
  console.error('Worker error:', err);
});

worker.on('failed', (job, err) => {
  console.error('Job failed permanently:', job.id, err.message);
});
```

**Worker debugging tips:**
- Ensure error handling is correct (throw vs. return)
- Check job timeout (default 30s)
- Verify worker process is running (docker, PM2, etc.)
- Check for resource exhaustion (memory, connections)
- Look for unhandled promise rejections

---

## Performance Debugging

### Identifying Bottlenecks

**Database performance issues:**
- Monitor slow query logs (see Database Debugging section)
- Use EXPLAIN to analyze query plans
- Check indexes on frequently queried columns
- Look for N+1 query patterns

**Application performance:**
- Memory profiling: Look for memory leaks
- CPU profiling: Identify hot paths
- Request timing: Which endpoints are slow?
- Third-party API calls: Slow external dependencies?

**Job queue performance:**
- Queue depth: Are jobs piling up?
- Job processing time: How long per job?
- Worker throughput: Jobs per second?
- Concurrency: Too many/few concurrent workers?

### Performance Analysis Pattern

```javascript
// Measure operation timing
const startTime = Date.now();
const result = await expensiveOperation();
const duration = Date.now() - startTime;

console.log('Operation took', duration, 'ms');

// Set alerts for slow operations
if (duration > 1000) {
  logger.warn('Slow operation detected', {
    operation: 'expensiveOperation',
    durationMs: duration,
    threshold: 1000
  });
}
```

### Resource Monitoring

**Check system resources:**

```bash
# Docker
docker stats <container-name>

# Node.js process
ps aux | grep node
free -h  # Memory
top      # CPU usage

# Database
SHOW PROCESSLIST;  # Current queries
SHOW STATUS;       # Server metrics
```

**Common resource issues:**
- Memory leak (process keeps growing)
- Connection leak (connections not closed)
- File descriptor leak (too many open files)
- CPU spike (blocking operations, infinite loops)

---

## Common Issues & Patterns

### Debugging API Endpoint Failures

**Template for investigating endpoint issues:**

```javascript
// 1. Log request details
app.get('/api/users/:id', (req, res) => {
  console.log('Request:', {
    method: req.method,
    path: req.path,
    params: req.params,
    query: req.query,
    headers: req.headers
  });

  // 2. Try to parse/validate inputs
  if (!req.params.id) {
    return res.status(400).json({ error: 'ID required' });
  }

  // 3. Log database operations
  try {
    const user = await db.findUserById(req.params.id);
    if (!user) {
      console.log('User not found:', req.params.id);
      return res.status(404).json({ error: 'User not found' });
    }

    // 4. Return response
    res.json(user);
  } catch (error) {
    // 5. Log error with full context
    console.error('Endpoint error:', {
      endpoint: '/api/users/:id',
      params: req.params,
      error: error.message,
      stack: error.stack
    });
    res.status(500).json({ error: 'Internal server error' });
  }
});
```

### Debugging Database Connection Issues

**Check MariaDB connection:**

```javascript
const mysql = require('mysql2/promise');

// Test connection
try {
  const connection = await mysql.createConnection({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME
  });
  
  const result = await connection.execute('SELECT 1');
  console.log('Connection successful');
  await connection.end();
} catch (error) {
  console.error('Connection failed:', error.message);
  console.error('Host:', process.env.DB_HOST);
  console.error('User:', process.env.DB_USER);
  // Don't log password!
}
```

### Debugging Job Queue Issues

**Check queue and worker status:**

```javascript
const Queue = require('bullmq').Queue;
const Worker = require('bullmq').Worker;

// 1. Verify queue exists and is accessible
const queue = new Queue('myQueue', {
  connection: {
    host: process.env.REDIS_HOST,
    port: process.env.REDIS_PORT
  }
});

// 2. Check queue stats
const stats = await queue.getStats();
console.log('Queue stats:', stats);

// 3. Check for failed jobs
const failedJobs = await queue.getFailed(0, 10);
console.log('Failed jobs:', failedJobs.length);

// 4. Verify worker is running
const worker = new Worker('myQueue', jobHandler, { connection });
console.log('Worker started');

worker.on('completed', (job) => {
  console.log('Job completed:', job.id);
});

worker.on('failed', (job, err) => {
  console.error('Job failed:', job.id, err.message);
});
```

### Checking Error Messages Don't Leak Information

**Secure error handling:**

```javascript
// ❌ WRONG - Exposes internals
catch (error) {
  res.status(500).json({ error: error.stack });
}

// ✅ RIGHT - Generic message to user
catch (error) {
  logger.error('Database query failed', {
    query: sql,
    error: error.message,
    stack: error.stack
  });
  res.status(500).json({ error: 'Database operation failed' });
}
```

---

## Debugging Tools & Utilities

### Node.js Debugger

```bash
# Start with debugger
node --inspect server.js

# Access at chrome://inspect
# Or use VSCode Debug configuration
```

### Logging Best Practices

```javascript
// Use structured logging with context
logger.error('Operation failed', {
  operation: 'migrateUser',
  userId: '123',
  error: err.message,
  stack: err.stack,
  context: { // Extra debugging info
    attempt: 3,
    timeout: 5000
  }
});

// NOT:
console.log('Error:', error); // Vague, no context
console.log(error.stack);      // Raw stack trace
```

### Quick Debug Checklist

- [ ] Enable error logging if not enabled
- [ ] Collect full error message and stack trace
- [ ] Identify recent code/config changes
- [ ] Reproduce locally if possible
- [ ] Check configuration (env vars, credentials)
- [ ] Verify database/queue connectivity
- [ ] Check resource usage (memory, CPU, connections)
- [ ] Review related logs for context
- [ ] Test with minimal reproducible example
- [ ] Verify fix with test case

---

## When to Reference This

Use this guide when:
- Investigating API errors or failures
- Debugging database issues
- Tracking down job queue problems
- Analyzing performance issues
- Finding memory leaks or resource exhaustion
- Reproducing intermittent issues
- Documenting debugging findings
- Training on debugging methodology
