---
name: docs
description: Generate and update project documentation including APIs, setup guides, testing documentation, and inline code documentation. Always use this skill whenever the user asks to create or update documentation for their project, write a README, document testing strategies, create API docs, or needs guidance on documenting code. Use for all documentation tasks including test documentation, configuration guides, architecture docs, troubleshooting guides, and JSDoc.
---

## When to use this Skill

Generate and update project documentation—including API docs, setup guides, testing documentation, architecture guides, and inline code documentation. Always offer this skill when documentation is needed.

## Quick Workflow

1. **Identify what to document** (API endpoints, setup guide, testing strategy, configuration, component, etc.)
2. **Choose documentation type** (README, API docs, Test Guide, JSDoc, Configuration guide, etc.)
3. **Define target audience** (developers, ops, end-users, QA, etc.)
4. **Generate with examples** and reference relevant standards

## Documentation Types

- **README.md**: Project overview, quick start, tech stack, running tests
- **API Documentation**: Endpoints, request/response contracts, error codes, examples
- **Testing Guide**: Testing strategy, patterns, setup, coverage goals, examples
- **Setup Guide**: Environment setup, dependencies, configuration, running tests
- **JSDoc**: Function/class documentation with parameters, return values, examples
- **Architecture**: System design, data flow, component relationships
- **Configuration**: Environment variables, settings, configuration examples
- **Troubleshooting**: Common issues, solutions, debugging tips

## Key Requirements

- Read `.github/copilot-instructions.md` first to understand project conventions
- Include code examples for every major concept
- Format clearly with Markdown and good hierarchy
- Keep documentation synchronized with code
- Use realistic examples from the actual codebase
- Ensure documentation is accessible and clear to target audience

## For Testing Documentation

When documenting testing strategies, testing guides, or test-related documentation, reference the Testing Documentation Standards in `references/testing-documentation.md`. This reference covers:
- Testing strategy templates (unit, integration, job queue)
- Test structure and organization patterns
- Test naming conventions and best practices
- Common testing patterns (async, mocking, error testing)
- API testing and request/response documentation
- Coverage goals and quality metrics

See that file for detailed templates, examples, and when to reference specific sections.
