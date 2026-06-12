---
name: Security - Authentication and Secrets Check
description: Detects authentication bypasses, hardcoded secrets, and improper credential management.
applyTo: "**/*.{js,ts,jsx,tsx,mjs,cjs}"
---

# Security - Authentication and Secrets Check

## Context

This check identifies security issues related to authentication mechanisms and secret management in the OneMailMigration codebase, focusing on:

- Hardcoded credentials and API keys
- Insecure authentication flows
- Improper credential storage
- Missing authentication in internal APIs
- Session management issues

These vulnerabilities could allow attackers to access sensitive data or perform actions as other users.

## What to Check

### 1. Hardcoded Secrets

**Bad Pattern:**

```javascript
// Hardcoded database credentials
const dbConfig = {
  host: "db.example.com",
  user: "admin",
  password: "supersecret123",
};

// Hardcoded API keys
const BAC_API_KEY = "sk-1234567890abcdef";
const OX_API_KEY = "ox-key-secret";
```

**Good Pattern:**

```javascript
// Use environment variables
const dbConfig = {
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
};

// Use secret management
const BAC_API_KEY = process.env.BAC_API_KEY;
const OX_API_KEY = process.env.OX_API_KEY;
```

**Key Files to Check:**

- All `.mjs` files
- Configuration files
- Files containing `password`, `secret`, `key`, `token`, `apiKey`

### 2. Authentication Bypass

**Bad Pattern:**

```javascript
// Missing authentication check
app.get("/api/admin/users", async (req, res) => {
  const users = await db.users.findAll();
  res.json(users);
});

// Insecure admin check
if (user.role === "admin" || user.email === "admin@example.com") {
  // grant access
}
```

**Good Pattern:**

```javascript
// Proper authentication middleware
app.get("/api/admin/users", authenticate, authorize("admin"), async (req, res) => {
  const users = await db.users.findAll();
  res.json(users);
});

// Use proper role-based access control
if (user.roles.includes("admin")) {
  // grant access
}
```

**Key Files to Check:**

- API endpoint files
- Route handlers
- Middleware files
- Files with `/api/` or `/admin/` paths

### 3. Insecure Credential Storage

**Bad Pattern:**

```javascript
// Storing passwords in plain text
await db.users.insert({
  email: user.email,
  password: user.password, // plain text password
});

// Using weak hashing
const hash = crypto.createHash("md5").update(password).digest("hex");
```

**Good Pattern:**

```javascript
// Proper password hashing
const hash = await bcrypt.hash(password, 12);

// Secure storage
await db.users.insert({
  email: user.email,
  passwordHash: hash,
  passwordSalt: salt,
});
```

**Key Files to Check:**

- Authentication files
- User management files
- Files with `password`, `hash`, `encrypt`

### 4. Missing Input Validation in Authentication

**Bad Pattern:**

```javascript
// No validation on login
const { email, password } = req.body;
const user = await authenticateUser(email, password);
```

**Good Pattern:**

```javascript
// Proper validation
const { email, password } = req.body;

if (!isValidEmail(email)) {
  throw new ValidationError("Invalid email format");
}

if (!isValidPassword(password)) {
  throw new ValidationError("Password must be 8-64 characters");
}

const user = await authenticateUser(email, password);
```

**Key Files to Check:**

- Login handlers
- Authentication middleware
- Files with `login`, `authenticate`, `signin`

### 5. Session Management Issues

**Bad Pattern:**

```javascript
// Infinite session lifetime
res.cookie("session", token, { maxAge: 365 * 24 * 60 * 60 * 1000 });

// No secure flag
res.cookie("session", token);
```

**Good Pattern:**

```javascript
// Proper session configuration
res.cookie("session", token, {
  maxAge: 24 * 60 * 60 * 1000, // 24 hours
  secure: true,
  httpOnly: true,
  sameSite: "strict",
});
```

**Key Files to Check:**

- Session middleware
- Authentication handlers
- Files with `cookie`, `session`, `setCookie`

### 6. Credential Rotation and Management

**Bad Pattern:**

```javascript
// No rotation mechanism
const API_KEY = "fixed-key-123";

// Hardcoded in multiple places
const key1 = "secret-key";
const key2 = "secret-key"; // duplicate
```

**Good Pattern:**

```javascript
// Use environment variables with rotation support
const API_KEY = process.env.API_KEY;

// Centralized credential management
const credentials = {
  bac: process.env.BAC_API_KEY,
  ox: process.env.OX_API_KEY,
  soap: process.env.SOAP_CREDENTIALS,
};
```

**Key Files to Check:**

- Configuration files
- Credential management files
- Files with multiple hardcoded secrets

## Exclusions

This check does NOT apply to:

- Test files in `__tests__/` directory
- Generated files in `openapi/generators/`
- Sample configuration files with `.example` or `.template` extensions
- Documentation files

## Severity

- **Critical**: Hardcoded secrets in production code
- **High**: Missing authentication on sensitive endpoints
- **Medium**: Weak credential storage practices
- **Low**: Potential authentication bypass vectors needing review
