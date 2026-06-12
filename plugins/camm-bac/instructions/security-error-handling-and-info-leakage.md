--- 
name: Security - Error Handling and Information Leakage Check
description: Detects insecure error handling patterns and information leakage vulnerabilities.
---

# Security - Error Handling and Information Leakage Check

## Context

This check identifies security issues related to error handling and information disclosure in the OneMailMigration codebase, focusing on:
- Stack traces exposed to users
- Sensitive information in error messages
- Improper error handling in APIs
- Missing error boundaries
- Information leakage through logs

These vulnerabilities could expose internal implementation details to attackers or disclose sensitive information about the system.

## What to Check

### 1. Stack Trace Exposure

**Bad Pattern:**
```javascript
// Exposing stack traces to clients
app.get('/api/user/:id', async (req, res) => {
    try {
        const user = await getUser(req.params.id)
        res.json(user)
    } catch (error) {
        res.status(500).json({ error: error.stack })
    }
})

// Global error handler exposing details
app.use((err, req, res, next) => {
    res.status(500).json({
        message: err.message,
        stack: err.stack
    })
})
```

**Good Pattern:**
```javascript
// Safe error handling
app.get('/api/user/:id', async (req, res) => {
    try {
        const user = await getUser(req.params.id)
        res.json(user)
    } catch (error) {
        logger.error(error)
        res.status(500).json({ error: 'Internal server error' })
    }
})

// Proper global error handler
app.use((err, req, res, next) => {
    logger.error(err)
    res.status(500).json({
        error: 'An error occurred',
        code: err.code || 'INTERNAL_ERROR'
    })
})
```

**Key Files to Check:**
- API endpoint files
- Route handlers
- Error middleware files
- Files with `catch`, `try/catch`, `error`

### 2. Sensitive Information in Error Messages

**Bad Pattern:**
```javascript
// Exposing database errors
catch (error) {
    res.status(500).json({ error: error.message })
    // error.message might contain SQL details
}

// Exposing file paths
catch (error) {
    logger.error(`Failed to read file: ${error.path}`)
}
```

**Good Pattern:**
```javascript
// Generic error messages
catch (error) {
    logger.error('Database operation failed', { error: error.message })
    res.status(500).json({ error: 'Database operation failed' })
}

// Safe logging
catch (error) {
    logger.error('File operation failed', { 
        fileOperation: 'read',
        errorCode: error.code
    })
}
```

**Key Files to Check:**
- All files with error handling
- Database operation files
- File system operation files
- Files with `logger.error`, `console.error`

### 3. Missing Error Boundaries

**Bad Pattern:**
```javascript
// No error handling in async functions
async function processMigration() {
    await step1()
    await step2()
    await step3()
}

// Unhandled promise rejections
Promise.all([task1(), task2(), task3()])
```

**Good Pattern:**
```javascript
// Proper error handling
async function processMigration() {
    try {
        await step1()
        await step2()
        await step3()
    } catch (error) {
        logger.error('Migration failed', { error })
        throw new MigrationError('Migration process failed')
    }
}

// Handled promise rejections
Promise.all([task1(), task2(), task3()]).catch(error => {
    logger.error('Parallel tasks failed', { error })
})
```

**Key Files to Check:**
- Worker files
- Migration process files
- Files with `async/await` or `Promise`

### 4. Information Leakage Through Logs

**Bad Pattern:**
```javascript
// Logging sensitive data
logger.info('User logged in', {
    userId: user.id,
    email: user.email,
    password: user.password  // Sensitive!
})

// Logging API keys
logger.debug('API configuration', {
    apiKey: process.env.API_KEY,
    dbPassword: process.env.DB_PASSWORD
})
```

**Good Pattern:**
```javascript
// Safe logging
logger.info('User logged in', {
    userId: user.id,
    email: user.email
})

// Mask sensitive data
logger.debug('API configuration loaded', {
    apiKey: '***',
    dbPassword: '***',
    service: 'BAC API'
})
```

**Key Files to Check:**
- All files with logging statements
- Files with `logger.`, `console.`, `pino`
- Files with `info`, `debug`, `log`, `warn`, `error`

### 5. Improper Error Handling in SOAP

**Bad Pattern:**
```javascript
// Exposing SOAP errors
const result = await soapClient.callMethod(args)
if (result.error) {
    throw new Error(result.error.message)
}
```

**Good Pattern:**
```javascript
// Safe SOAP error handling
try {
    const result = await soapClient.callMethod(args)
    if (result.error) {
        logger.error('SOAP call failed', { 
            errorCode: result.error.code,
            soapFault: result.error.fault
        })
        throw new SoapError('SOAP operation failed')
    }
    return result
} catch (error) {
    logger.error('SOAP communication error', { error })
    throw new SoapError('SOAP service unavailable')
}
```

**Key Files to Check:**
- SOAP client files
- Files with `soap` library usage
- Files in `lib/soap/` directory

### 6. Error Handling in Message Queues

**Bad Pattern:**
```javascript
// No error handling in queue processors
queue.process('migration', async (job) => {
    await migrateJob(job.data)
})
```

**Good Pattern:**
```javascript
// Proper queue error handling
queue.process('migration', async (job) => {
    try {
        await migrateJob(job.data)
        job.progress(100)
    } catch (error) {
        logger.error('Job processing failed', { 
            jobId: job.id,
            error: error.message
        })
        throw error  // Let BullMQ handle retries
    }
})
```

**Key Files to Check:**
- Queue worker files
- Files in `lib/mq/` directory
- Files with `BullMQ`, `queue.process`

## Exclusions

This check does NOT apply to:
- Test files in `__tests__/` directory
- Generated files in `openapi/generators/`
- Mock files and test utilities

## Severity

- **Critical**: Stack traces or sensitive data exposed to users
- **High**: Information leakage in logs or error messages
- **Medium**: Missing error boundaries in critical paths
- **Low**: Potential information disclosure needing review