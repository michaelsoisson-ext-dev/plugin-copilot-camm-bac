---
name: security-camm-bac
description: Review code changes for quality, security, and adherence to project standards
---

## When to Use This Skill

Review code changes for quality, security, and adherence to project standards.

## Ask for:

1. **What to check** (paste code diff or describe changes)
2. **Focus areas** (functionality, security, performance, testing, etc.)
3. **Vulnerabilities** (what issue does this solve?)

## Review Checklist

- [ ] Does code solve the stated problem?
- [ ] Follow project conventions and style?
- [ ] Tests included and meaningful?
- [ ] Proper error handling implemented?
- [ ] No security vulnerabilities?
- [ ] Database queries optimized?
- [ ] Documentation updated?
- [ ] No hardcoded secrets or credentials?
- [ ] Performance implications considered?
- [ ] Code is maintainable long-term?

## Requirements

- Always read `.github/copilot-instructions.md` in the current working directory first to understand the project's architecture, conventions, and commands
- Apply the repository's code standards
- Reference code review guidelines from files `~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/security*.instructions.md`

- Provide constructive, specific feedback
- Categorize issues as critical, important, or nice-to-have
- Suggest improvements with examples
- Praise good code patterns
