# Documentation Standards & Best Practices

Comprehensive guide for creating high-quality documentation in the camm-bac project, covering code docs, READMEs, API specs, and more.

## Table of Contents
1. [Code Documentation](#code-documentation)
2. [README.md](#readmemd)
3. [API Documentation](#api-documentation)
4. [Job Queue Documentation](#job-queue-documentation)
5. [Database Documentation](#database-documentation)
6. [Configuration Documentation](#configuration-documentation)
7. [Markdown Formatting](#markdown-formatting)
8. [Documentation Maintenance](#documentation-maintenance)

---

## Code Documentation

### JSDoc Format

Always use JSDoc format for functions, classes, and methods. This enables IDE autocomplete and type checking.

**Function Documentation**

```javascript
/**
 * Creates a new user with the provided email and password.
 * @param {string} email - The user's email address
 * @param {string} password - The user's password (will be hashed)
 * @returns {Promise<User>} The created user object
 * @throws {ValidationError} If email or password is invalid
 * @throws {DuplicateEmailError} If email already exists
 * @example
 * const user = await createUser('john@example.com', 'securePass123');
 */
async function createUser(email, password) {
  // Implementation
}
```

**Class Documentation**

```javascript
/**
 * Service for managing user operations.
 * Handles user creation, retrieval, updates, and deletion.
 * @class UserService
 */
class UserService {
  /**
   * Find user by ID.
   * @param {number} userId - The user ID to search for
   * @returns {Promise<User|null>} The user object or null if not found
   * @throws {DatabaseError} If database query fails
   * @example
   * const user = await service.findById(123);
   * if (!user) console.log('User not found');
   */
  async findById(userId) {
    // Implementation
  }

  /**
   * List all users with optional filtering.
   * @param {Object} options - Query options
   * @param {number} [options.limit=20] - Maximum users to return
   * @param {number} [options.offset=0] - Number of users to skip
   * @param {string} [options.role] - Filter by user role
   * @returns {Promise<User[]>} Array of user objects
   */
  async list(options = {}) {
    // Implementation
  }
}
```

**Common JSDoc Tags**

```javascript
/**
 * @param {type} name - Parameter description
 * @returns {type} Return value description
 * @throws {ErrorType} When this error occurs
 * @example Code example showing usage
 * @deprecated Use newFunction instead
 * @async Indicates function is async
 * @private Internal use only
 * @static Class method, not instance method
 * @readonly Property cannot be modified
 */
```

### Comments vs. Documentation

**Write comments for:**
- **Why** (not what) — Explain reasoning behind implementation
- Complex algorithms or business logic
- Non-obvious implementation choices
- Known workarounds, limitations, or hacks
- Anything that would surprise a code reviewer

```javascript
// Why: Use Set for O(1) lookup vs O(n) for array
const existingIds = new Set(users.map(u => u.id));

// Workaround: Database doesn't support batch updates, so loop manually
for (const item of items) {
  await db.update(item);
}
```

**Avoid comments for:**
- Self-explanatory code (`// Increment counter` on `count++`)
- Repeating what the code does
- Outdated or obvious information
- Dead code (delete it instead)

### What to Document

**Always document:**
- Parameters (types, required/optional, valid values)
- Return types and values
- Exceptions/errors thrown
- Side effects (modified globals, database changes, etc.)
- Complex logic or non-obvious behavior
- External API integrations
- Configuration dependencies

**Example: External API**

```javascript
/**
 * Migrates user data to OX server via SOAP API.
 * Communicates with OX Mail Service on configured host/port.
 * 
 * @param {Object} userData - User data to migrate
 * @param {string} userData.email - User email address
 * @param {string} userData.password - Plain text password (sent via TLS)
 * @returns {Promise<{oxId: string, status: string}>} OX server response
 * @throws {SoapFault} If OX server returns error
 * @throws {TimeoutError} If request exceeds 30s timeout
 * 
 * @example
 * const result = await migrateToOX({
 *   email: 'user@example.com',
 *   password: 'securePass'
 * });
 * console.log('User migrated with OX ID:', result.oxId);
 */
async function migrateToOX(userData) {}
```

---

## README.md

A good README is the first impression of your project. It should answer: "What is this? How do I use it?"

### README Structure

```markdown
# Project Name

Brief one-line description of what the project does.

## Overview

2-3 paragraph explanation of the project's purpose, scope, and key features.

## Quick Start

### Prerequisites
- Node.js 18+
- MariaDB 10.6+
- Docker & Docker Compose

### Installation

```bash
# Clone repository
git clone https://github.com/org/project.git
cd project

# Install dependencies
npm install

# Setup environment
cp .env.example .env
# Edit .env with your configuration

# Start services
docker-compose up -d

# Run migrations
npm run migrate

# Start dev server
npm run dev
```

## Technology Stack

- **Runtime**: Node.js with ESM modules
- **Database**: MariaDB 10.6
- **Queue**: BullMQ with Redis
- **Testing**: Jest
- **API Format**: REST with JSON
- **Documentation**: OpenAPI/Swagger

## Project Structure

```
project/
├── src/
│   ├── api/           # REST API endpoints
│   ├── services/      # Business logic
│   ├── db/            # Database layer
│   ├── jobs/          # BullMQ job handlers
│   └── utils/         # Utilities & helpers
├── tests/             # Test files
├── docs/              # Documentation
├── migrations/        # Database schema changes
├── docker-compose.yml # Services setup
└── .env.example       # Configuration template
```

## Development

### Running Locally

```bash
npm run dev          # Start with auto-reload
npm run build        # Build for production
npm test             # Run tests
npm run test:watch   # Watch mode
npm run test:coverage # Coverage report
```

### Testing

We use Jest for testing. See [TESTING.md](./docs/TESTING.md) for detailed guide.

```bash
npm test -- --testNamePattern="specific test"
npm run test:coverage
```

### Database Migrations

```bash
npm run migrate:create -- -n add_user_table
npm run migrate:up
npm run migrate:down
```

## API Documentation

Full API documentation is available in [API.md](./docs/API.md) or via Swagger at `/api/docs` when running locally.

## Deployment

See [DEPLOYMENT.md](./docs/DEPLOYMENT.md) for production deployment procedures.

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see [LICENSE](./LICENSE) file for details.

## Support

For questions or issues:
- Create an issue on GitHub
- Contact: support@example.com
```

---

## API Documentation

### Endpoint Documentation Template

Document every endpoint systematically:

```markdown
### Create User

**Endpoint**: `POST /api/users`

**Authentication**: Required (Bearer token)

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "name": "John Doe",
  "role": "user"
}
```

**Response** (201 Created):
```json
{
  "id": "usr_123abc",
  "email": "user@example.com",
  "name": "John Doe",
  "role": "user",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

**Error Responses**:

- `400 Bad Request`: Invalid email format or missing required fields
- `409 Conflict`: Email already exists
- `500 Internal Server Error`: Database error
```

### OpenAPI/Swagger Specification

Provide a complete OpenAPI spec if possible:

```yaml
openapi: 3.0.0
info:
  title: Project API
  version: 1.0.0
  description: API for project management

paths:
  /api/users:
    post:
      summary: Create a new user
      tags:
        - Users
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UserCreateRequest'
      responses:
        '201':
          description: User created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '400':
          description: Invalid request body
        '409':
          description: Email already exists

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
          example: "usr_123abc"
        email:
          type: string
          format: email
        name:
          type: string
```

### Request/Response Examples

Always include realistic, executable examples:

```markdown
### List Users with Filtering

**Request**:
```bash
curl -H "Authorization: Bearer token_xyz" \
  "http://localhost:3000/api/users?limit=10&offset=20&role=admin"
```

**Response**:
```json
{
  "data": [
    {
      "id": "usr_001",
      "email": "admin@example.com",
      "role": "admin"
    }
  ],
  "pagination": {
    "total": 150,
    "offset": 20,
    "limit": 10
  }
}
```
```

---

## Job Queue Documentation

### BullMQ Job Documentation

```markdown
## Email Notification Job

**Job Name**: `email-notification`

**Purpose**: Sends transactional emails asynchronously

**Input Data Schema**:
```json
{
  "userId": "usr_123",
  "templateId": "welcome",
  "params": {
    "name": "John",
    "verifyLink": "https://..."
  }
}
```

**Processing Time**: 1-5 seconds (depends on email provider)

**Retry Policy**:
- Attempts: 3 times
- Backoff: Exponential (2s, 10s, 30s)
- Fails after 30 seconds retry timeout

**Error Handling**:
- Network errors: Retry automatically
- Invalid email: Fail immediately with error
- Timeout: Retry with exponential backoff
- Invalid template: Fail immediately

**Example Usage**:
```javascript
const queue = new Queue('email-notification');
await queue.add(
  'email-notification',
  {
    userId: 'usr_123',
    templateId: 'welcome',
    params: { name: 'John' }
  },
  { priority: 10 }
);
```

**Monitoring**:
- Check queue depth: `npm run queue:stats`
- View failed jobs: `npm run queue:failed`
- Retry failed job: `npm run queue:retry -- job-123`
```

---

## Database Documentation

### Schema Documentation

Document database structure clearly:

```markdown
## Database Schema

### Users Table

| Column | Type | Constraints | Description |
|--------|------|-----------|-------------|
| id | VARCHAR(255) | PRIMARY KEY | Unique user identifier |
| email | VARCHAR(255) | UNIQUE, NOT NULL | Email address |
| password_hash | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| name | VARCHAR(255) | NOT NULL | Full name |
| role | ENUM('user','admin') | NOT NULL | User role |
| created_at | TIMESTAMP | NOT NULL | Account creation time |
| updated_at | TIMESTAMP | NOT NULL | Last update time |

**Indexes**:
- PRIMARY KEY (id)
- UNIQUE (email)
- INDEX (role, created_at) for filtering

### Orders Table

| Column | Type | Constraints | Description |
|--------|------|-----------|-------------|
| id | BIGINT | PRIMARY KEY | Order ID |
| user_id | VARCHAR(255) | FOREIGN KEY | User who placed order |
| total | DECIMAL(10,2) | NOT NULL | Order total |
| status | ENUM(...) | NOT NULL | Order status |

**Foreign Keys**:
- user_id → users(id)

**Entity Relationships**:
- User has many Orders (1:N)
```

### Entity Relationship Diagram

Include ER diagram for complex schemas:

```
Users
├─ id (PK)
├─ email
├─ name
└─ role
   │
   └─→ Orders
       ├─ id (PK)
       ├─ user_id (FK)
       ├─ total
       └─ status
```

---

## Configuration Documentation

### Environment Variables

Document all environment variables in `.env.example`:

```bash
# Node Environment
NODE_ENV=development

# Database
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=password
DB_NAME=project_db

# Redis (for BullMQ)
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0

# API Server
API_PORT=3000
API_URL=http://localhost:3000

# SOAP Migration
SOAP_HOST=ox.example.com
SOAP_PORT=8443
SOAP_TIMEOUT_MS=30000

# Email Service
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password

# Security
JWT_SECRET=your-secret-key-change-in-production
SESSION_TIMEOUT_MS=3600000

# Logging
LOG_LEVEL=info
```

### Configuration by Environment

```markdown
### Development Environment

- Database: Local MariaDB
- Cache: Local Redis
- Email: Console output (no sending)
- Workers: Single process

### Production Environment

- Database: Managed MariaDB cluster
- Cache: Redis cluster with replication
- Email: SendGrid or AWS SES
- Workers: Multiple processes with PM2
- Monitoring: Datadog or New Relic
```

---

## Markdown Formatting

### Structure & Headings

```markdown
# Main Title (H1)
Used once at the top of document

## Major Section (H2)
For main topics

### Subsection (H3)
For detailed topics

#### Details (H4)
For very specific details

Use clear hierarchy - don't skip levels
```

### Code Blocks

```markdown
JavaScript example with syntax highlighting:
```javascript
const user = await createUser(email);
console.log(user.id);
```

Bash command:
```bash
npm install
npm run dev
```

JSON data:
```json
{
  "status": "success",
  "data": {}
}
```

Use language-specific highlighting for clarity
```

### Tables

```markdown
| Column | Description | Required |
|--------|-------------|----------|
| email | User email | Yes |
| name | Full name | Yes |
| role | User role | No |

Good for API parameters, config options, comparisons
```

### Lists

**Bullet list** (use for items without order):
- Item 1
- Item 2
- Item 3

**Numbered list** (use for procedures/steps):
1. First step
2. Second step
3. Third step

**Nested list**:
- Parent item
  - Child item
  - Another child

### Links

```markdown
Link to section: [See Configuration](#configuration-documentation)
External link: [Node.js](https://nodejs.org)
Reference link: [Learn more][1]

[1]: https://example.com/learn-more
```

---

## Documentation Maintenance

### Keep Documentation Current

**During Development**:
- Update docs with code changes
- Include docs in pull request reviews
- Test documentation examples

**Before Release**:
- Review all documentation
- Update version numbers
- Check all code examples work
- Test command examples

**Regular Maintenance**:
- Review documentation quarterly
- Update outdated information
- Remove dead links
- Archive old documentation

### Documentation Review Checklist

- [ ] All new code has JSDoc comments
- [ ] Function examples are current and working
- [ ] API documentation reflects latest endpoints
- [ ] Configuration documentation is complete
- [ ] README is up-to-date with setup instructions
- [ ] Links are not broken
- [ ] Code examples are tested
- [ ] Error cases are documented
- [ ] Deployment procedures are current
- [ ] Architecture diagrams are accurate

### Versioning Documentation

```markdown
## Release v2.0.0 (2024-01-15)

- Updated API documentation for new endpoints
- Added database schema v3 documentation
- Updated deployment procedures for Docker

## Archive

- v1.0.0 Documentation: [archived/v1.0.0/README.md](./archived/v1.0.0/)
```

---

## Quick Reference: What Goes Where

| Type | Location | Format |
|------|----------|--------|
| Function/Class | JSDoc in code | `/** ... */` |
| API endpoints | docs/API.md | Markdown + OpenAPI |
| Setup & usage | README.md | Markdown |
| Test guide | docs/TESTING.md | Markdown |
| Deployment | docs/DEPLOYMENT.md | Markdown |
| Database schema | docs/DATABASE.md | Markdown + diagrams |
| Config | .env.example | Environment format |
| Architecture | docs/ARCHITECTURE.md | Markdown + diagrams |
| Complex logic | Code comments | Inline `//` |
| Migration procedures | docs/MIGRATIONS.md | Markdown |
| Troubleshooting | docs/TROUBLESHOOTING.md | Q&A format |

---

## When to Reference This

Use this guide when:
- Writing code documentation or JSDoc comments
- Creating README files
- Documenting API endpoints
- Setting up configuration documentation
- Creating database documentation
- Maintaining project documentation
- Onboarding new team members
- Documenting job queue workflows
- Creating deployment guides
