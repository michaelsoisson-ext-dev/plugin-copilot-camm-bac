--- 
name: Security - Injection Vectors Check
description: Detects SQL injection, NoSQL injection, and command injection vulnerabilities in the codebase.
---

# Security - Injection Vectors Check

## Context

This check identifies potential injection vulnerabilities in the OneMailMigration codebase, focusing on:
- SQL injection in Knex queries
- NoSQL injection in MongoDB-like operations
- Command injection in shell operations
- XML injection in SOAP requests

These vulnerabilities are critical as they could allow attackers to execute arbitrary code or access unauthorized data.

## What to Check

### 1. SQL Injection in Knex Queries

**Bad Pattern:**
```javascript
// Direct string concatenation in queries
const userId = req.params.id
const result = await knex('users').where('id = ' + userId)

// Unsafe raw queries
const email = req.body.email
const query = 'SELECT * FROM users WHERE email = ?'
await knex.raw(query, [email])
```

**Good Pattern:**
```javascript
// Use parameterized queries
const userId = req.params.id
const result = await knex('users').where({ id: userId })

// Use Knex query builder
const email = req.body.email
const result = await knex('users').where({ email })
```

**Key Files to Check:**
- `lib/db/**/*.mjs`
- `lib/**/*-repository.mjs`
- Any file using `knex` or `knex.raw()`

### 2. NoSQL Injection

**Bad Pattern:**
```javascript
// Direct object evaluation
const query = { email: req.body.email }
const user = await User.findOne(query)

// String-based queries
const search = req.query.search
const results = await Model.find({ $where: 'this.name.match(/.*' + search + '.*/)' })
```

**Good Pattern:**
```javascript
// Use proper query operators
const email = req.body.email
const user = await User.findOne({ email: email })

// Use regex properly
const search = req.query.search
const results = await Model.find({ name: { $regex: search, $options: 'i' } })
```

### 3. Command Injection

**Bad Pattern:**
```javascript
// Direct shell execution with user input
const filename = req.params.filename
const result = await exec(`cat /tmp/${filename}`)

// Template literals with user input
const userInput = req.body.command
const output = await exec(`${userInput}`)
```

**Good Pattern:**
```javascript
// Use safe alternatives
const filename = req.params.filename
const result = await fs.readFile(`/tmp/${safeFilename}`)

// Validate and sanitize input
const userInput = req.body.command
const safeCommand = validateCommand(userInput)
const output = await exec(safeCommand)
```

**Key Files to Check:**
- Files using `child_process.exec`, `execSync`, `spawn`
- Files using `util.execute` or similar

### 4. XML Injection in SOAP

**Bad Pattern:**
```javascript
// Direct XML construction with user input
const soapRequest = `<soap:Envelope><soap:Body><GetUser><userId>${userId}</userId></soap:Body></soap:Envelope>`

// Unsafe XML templates
const xml = `<request><data>${unsafeData}</data></request>`
```

**Good Pattern:**
```javascript
// Use SOAP libraries properly
const client = await soap.createClientAsync(wsdl)
const result = await client.GetUserAsync({ userId: userId })

// Validate XML content
const safeData = sanitizeXml(unsafeData)
const xml = `<request><data>${safeData}</data></request>`
```

**Key Files to Check:**
- `lib/soap/**/*.mjs`
- Files using `soap` library
- Files with `.xml` extensions

### 5. Input Validation

**Bad Pattern:**
```javascript
// No validation
const email = req.body.email
await sendEmail(email, message)

// Incomplete validation
const age = req.body.age
if (age > 0) { // Only checks if positive
    // process
}
```

**Good Pattern:**
```javascript
// Proper validation
const email = req.body.email
if (!isValidEmail(email)) {
    throw new ValidationError('Invalid email format')
}

// Comprehensive validation
const age = req.body.age
if (!Number.isInteger(age) || age < 0 || age > 120) {
    throw new ValidationError('Age must be between 0 and 120')
}
```

**Key Files to Check:**
- API endpoint files
- Command handlers
- Worker files processing external data

## Exclusions

This check does NOT apply to:
- Test files in `__tests__/` directory
- Generated files in `openapi/generators/`
- Configuration files
- Static asset files

## Severity

- **Critical**: Direct injection vectors with user-controlled input
- **High**: Injection patterns in internal APIs
- **Medium**: Potential injection vectors needing review
- **Low**: Safe patterns with proper parameterization