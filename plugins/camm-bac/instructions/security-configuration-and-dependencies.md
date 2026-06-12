---
name: Security - Configuration and Dependencies Check
description: Detects insecure configuration practices and vulnerable dependencies.
applyTo: "**/*.{js,ts,jsx,tsx,mjs,cjs},package.json,.env*"
---

# Security - Configuration and Dependencies Check

## Context

This check identifies security issues related to configuration management and dependency vulnerabilities in the OneMailMigration codebase, focusing on:

- Insecure default configurations
- Missing security headers
- Outdated or vulnerable dependencies
- Improper dependency configuration
- Missing security-related middleware

These vulnerabilities could expose the application to various attacks or compromise system security.

## What to Check

### 1. Insecure Default Configurations

**Bad Pattern:**

```javascript
// Development mode in production
app.set("env", "development");

// Insecure CORS configuration
app.use(
  cors({
    origin: "*", // Allow all origins
  }),
);

// No rate limiting
app.use(express.json());
```

**Good Pattern:**

```javascript
// Proper environment configuration
const env = process.env.NODE_ENV || "production";
app.set("env", env);

// Secure CORS configuration
app.use(
  cors({
    origin: process.env.ALLOWED_ORIGINS?.split(",") || [],
    methods: ["GET", "POST", "PUT", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
  }),
);

// Add rate limiting
const rateLimit = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
});
app.use(rateLimit);
```

**Key Files to Check:**

- Main application files
- Server configuration files
- Files with `app.set`, `app.use`

### 2. Missing Security Headers

**Bad Pattern:**

```javascript
// No security headers
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
```

**Good Pattern:**

```javascript
// Proper security headers
app.use(helmet());
app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "'unsafe-inline'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:"],
      fontSrc: ["'self'"],
      objectSrc: ["'none'"],
      upgradeInsecureRequests: [],
    },
  }),
);
```

**Key Files to Check:**

- Main application files
- Security middleware files
- Files with `helmet`, `cors`, `express`

### 3. Dependency Vulnerabilities

**Bad Pattern:**

```javascript
// Outdated dependencies
"dependencies": {
    "express": "4.16.0",  // Old version
    "lodash": "4.17.15"   // Known vulnerabilities
}
```

**Good Pattern:**

```javascript
// Updated dependencies
"dependencies": {
    "express": "4.18.2",
    "lodash": "4.17.21"
}
```

**Key Files to Check:**

- `package.json`
- `package-lock.json`
- `npm-shrinkwrap.json`

### 4. Improper Dependency Configuration

**Bad Pattern:**

```javascript
// Using deprecated APIs
const crypto = require("crypto");
const hash = crypto.createHash("md5").update(data).digest("hex");

// Insecure session configuration
app.use(
  session({
    secret: "keyboard cat",
    resave: false,
    saveUninitialized: true,
  }),
);
```

**Good Pattern:**

```javascript
// Using secure alternatives
const { createHash } = require("crypto");
const hash = createHash("sha256").update(data).digest("hex");

// Secure session configuration
app.use(
  session({
    secret: process.env.SESSION_SECRET,
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: process.env.NODE_ENV === "production",
      httpOnly: true,
      sameSite: "strict",
    },
  }),
);
```

**Key Files to Check:**

- All files using external libraries
- Files with `require` or `import` statements
- Files with security-sensitive operations

### 5. Missing Security Middleware

**Bad Pattern:**

```javascript
// No CSRF protection
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
```

**Good Pattern:**

```javascript
// Complete security middleware stack
app.use(express.json({ limit: "10kb" }));
app.use(express.urlencoded({ extended: true, limit: "10kb" }));
app.use(helmet());
app.use(cors(corsOptions));
app.use(rateLimit);
app.use(csrf());
app.use(xss());
```

**Key Files to Check:**

- Main application files
- Security configuration files
- Files with middleware setup

### 6. Insecure File Upload Configuration

**Bad Pattern:**

```javascript
// No file type validation
app.post("/upload", upload.any(), (req, res) => {
  // process files
});
```

**Good Pattern:**

```javascript
// Secure file upload
const fileFilter = (req, file, cb) => {
  if (file.mimetype === "image/jpeg" || file.mimetype === "image/png") {
    cb(null, true);
  } else {
    cb(new Error("Invalid file type"), false);
  }
};

app.post("/upload", upload.fields([{ name: "avatar", maxCount: 1, fileFilter }]), (req, res) => {
  // process files
});
```

**Key Files to Check:**

- File upload handlers
- Files with `multer`, `busboy`, `formidable`
- Files with `/upload` routes

## Exclusions

This check does NOT apply to:

- Test files in `__tests__/` directory
- Generated files in `openapi/generators/`
- Sample configuration files
- Documentation files

## Severity

- **Critical**: Known vulnerable dependencies in production
- **High**: Missing critical security headers
- **Medium**: Insecure default configurations
- **Low**: Potential security misconfigurations needing review
