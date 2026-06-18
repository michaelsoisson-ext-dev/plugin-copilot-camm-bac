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

## Reference Files for Detailed Guidance

This skill uses progressive disclosure—quick guidelines in SKILL.md, comprehensive details in references:

### `references/documentation-standards.md` (16 KB)
Use for general documentation tasks covering:
- **Code Documentation**: JSDoc format, comments vs. documentation, what to document
- **README.md**: Structure template, quick start, technology stack, project structure
- **API Documentation**: Endpoint templates, OpenAPI specs, request/response examples
- **Job Queue Documentation**: BullMQ job schemas, retry policies, monitoring
- **Database Documentation**: Schema tables, ER diagrams, relationships
- **Configuration Documentation**: Environment variables, multi-environment setup
- **Markdown Formatting**: Headings, code blocks, tables, lists, links
- **Documentation Maintenance**: Review checklists, versioning, what goes where

### `references/testing-documentation.md` (12 KB)
Use for testing documentation tasks covering:
- Testing strategy templates (unit, integration, job queue)
- Test structure and organization patterns
- Test naming conventions and best practices
- Common testing patterns (async, mocking, error testing)
- API testing and request/response documentation
- Coverage goals and quality metrics

## How to Use This Skill

1. **Identify what to document** — Code, API, setup guide, test guide, database schema, config, etc.
2. **Choose appropriate reference** — General docs → `documentation-standards.md`, testing docs → `testing-documentation.md`
3. **Follow the templates and examples** — Both references include copy-paste ready templates
4. **Keep it synchronized** — Update documentation when code changes
5. **Use realistic examples** — Pull examples from actual codebase when possible
