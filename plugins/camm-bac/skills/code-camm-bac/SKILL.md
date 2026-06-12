---
name: code-camm-bac
description: Write code or refactor code to improve readability, performance, and maintainability
---

## When to use this Skill

Write new code or refactor existing code to improve readability, maintainability, performance, or security.

## Ask for

1. **What to code** (paste code or describe the file/section)
2. **What to refactor** (paste code or describe the file/section)
3. **Refactoring goals** (readability, performance, security, maintainability, etc.)
4. **Constraints** (breaking changes not allowed, keep API same, etc.)

## Goals

- **Readability**: Clear variable names, reduced complexity
- **Maintainability**: Modular code, following patterns, reducing duplication
- **Performance**: Optimizing queries, caching, reducing memory
- **Security**: Removing vulnerabilities, securing secrets
- **Testing**: Making code more testable

## Requirements

- Always read `.github/copilot-instructions.md` in the current working directory first to understand the project's architecture, conventions, and commands
- Follow project's documentation standards from ` ~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/javascript-camm-bac.instructions` and ` ~/.copilot/installed-plugins/orange-camm-bac/camm-bac/instructions/conventions-camm-bac.instructions`
- Maintain backward compatibility unless explicitly allowed
- Include tests for refactored code
- Update documentation if behavior changes
- Explain refactoring rationale
- Highlight performance improvements with metrics
- Ensure no regression in functionality
