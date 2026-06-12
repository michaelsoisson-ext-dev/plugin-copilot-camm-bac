---
applyTo: "**/*.md,pull_requests"
description: "Code review standards and GitHub review guidelines"
---

# Code Review Standards

Apply the repository-wide guidance from `../copilot-instructions.md` to all code reviews.

## Code Review Process

- All changes require at least one code review approval before merging
- Author should not approve their own PRs
- Reviews should be completed within 24 hours
- Assign reviewers based on code ownership and expertise
- Use PR templates to ensure consistent information
- Link related issues and documentation in PR description

- Focus on high-signal findings; skip trivial style-only comments.
- Use severity tags exactly: [Critical], [Major], [Minor].
- Be concrete and actionable; propose minimal safe fixes.
- Avoid repeating the diff verbatim.

### Output format (Markdown):

1. Summary (2-4 bullets)
2. Key behavior changes (additions/deletions)
3. Findings by severity
   For each finding include:
   - Severity + short title
   - File/path and line(s) if identifiable
   - What is wrong and why it matters
   - Concrete fix suggestion
   - Optional `diff` snippet when helpful
4. Final verdict:
   - "No significant issues found" OR
   - "Changes requested" with top 1-3 must-fix items

## What to Review

### Functionality & Logic

- Does the code solve the stated problem?
- Are edge cases handled appropriately?
- Does the implementation follow the agreed design?
- Are error cases handled correctly?
- Is the solution maintainable long-term?
- Does it introduce any breaking changes?

### Code Quality

- Does code follow project conventions and style?
- Are variable and function names clear and descriptive?
- Is the code DRY (Don't Repeat Yourself)?
- Are there unnecessary comments or dead code?
- Is the code properly structured (layered architecture)?
- Are dependencies well-managed?

### Testing

- Are tests included for new features?
- Do tests cover happy path and error scenarios?
- Are tests meaningful and not just mocking everything?
- Is test coverage appropriate (80%+)?
- Are integration tests included for features?
- Do existing tests still pass?

### Performance

- Will this change impact performance?
- Are there N+1 query problems introduced?
- Is caching used appropriately?
- Are there memory leaks or resource issues?
- Is database query optimization considered?
- Are there any blocking operations in async code?

### Security

- Are inputs validated properly?
- Is sensitive data handled securely?
- Are there SQL injection vulnerabilities?
- Is authentication/authorization implemented?
- Are secrets stored in environment variables?
- Are dependencies known to be secure?

### Documentation

- Is code documentation (JSDoc) updated?
- Are API changes documented?
- Is README updated if needed?
- Are configuration changes documented?
- Are complex logic explanations included?

## Review Comments Best Practices

### Be Constructive

- Frame comments as questions when appropriate: "Did you consider...?"
- Explain the reasoning behind suggestions
- Offer suggestions, not demands
- Praise good code when reviewing
- Focus on the code, not the person

### Be Specific

- Point to exact lines of code
- Reference relevant style guides or conventions
- Provide examples of better approaches
- Explain why a change is needed

### Categorize Issues

- **Critical**: Must be fixed before merging (security, bugs, breaking changes)
- **Important**: Should be fixed, but can discuss alternatives
- **Nice-to-have**: Suggestions for improvement
- **Out-of-scope**: Suggestions for future refactoring

### Use GitHub Review Features

- Request changes for critical issues
- Comment for discussions or questions
- Approve after concerns are addressed
- Use threads to keep discussions organized
- Resolve conversations when issues are addressed

## What NOT to Review

- Style and formatting that are automatically checked (use linters)
- Bike-shedding (minor preference issues)
- Out-of-scope architectural decisions (raise in design phase)
- Unrelated code refactoring (separate PR)
- Third-party code that's already tested

## PR Author Responsibilities

- Write clear, descriptive PR titles and descriptions
- Keep PRs focused and reasonably sized
- Respond to review comments promptly
- Don't dismiss concerns without discussion
- Rebase and force-push only with agreement
- Update PR based on feedback
- Mark conversations as resolved when addressed

## Performance Review

- Check for performance regressions
- Review database query changes
- Verify caching implementation
- Look for memory leaks
- Check for blocking operations
- Verify job queue implementation efficiency

## Security Review Checklist

- [ ] No hardcoded secrets or credentials
- [ ] Input validation implemented
- [ ] SQL injection prevention (parameterized queries)
- [ ] CORS/CSRF protection appropriate
- [ ] Authentication/authorization correct
- [ ] Error messages don't leak internal details
- [ ] Dependencies checked for vulnerabilities
- [ ] Sensitive data handling is secure

## BullMQ-Specific Review

- Job handlers have proper error handling
- Job timeouts are set appropriately
- Retry logic is reasonable
- Job data validation is present
- Sensitive data not logged in jobs
- Queue initialization is correct
- Worker shutdown is graceful

## MariaDB-Specific Review

- All queries use parameterized statements
- Indexes are considered for new queries
- N+1 problems are avoided
- Transactions used appropriately
- Schema migrations documented
- Connection pooling configured
- Database credentials in environment variables

## After Review

- Merge only after all critical issues resolved
- Delete feature branches after merge
- Track review feedback for continuous improvement
- Use metrics to improve review process
