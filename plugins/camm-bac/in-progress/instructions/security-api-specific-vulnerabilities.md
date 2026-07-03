---
name: Security - API-Specific Vulnerabilities Check
description: Detects API-specific security issues including REST and SOAP vulnerabilities.
applyTo: "**/*.{js,ts,jsx,tsx,mjs,cjs}"
---

# Security - API-Specific Vulnerabilities Check

## Context

This check identifies security issues specific to REST and SOAP APIs in the OneMailMigration codebase, focusing on:

- Missing API authentication
- Insecure API endpoints
- SOAP-specific vulnerabilities
- API rate limiting issues
- Input validation problems in APIs

These vulnerabilities could allow attackers to access or manipulate API endpoints without proper authorization.

## What to Check

### 1. Missing API Authentication

**Bad Pattern:**

```javascript
// Public API endpoint without authentication
app.get("/api/users/:id", async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json(user);
});

// Admin endpoint without authorization check
app.get("/api/admin/stats", async (req, res) => {
  const stats = await getMigrationStats();
  res.json(stats);
});
```

**Good Pattern:**

```javascript
// Protected API endpoint
app.get("/api/users/:id", authenticate, async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json(user);
});

// Admin endpoint with proper authorization
app.get("/api/admin/stats", authenticate, authorize("admin"), async (req, res) => {
  const stats = await getMigrationStats();
  res.json(stats);
});
```

**Key Files to Check:**

- API endpoint files in `lib/bac/`, `lib/ox/`, `lib/cst/`
- Files with `app.get`, `app.post`, `app.put`, `app.delete`
- Files with `/api/` paths

### 2. Insecure API Endpoints

**Bad Pattern:**

```javascript
// Mass assignment vulnerability
app.put("/api/users/:id", async (req, res) => {
  await db.users.update(req.params.id, req.body);
  res.json({ success: true });
});

// IDOR vulnerability
app.get("/api/messages/:id", async (req, res) => {
  const message = await db.messages.findById(req.params.id);
  res.json(message);
});
```

**Good Pattern:**

```javascript
// Safe mass assignment
app.put("/api/users/:id", async (req, res) => {
  const safeData = pick(req.body, ["name", "email"]); // Only allow specific fields
  await db.users.update(req.params.id, safeData);
  res.json({ success: true });
});

// IDOR protection
app.get("/api/messages/:id", authenticate, async (req, res) => {
  const message = await db.messages.findById(req.params.id);
  if (message.userId !== req.user.id) {
    throw new ForbiddenError("Not authorized to access this message");
  }
  res.json(message);
});
```

**Key Files to Check:**

- All API endpoint files
- Files with `app.put`, `app.post`, `app.patch`
- Files with user data manipulation

### 3. SOAP-Specific Vulnerabilities

**Bad Pattern:**

```javascript
// No SOAP authentication
const client = await soap.createClientAsync(wsdl);
const result = await client.GetUserAsync({ userId: userId });

// Direct XML manipulation
const xml = `<soap:Envelope><soap:Body><GetUser><userId>${userId}</userId></soap:Body></soap:Envelope>`;
```

**Good Pattern:**

```javascript
// Secure SOAP client with authentication
const client = await soap.createClientAsync(wsdl);
client.addSoapHeader({
  Authentication: {
    username: process.env.SOAP_USERNAME,
    password: process.env.SOAP_PASSWORD,
  },
});
const result = await client.GetUserAsync({ userId: userId });

// Safe XML handling
const result = await client.GetUserAsync({ userId: userId });
```

**Key Files to Check:**

- SOAP client files in `lib/soap/`
- Files with `soap.createClientAsync`
- Files with `.xml` extensions

### 4. API Rate Limiting Issues

**Bad Pattern:**

```javascript
// No rate limiting on critical endpoints
app.post("/api/migrate", async (req, res) => {
  await startMigration(req.body);
  res.json({ success: true });
});
```

**Good Pattern:**

```javascript
// Proper rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
});

app.post("/api/migrate", limiter, async (req, res) => {
  await startMigration(req.body);
  res.json({ success: true });
});
```

**Key Files to Check:**

- API endpoint files
- Files with critical operations
- Files with `app.post`, `app.put` for state-changing operations

### 5. Input Validation in APIs

**Bad Pattern:**

```javascript
// No input validation
app.post("/api/migrate", async (req, res) => {
  const { ise, batchSize } = req.body;
  await startMigration(ise, batchSize);
  res.json({ success: true });
});
```

**Good Pattern:**

```javascript
// Proper input validation
app.post("/api/migrate", async (req, res) => {
  const { ise, batchSize } = req.body;

  if (!isValidISE(ise)) {
    throw new ValidationError("Invalid ISE format");
  }

  if (!isValidBatchSize(batchSize)) {
    throw new ValidationError("Batch size must be between 10 and 1000");
  }

  await startMigration(ise, batchSize);
  res.json({ success: true });
});
```

**Key Files to Check:**

- All API endpoint files
- Files with request body processing
- Files with `req.body`, `req.params`, `req.query`

### 6. API Response Security

**Bad Pattern:**

```javascript
// Exposing sensitive data in responses
app.get("/api/user/:id", async (req, res) => {
  const user = await db.users.findById(req.params.id);
  res.json(user); // Might include sensitive fields
});
```

**Good Pattern:**

```javascript
// Safe response handling
app.get("/api/user/:id", async (req, res) => {
  const user = await db.users.findById(req.params.id);
  const safeUser = {
    id: user.id,
    name: user.name,
    email: user.email,
    // Exclude sensitive fields like passwordHash
  };
  res.json(safeUser);
});
```

**Key Files to Check:**

- All API endpoint files
- Files with `res.json`, `res.send`
- Files returning user or sensitive data

## Exclusions

This check does NOT apply to:

- Test files in `__tests__/` directory
- Generated files in `openapi/generators/`
- Mock files and test utilities

## Severity

- **Critical**: Unauthenticated access to sensitive endpoints
- **High**: Missing input validation on critical APIs
- **Medium**: Potential API abuse vectors
- **Low**: API-specific security issues needing review

## Secrets and Configuration

- **Never hardcode secrets**: Use environment variables
- **Input validation**: Always validate at boundaries
- **Sanitization**: Clean data before storage/display

```javascript
// Configuration centralisée
const config = {
  bac: {
    apiUrl: process.env.BAC_API_URL,
    apiKey: process.env.BAC_API_KEY,
    timeout: parseInt(process.env.BAC_TIMEOUT || "30000"),
  },
  db: {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
  },
};
```
