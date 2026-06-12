---
applyTo: "**/*.md,README.md,docs/**"
description: "Documentation standards and requirements"
---

# Documentation Standards

Apply the repository-wide guidance from `../copilot-instructions.md` to all documentation.

## Documentation Requirements

### Code Documentation
- Use JSDoc format for all functions and classes
- Document function parameters, return types, and throw errors
- Include examples for complex functions
- Document class properties and methods
- Add comments for complex business logic
- Document external API integrations

### README.md
- Project title and brief description
- Quick start guide with setup instructions
- Technology stack overview
- Project structure and key directories
- Development workflow (clone, install, run)
- Testing instructions
- Deployment instructions
- Contributing guidelines
- License information

### API Documentation
- Document all API endpoints with:
  - HTTP method and path
  - Request parameters (query, body, headers)
  - Response status codes
  - Response body examples
  - Authentication requirements
  - Error responses
- Use OpenAPI/Swagger format for API specification
- Keep documentation synchronized with code

### Job Queue Documentation
- Document all BullMQ jobs with:
  - Job name and purpose
  - Input data schema
  - Processing time expectations
  - Retry policy
  - Error handling
  - Example usage
- Document queue architecture and worker configuration

### Database Documentation
- Document database schema:
  - Table names and purposes
  - Column definitions and data types
  - Primary keys and foreign keys
  - Indexes
  - Constraints
- Document relationships between entities
- Include entity-relationship diagram (ER diagram)
- Document migration procedures for schema changes

### Configuration Documentation
- Document all environment variables in `.env.example`
- Include variable purposes and acceptable values
- Document default values if applicable
- Document required vs. optional variables
- Document configuration for different environments (dev, staging, production)

### Security & Deployment
- Document security requirements and setup
- Document deployment procedures step-by-step
- Document backup and recovery procedures
- Document monitoring and alerting setup
- Document incident response procedures
- Document performance tuning guidelines

## JSDoc Format Examples

### Function Documentation
```javascript
/**
 * Creates a new user with the provided email and password.
 * @param {string} email - The user's email address
 * @param {string} password - The user's password (will be hashed)
 * @returns {Promise<User>} The created user object
 * @throws {ValidationError} If email or password is invalid
 * @throws {DuplicateEmailError} If email already exists
 */
async function createUser(email, password) { }
```

### Class Documentation
```javascript
/**
 * Service for managing user operations.
 * @class UserService
 */
class UserService {
  /**
   * Find user by ID.
   * @param {number} userId - The user ID
   * @returns {Promise<User|null>} The user object or null if not found
   */
  async findById(userId) { }
}
```

## Markdown Formatting

- Use clear headings hierarchy (# > ## > ###)
- Use code blocks with language specification for syntax highlighting
- Use tables for structured data (API parameters, configuration)
- Use bullet points for lists (use dashes or asterisks)
- Use numbered lists for procedures/steps
- Link to related documentation

## Documentation Maintenance

- Update documentation when code changes
- Review documentation in code reviews
- Keep documentation examples current and tested
- Version documentation for major releases
- Archive old documentation versions
- Link documentation from code comments

## Comments vs. Documentation

**Write comments for:**
- Explaining "why" (not "what")
- Complex algorithms or business logic
- Non-obvious implementation choices
- Known workarounds or limitations

**Avoid comments for:**
- Self-explanatory code
- Repeating what the code does
- Outdated or obvious information
