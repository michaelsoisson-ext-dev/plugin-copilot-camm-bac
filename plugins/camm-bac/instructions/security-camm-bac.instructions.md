---
applyTo: "**/*.js,**/*.ts,package.json,.env*"
description: "Security best practices and guidelines"
---

# Security Best Practices

Apply the repository-wide guidance from `../copilot-instructions.md` to all code.

## Authentication & Authorization

- Implement JWT for stateless authentication
- Store JWT secrets securely in environment variables
- Use appropriate token expiration times (short-lived access tokens, longer refresh tokens)
- Validate tokens on all protected endpoints
- Implement role-based access control (RBAC) for different user types
- Never store sensitive data in JWT claims
- Use HTTPS in production to protect token transmission

## Database Security

- **SQL Injection Prevention**: Always use parameterized queries
  - Use ORM or query builders that handle parameterization
  - Never concatenate user input into SQL queries
  - Validate and sanitize all user inputs
- **Database Connection**: Use connection pooling with appropriate size limits
- **Credentials**: Store database credentials in environment variables, never in code
- **Access Control**: Implement least privilege database user accounts
- **Backups**: Regular automated backups with encryption

## API Security

- Implement rate limiting to prevent DDoS and brute force attacks
- Use API keys for service-to-service communication
- Validate Content-Type and request headers
- Implement CORS appropriately (whitelist domains, not wildcards)
- Use helmet middleware for HTTP headers security
- Implement request size limits to prevent payload attacks
- Validate and sanitize all user inputs

## Environment & Configuration

- Use `.env` files for configuration, keep `.env.example` in repo
- Never commit `.env` files with real credentials
- Use environment variables for all sensitive data:
  - Database credentials
  - API keys and secrets
  - JWT secrets
  - Third-party service credentials
- Implement configuration validation at startup
- Use different secrets for different environments (dev, staging, production)

## Password & Credential Management

- Hash passwords using bcrypt or scrypt with appropriate salt rounds
- Never store plain text passwords
- Implement password policies (length, complexity)
- Implement account lockout after failed login attempts
- Use secure password reset mechanisms with time-limited tokens
- Log failed authentication attempts for security monitoring

## Dependency Management

- Keep dependencies updated regularly
- Use `npm audit` to check for vulnerabilities
- Review dependency security advisories
- Minimize external dependencies
- Use dependency pinning in production (lock files)
- Audit transitive dependencies

## BullMQ Queue Security

- Validate job data before processing
- Implement authorization checks in job handlers
- Log sensitive job operations (without exposing data)
- Use Redis authentication if queue uses Redis
- Implement job timeout to prevent infinite processing
- Handle sensitive data in jobs securely (don't log raw data)

## File Upload Security

- Validate file types (check content, not just extensions)
- Implement file size limits
- Store uploads outside web root
- Use secure file naming (don't use user-provided names directly)
- Scan uploaded files for malware if handling user files
- Implement access controls for uploaded files

## Error Handling & Logging

- Never expose internal error details to clients
- Log security-relevant events (login attempts, authorization failures)
- Include sufficient context in logs (user ID, request ID, timestamp)
- Implement centralized logging for security monitoring
- Store logs securely with appropriate retention policies
- Don't log sensitive data (passwords, API keys, tokens)

## Docker & Container Security

- Use official base images, regularly update
- Don't run containers as root
- Implement resource limits (CPU, memory)
- Use environment variables for secrets (not hardcoded)
- Scan images for vulnerabilities
- Keep Docker runtime updated

## Code Review Security

- Require code review for all changes
- Review security implications of changes
- Check for hardcoded secrets before merging
- Verify input validation and error handling
- Audit dependency additions
- Review changes to authentication and authorization code

## Security Incident Response

- Have a documented incident response plan
- Implement monitoring and alerting for security events
- Keep audit logs for incident investigation
- Have a process for security vulnerability disclosure
- Regular security training for development team
