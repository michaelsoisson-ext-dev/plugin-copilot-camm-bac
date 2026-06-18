# Security Practices

## Secrets and Configuration Management

Never hardcode secrets. Always use environment variables for sensitive configuration.

### Environment Variable Configuration

Centralize all configuration in one place:

```javascript
// lib/config.mjs
const config = {
  bac: {
    apiUrl: process.env.BAC_API_URL,
    apiKey: process.env.BAC_API_KEY,
    timeout: parseInt(process.env.BAC_TIMEOUT || "30000"),
  },
  ox: {
    apiUrl: process.env.OX_API_URL,
    apiKey: process.env.OX_API_KEY,
    timeout: parseInt(process.env.OX_TIMEOUT || "30000"),
  },
  db: {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
  },
  redis: {
    host: process.env.REDIS_HOST || "localhost",
    port: parseInt(process.env.REDIS_PORT || "6379"),
    password: process.env.REDIS_PASSWORD,
  },
};

export default config;
```

### Configuration Validation

Validate required configuration on startup:

```javascript
// lib/config.mjs (continued)
function validateConfig() {
  const required = [
    "BAC_API_URL",
    "BAC_API_KEY",
    "DB_HOST",
    "DB_USER",
    "DB_PASSWORD",
    "DB_NAME",
  ];

  const missing = required.filter((key) => !process.env[key]);

  if (missing.length > 0) {
    throw new Error(`Missing required environment variables: ${missing.join(", ")}`);
  }
}

validateConfig();
export default config;
```

## Input Validation

Always validate input at request boundaries.

### Request Validation

```javascript
import { ValidationError } from "../lib/errors";

async function createUser(request, reply) {
  const { email, password, name } = request.body;

  // Validate required fields
  if (!email || !password || !name) {
    throw new ValidationError("Missing required fields: email, password, name");
  }

  // Validate field types
  if (typeof email !== "string" || typeof password !== "string") {
    throw new ValidationError("Email and password must be strings");
  }

  // Validate field formats
  if (!isValidEmail(email)) {
    throw new ValidationError("Invalid email format");
  }

  if (password.length < 8) {
    throw new ValidationError("Password must be at least 8 characters");
  }

  // Create user
  const user = await userService.createUser(email, password, name);
  reply.status(201).send(user);
}

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}
```

### Schema Validation with Joi or Similar

Use a validation library for complex schemas:

```javascript
import Joi from "joi";

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).max(100).required(),
  name: Joi.string().min(1).max(100).required(),
  age: Joi.number().integer().min(0).max(150).optional(),
});

async function createUser(request, reply) {
  const { error, value } = userSchema.validate(request.body);

  if (error) {
    throw new ValidationError("Validation failed", {
      details: error.details,
    });
  }

  const user = await userService.createUser(value);
  reply.status(201).send(user);
}
```

## Input Sanitization

Clean and normalize user input before storage:

```javascript
import { ValidationError } from "../lib/errors";

function sanitizeEmail(email) {
  return email.toLowerCase().trim();
}

function sanitizeInput(str) {
  if (typeof str !== "string") {
    return "";
  }
  // Remove leading/trailing whitespace and dangerous characters
  return str.trim().substring(0, 1000);
}

async function createUser(request, reply) {
  const { email, password, name } = request.body;

  const sanitized = {
    email: sanitizeEmail(email),
    name: sanitizeInput(name),
    password: password, // Don't sanitize—hash as-is
  };

  // Validate sanitized values
  if (!sanitized.email || !sanitized.name) {
    throw new ValidationError("Email and name cannot be empty");
  }

  const user = await userService.createUser(sanitized);
  reply.status(201).send(user);
}
```

## Authentication and Authorization

### JWT Tokens

Never log or expose tokens. Use secure HTTP-only cookies or Authorization headers:

```javascript
import jwt from "@fastify/jwt";

export async function registerAuthRoutes(fastify) {
  fastify.register(jwt, {
    secret: process.env.JWT_SECRET,
    sign: {
      expiresIn: "7d",
    },
  });

  fastify.post("/login", async (request, reply) => {
    const { email, password } = request.body;

    // Validate and authenticate user
    const user = await userService.authenticateUser(email, password);

    if (!user) {
      return reply.status(401).send({
        error: "Invalid credentials",
      });
    }

    // Create token (never log it!)
    const token = fastify.jwt.sign({
      userId: user.id,
      email: user.email,
    });

    // ✅ Good: Send token in Authorization header
    reply.header("Authorization", `Bearer ${token}`);
    reply.send({ success: true });

    // ❌ Bad: Never log tokens
    // logger.info({ token }); // DON'T DO THIS
  });
}
```

### Route Protection

```javascript
export async function registerProtectedRoutes(fastify) {
  fastify.get("/profile", async (request, reply) => {
    // Verify token automatically
    await request.jwtVerify();

    const user = await userService.getUserById(request.user.userId);
    reply.send(user);
  });
}
```

## SQL Injection Prevention

Knex automatically parameterizes queries—never use string concatenation:

```javascript
// ✅ Safe: Parameterized query
const user = await db("users")
  .where({ email, status: "active" })
  .first();

// ❌ NEVER: Raw SQL concatenation (security risk)
const user = await db.raw(`SELECT * FROM users WHERE email = '${email}'`);
```

## Rate Limiting

Protect endpoints from abuse with rate limiting:

```javascript
import rateLimit from "@fastify/rate-limit";

export async function registerRateLimit(fastify) {
  fastify.register(rateLimit, {
    max: 100, // 100 requests
    timeWindow: "15 minutes",
  });

  // Custom rate limit for login attempts
  fastify.post(
    "/login",
    {
      config: {
        rateLimit: {
          max: 5,
          timeWindow: "15 minutes",
        },
      },
    },
    async (request, reply) => {
      // Login logic
    }
  );
}
```

## CORS Configuration

Restrict allowed origins to prevent unauthorized cross-origin requests:

```javascript
import cors from "@fastify/cors";

export async function registerCors(fastify) {
  fastify.register(cors, {
    origin: process.env.ALLOWED_ORIGINS?.split(",") || ["http://localhost:3000"],
    credentials: true,
  });
}
```

## Error Messages

Return generic error messages to clients, log detailed errors server-side:

```javascript
// ✅ Good: Generic message to client, detailed log
async function createUser(request, reply) {
  try {
    const user = await userService.createUser(request.body);
    reply.send(user);
  } catch (error) {
    // Log detailed error server-side
    logger.error({
      error: error.message,
      stack: error.stack,
      userId: request.user?.id,
    });

    // Send generic message to client (don't leak info)
    reply.status(500).send({
      error: "An error occurred while creating the user",
      code: "USER_CREATION_FAILED",
    });
  }
}
```

## Sensitive Data in Logs

Never log passwords, tokens, API keys, or PII:

```javascript
// ✅ Good: Log only safe fields
logger.info({
  userId: user.id,
  email: user.email,
  action: "user_created",
});

// ❌ Bad: Never log sensitive fields
// logger.info({ userId, password: user.password, apiKey: config.apiKey });
```

## HTTPS/TLS

Always use HTTPS in production:

```javascript
import fastify from "fastify";
import fs from "fs";

const server = fastify({
  https: {
    key: fs.readFileSync("/path/to/key.pem"),
    cert: fs.readFileSync("/path/to/cert.pem"),
  },
});
```

## Database Connection Security

```javascript
// ✅ Good: Use connection pool with SSL
const knex = require("knex")({
  client: "mysql2",
  connection: {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
    ssl: "require", // Enforce SSL
  },
  pool: {
    min: 2,
    max: 10,
  },
});
```

## Security Checklist

- ✅ All secrets in environment variables (never hardcoded)
- ✅ Input validation at all request boundaries
- ✅ Input sanitization before storage
- ✅ SQL injection prevention (use parameterized queries)
- ✅ Authentication with JWT or sessions
- ✅ Rate limiting on sensitive endpoints
- ✅ CORS properly configured
- ✅ Generic error messages to clients
- ✅ Detailed logs server-side only
- ✅ Never log sensitive data (passwords, tokens, PII)
- ✅ HTTPS/TLS in production
- ✅ Database connections over SSL
