---
name: conventions-camm-bac
description: coding conventions applicable in js/ts
applyTo: "**/*.{js,ts,jsx,tsx,mjs,cjs}"
---

# Conventions Standards

Apply the repository-wide guidance from `./github/copilot-instructions.md` to all documentation.

## Naming Conventions

- Use descriptive and meaningful names
- Be consistent with naming patterns
- Avoid abbreviations and single letters (except loop counters)
- Use searchable names for constants
- Make the purpose clear from the name
- Prefer `kebab-case` for naming js files
- Prefer `camelCase` for variables and functions
- Use `PascalCase` for classes and constructors
- Use `UPPER_CASE` for constants
- use extenstion `*.mjs` for naming file

- Functions: `camelCase` with action verbs

  ```javascript
  async function migrateAccount(accountId) {}
  async function validateEmailAddress(email) {}
  ```

- Classes: `PascalCase`

  ```javascript
  class MigrationWorker {}
  class EmailRepository {}
  ```

- Files: `kebab-case`
  ```
  migration-worker.mjs
  email-repository.mjs
  ```

## Module Organization

- Use ES6 modules with explicit imports and exports like `import chunk from 'lodash/chunk.js'`
- Organize related types in dedicated type files
- Use barrel exports for clean public APIs
- Implement proper module boundaries
- Use path mapping for clean import statements

## Function Design

- Keep functions small and focused (single responsibility)
- Limit function parameters (ideally 3 or fewer)
- Use descriptive function names that indicate action
- Avoid side effects when possible
- Return early to reduce nesting

## Code Readability

- Write code for humans to read
- Use consistent formatting
- Add whitespace for visual separation
- Limit line length (80-120 characters)
- Use meaningful variable names

## DRY Principle

- Don't Repeat Yourself
- Extract common functionality
- Use configuration over duplication
- Create reusable components
- Balance DRY with clarity

## SOLID Principles

- **Single Responsibility**: One reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes must be substitutable
- **Interface Segregation**: Many specific interfaces
- **Dependency Inversion**: Depend on abstractions

## Best Practices

- Prefer composition over inheritance
- Write tests for your code
- Refactor regularly
- Use version control effectively
- Document architectural decisions

## Code Standards

- Follow SOLID principles
- Prefer composition to inheritance
- Use pure functions without side effects whenever possible
- Validate inputs at boundaries (APIs)

## Database

- Use Knex for all queries
- Versioned migrations in /resources/db
- Transactions for critical operations

## Generic Programming

- Use meaningful generic parameter names
- Add constraints to generic types when appropriate
- Use default generic parameters for better ergonomics
- Implement generic factories and builders
- Use generic types for container and utility functions

## Common Patterns

- Use discriminated unions for state management
- Implement builder patterns with fluent interfaces
- Use factory patterns with proper typing
- Create type-safe event systems
- Implement proper null safety patterns

## Function Types

- Use arrow functions for simple expressions
- Use function declarations for hoisted functions
- Type function parameters and return values explicitly
- Use generic functions for type-safe reusable code
- Implement proper function overloads when needed

## Error Handling

- Use typed error classes for different error types
- Implement proper async error handling patterns
- Use Result types for explicit error handling
- Create error middleware with proper typing
- Handle unhandled promise rejections appropriately
- Use custom error classes in /errors
- Log with context (correlate IDs)
