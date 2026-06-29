# Debugging Tools & Commands Reference

Quick reference for tools, commands, and utilities used in debugging the camm-bac backend.

## Node.js & Application Debugging

### Node.js Inspector

```bash
# Start app with inspector
node --inspect server.js

# Headless mode (for remote debugging)
node --inspect-brk server.js

# Access debugger
# Chrome: chrome://inspect
# VSCode: Add debug config (see below)
```

**VSCode Debug Configuration (.vscode/launch.json):**
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${workspaceFolder}/server.js",
      "restart": true,
      "console": "integratedTerminal"
    },
    {
      "type": "node",
      "request": "attach",
      "name": "Attach to Process",
      "port": 9229
    }
  ]
}
```

### Console Logging Levels

```javascript
// Use different levels for different purposes
logger.trace('Detailed trace info');      // Development only
logger.debug('Debug information');        // Development debugging
logger.info('General information');       // Important events
logger.warn('Warning message');           // Warnings, use with caution
logger.error('Error occurred', error);    // Errors that need attention
logger.fatal('Fatal error', error);       // System failures
```

### Quick Debug Output

```javascript
// Pretty print objects
console.table(array);        // For arrays/objects
console.log(JSON.stringify(obj, null, 2));  // Formatted JSON

// Timing operations
console.time('operation');
// ... code to measure ...
console.timeEnd('operation');

// Counting occurrences
console.count('myCounter');
// ... code ...
console.count('myCounter');
console.countReset('myCounter');
```

---

## Docker Debugging

### Container Logs

```bash
# View container logs
docker logs <container-name>
docker logs -f <container-name>        # Follow logs (tail -f)
docker logs --tail 100 <container-name>  # Last 100 lines
docker logs --since 10m <container-name>  # Last 10 minutes

# View container resource usage
docker stats <container-name>
docker stats --no-stream <container-name>  # One-time snapshot
```

### Container Inspection

```bash
# Get container info
docker inspect <container-name>

# Check container status
docker ps -a

# Check container processes
docker top <container-name>

# Execute command in running container
docker exec -it <container-name> bash
docker exec -it <container-name> node -e "console.log(process.version)"
```

### Docker Compose Debugging

```bash
# View logs from all services
docker-compose logs

# View specific service
docker-compose logs <service-name>

# Interactive shell in service
docker-compose exec <service-name> bash

# Check service status
docker-compose ps
```

---

## MariaDB Debugging

### Connection & Status

```bash
# Connect to database
mysql -h <host> -u <user> -p <database>

# Test connection without login
mysql -h <host> -u <user> -p -e "SELECT 1"

# Check server status
mysql -u <user> -p -e "SHOW STATUS"
```

### Query Analysis

```sql
-- Show current queries
SHOW PROCESSLIST;

-- Kill slow query (use Process_ID from PROCESSLIST)
KILL PROCESS_ID;

-- Check query statistics
SHOW STATUS LIKE 'Questions';
SHOW STATUS LIKE 'Slow_queries';

-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;
SET GLOBAL log_queries_not_using_indexes = 'ON';

-- View slow log
SHOW VARIABLES LIKE 'slow_query_log_file';
tail -f /var/log/mysql/slow.log
```

### Query Performance

```sql
-- Analyze query execution plan
EXPLAIN SELECT * FROM users WHERE id = 1;

-- EXPLAIN columns:
-- id: Query sequence
-- select_type: Type of query (SIMPLE, PRIMARY, UNION, etc.)
-- table: Table name
-- type: How table is accessed (ALL, index, const, ref, etc.)
-- possible_keys: Indexes that could be used
-- key: Index actually used
-- key_len: Length of used key
-- ref: Columns compared to index
-- rows: Rows examined
-- Extra: Additional info

-- Good indicators:
-- type: const, eq_ref, ref (not ALL or index)
-- rows: Small number
-- Extra: NULL or "Using index"
```

### Database Operations

```sql
-- Check table size
SELECT TABLE_NAME, ROUND(((data_length + index_length) / 1024 / 1024), 2) AS size_mb
FROM INFORMATION_SCHEMA.TABLES 
WHERE table_schema = 'database_name'
ORDER BY (data_length + index_length) DESC;

-- Check table structure
DESCRIBE table_name;

-- Count rows
SELECT COUNT(*) FROM table_name;

-- Check for locks
SHOW OPEN TABLES WHERE In_use > 0;

-- Show binary logs
SHOW BINARY LOGS;
SHOW MASTER STATUS;
```

### Debugging Connection Issues

```sql
-- Check max connections setting
SHOW VARIABLES LIKE 'max_connections';

-- Check current connection count
SHOW STATUS LIKE 'Threads_connected';

-- Check connection errors
SHOW STATUS LIKE 'Aborted_connections';
SHOW STATUS LIKE 'Aborted_clients';

-- Increase if needed
SET GLOBAL max_connections = 1000;
```

---

## Redis/BullMQ Debugging

### Redis CLI

```bash
# Connect to Redis
redis-cli -h <host> -p <port>

# Basic operations
redis-cli ping               # Test connection
redis-cli dbsize            # Number of keys
redis-cli INFO              # Server info

# Key management
redis-cli KEYS '*'          # All keys
redis-cli KEYS 'job:*'      # Keys matching pattern
redis-cli GET <key>         # Get value
redis-cli DEL <key>         # Delete key
redis-cli FLUSHDB           # Delete all keys in DB
redis-cli EXPIRE <key> 3600 # Set TTL to 3600 seconds
```

### BullMQ Queue Inspection

```javascript
const { Queue } = require('bullmq');

const queue = new Queue('myQueue');

// Get queue stats
const stats = await queue.getStats();
console.log(stats);
// {
//   active: 5,
//   completed: 1000,
//   failed: 3,
//   waiting: 20,
//   ...
// }

// Get specific job
const job = await queue.getJob('job-id');
console.log('Status:', job.status);
console.log('Progress:', job.progress());
console.log('Data:', job.data);
console.log('Error:', job.failedReason);

// Get all failed jobs
const failedJobs = await queue.getFailed(0, -1);
failedJobs.forEach(job => {
  console.log(`Job ${job.id} failed: ${job.failedReason}`);
});

// Retry failed job
const job = await queue.getJob('job-id');
await job.retry();
```

### Clearing Stuck Jobs

```javascript
// Remove all failed jobs
const failedJobs = await queue.getFailed();
for (const job of failedJobs) {
  await job.remove();
}

// Remove all jobs
await queue.clean(0, 0);

// Remove completed jobs older than 1 hour
await queue.clean(3600000, 'completed');
```

---

## Git Debugging

### Find When Issue Was Introduced

```bash
# Binary search for commit introducing bug
git bisect start
git bisect bad HEAD      # Current commit is bad
git bisect good <commit> # Known good commit

# Test current commit
# If broken: git bisect bad
# If working: git bisect good

# Repeat until found

# End bisect
git bisect reset
```

### Check Recent Changes

```bash
# Show changes in last commit
git show HEAD

# Show changes between versions
git diff main..feature-branch

# Show commits affecting file
git log --oneline -- path/to/file

# Show who changed what (blame)
git blame path/to/file

# See when line was changed
git log -p --follow -- path/to/file
```

---

## Performance Profiling

### Node.js Profiling

```javascript
// CPU profiling
const profiler = require('v8-profiler-next');
profiler.startProfiling('app');

// ... code to profile ...

const cpuProfile = profiler.stopProfiling('app');
cpuProfile.export((err, result) => {
  require('fs').writeFileSync('./profile.cpuprofile', result);
  console.log('Profile saved to profile.cpuprofile');
  profiler.deleteAllProfiles();
});
```

### Memory Debugging

```javascript
// Check memory usage
const usage = process.memoryUsage();
console.log({
  rss: Math.round(usage.rss / 1024 / 1024) + ' MB',      // Total memory
  heapTotal: Math.round(usage.heapTotal / 1024 / 1024) + ' MB',
  heapUsed: Math.round(usage.heapUsed / 1024 / 1024) + ' MB',
  external: Math.round(usage.external / 1024 / 1024) + ' MB'
});

// Monitor memory over time
setInterval(() => {
  const used = process.memoryUsage().heapUsed / 1024 / 1024;
  console.log(`Memory: ${Math.round(used)} MB`);
}, 5000);
```

---

## Structured Logging Template

```javascript
// Create a structured logger
const logger = {
  error: (message, context = {}) => {
    console.error(JSON.stringify({
      level: 'ERROR',
      timestamp: new Date().toISOString(),
      message,
      ...context
    }));
  },
  
  warn: (message, context = {}) => {
    console.warn(JSON.stringify({
      level: 'WARN',
      timestamp: new Date().toISOString(),
      message,
      ...context
    }));
  },
  
  info: (message, context = {}) => {
    console.log(JSON.stringify({
      level: 'INFO',
      timestamp: new Date().toISOString(),
      message,
      ...context
    }));
  }
};

// Usage
logger.error('Database query failed', {
  query: 'SELECT * FROM users',
  error: err.message,
  duration: 5000,
  userId: '123'
});
```

---

## Health Check Commands

```bash
# Overall system health
curl http://localhost:3000/health

# Database
curl http://localhost:3000/health/db

# Cache/Redis
curl http://localhost:3000/health/cache

# Job queue
curl http://localhost:3000/health/queue

# All dependencies
curl http://localhost:3000/health/deep
```

---

## Quick Debug Workflow

1. **Collect information**
   ```bash
   docker logs -f <container> &
   docker exec <container> mysql -u user -p -e "SHOW PROCESSLIST;"
   ```

2. **Check recent changes**
   ```bash
   git log --oneline -10
   git diff HEAD~1 <file>
   ```

3. **Reproduce locally**
   ```bash
   npm test -- --testNamePattern="failing test"
   ```

4. **Add debug logging**
   ```javascript
   logger.debug('Debug checkpoint', { variable: value });
   ```

5. **Profile if performance issue**
   ```bash
   node --inspect server.js
   # Then open chrome://inspect
   ```

6. **Check resources**
   ```bash
   docker stats <container>
   mysql -e "SHOW STATUS LIKE '%connections%';"
   redis-cli INFO stats
   ```

7. **Verify fix**
   ```bash
   npm test -- --testNamePattern="fixed test"
   git diff main <branch>
   ```
