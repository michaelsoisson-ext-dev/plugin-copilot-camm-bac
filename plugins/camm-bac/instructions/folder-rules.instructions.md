---
name: Folder rules
globs: '**/*.{ts,js,mjs}'
alwaysApply: true
description: Guidelines for writing efficient in the correct folder
---

# Structure et Organisation

- As an expert software developer, you respect the structure and organization of the project.

## Code Organization

- Group related functionality together
- Use consistent file structure
- Separate concerns into modules/classes
- Keep files focused and reasonably sized
- Use clear folder hierarchies folder-rules.md file

## _tests_

- Write tests `*.spec.js` with jest for all new features directory

```
__tests__/
├── unit/           # Mocked isolated unit tests
├── integration/    # Tests with external dependencies
└── helper/         # Test environment setup assistance
```

## /lib/command

CLI Command Entry Point

- One file = one command
- Argument validation first
- Delegation to appropriate services

## /lib/bac, /lib/ox, /lib/cst

REST API Layer

- One file per endpoint/resource
- Use SDKs generated from /openapi/generators
- Uniform error handling
- Retry with exponential backoff

## /lib/soap SOAP client

SOAP Integration

- Wrapper around native SOAP calls
- XML ​​↔ JSON transformation
- Response caching when applicable

## /lib/db - Data Access Layer

Data Access Layer

- Knex queries only
- One file per entity/table
- Reusable functions
- Managed connection pool

## /lib/wassup

Auth access Layer

- wassup queries
- manages credentials method
- pase attributes

## /lib/mq

Message Queue & Workers

- Separation of producer/consumer queues
- Management of migration batches
- Dead letter queue for Failures
- Monitoring via /metrics

## /lib/recovery

Error Recovery

- Structured error logs
- Automatic replay where possible
- Alerts for critical failures

## /lib/errors

Custom errors

- Clear error hierarchy
- Standardized error codes
- Enriched context for debugging

## /lib/metrics

Observability

- Jaeger wrapper for tracing
- Important business metrics
- Correlation between traces

## Full structure

```
_tests_/ directory for Jest tests.
project
├
lib/
├─ recovery/ recovery directory for data views with errors
├─ command/ program entry point directory
├─ common/ directory for common utilities and tools
├─ bac/ directory for the bac API
├─ soap/ directory for the API mark in promises
├─ cst/ directory for the cst API
├─ db/ directory for Knex files dealing with MariaDB database SQL
├─ ox/ directory for the ox API
├─ errors/ directory for custom error handling
├─ mq/ directory for migration batch queues and workers
├─ soap/ directory for soap requests
├─ metrics/ directory for the Jaeger wrapper trace

openapi/

├─ specs/ directory for JSON/YAML BAC & OX specs
├─ generators/ directory for client SDKs generated from the specs

types/ directory for TypeScript, JSdoc types, and definitions '*.d.ts'
resources/ directory for external resources for JSON and Redis
├─ db/ is a directory for import scripts (_.sql) from the OneMail Migration platform

```
