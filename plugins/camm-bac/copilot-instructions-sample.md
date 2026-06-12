# Plugin Copilot CAMM BAC — Copilot Instructions

## Project Overview

A robust Node.js backend API built with Fastify for the Copilot BAC project, featuring job queue processing with BullMQ and persistent data storage with MariaDB. This project emphasizes strict coding standards, performance optimization, and maintainability.

## Tech Stack

**Runtime & Framework:**

- Node.js (JavaScript/TypeScript)
- Fastify - Modern, fast HTTP framework
- BullMQ - Distributed job queue system
- MariaDB - Relational database

**Infrastructure:**

- Docker & Docker Compose for containerization
- GitHub Actions (if enabled) for CI/CD

## Conventions

### Naming

- **Files & Directories**: Use kebab-case for files and folders
  - Routes: `user-routes.js`, `auth-routes.js`
  - Services: `user-service.js`, `email-service.js`
  - Controllers: `user-controller.js`
  - Utilities: `logger-util.js`
- **Variables & Functions**: Use camelCase
  - `const userEmail = 'user@example.com'`
  - `function validateUserInput() {}`
  - `const dbConnection = new Connection()`
- **Constants**: Use UPPER_SNAKE_CASE
  - `const MAX_RETRIES = 3`
  - `const JOB_QUEUE_NAME = 'processing-queue'`
- **Classes**: Use PascalCase
  - `class UserRepository {}`
  - `class EmailNotificationJob {}`

### Structure

- **Layered Architecture**: Controllers → Services → Repositories → Database
  - `routes/` - Fastify route handlers
  - `controllers/` - Request/response handling
  - `services/` - Business logic
  - `repositories/` - Data access layer
  - `jobs/` - BullMQ job definitions
  - `middleware/` - Fastify middleware
  - `utils/` - Helper functions and utilities
  - `config/` - Configuration files
  - `tests/` - Test files (mirror src structure)
- **Keep modules focused** - Single responsibility principle
- **Config files** in root: `package.json`, `docker-compose.yml`, `.env.example`

### Error Handling

- Use structured error objects with `code`, `message`, and `details`
- Implement custom error classes extending Error
- Log all errors with context (user ID, request ID, timestamp)
- Return meaningful HTTP status codes (400, 404, 500, etc.)
- Use try-catch for async operations; use `.catch()` for promise chains
- Validate inputs at the controller layer before processing
- Implement global error handler in Fastify

## Workflow

### PR Conventions

- Branch naming: `feature/user-auth`, `fix/queue-timeout`, `docs/api-setup`
- Small, focused PRs with clear description
- Include tests for new features
- Reference issues: `Fixes #123`

### Commit Style

- Format: `type(scope): message`
- Types: `feat`, `fix`, `docs`, `test`, `refactor`, `perf`, `chore`
- Examples:
  - `feat(auth): add JWT token refresh endpoint`
  - `fix(queue): handle job failure retry logic`
  - `test(users): add validation tests`

### Code Review Standards

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/code-review.instructions.md`

## Language-Specific Guidelines

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/javascript.instructions.md`

## Testing Standards

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/testing.instructions.md`

## Security Best Practices

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/security.instructions.md`

## Documentation Requirements

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/documentation.instructions.md`

## Performance Optimization

- See `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/performance.instructions.md`
