---
name: write-plan
description: Create detailed implementation plans for features and backend systems. Use this skill when the user needs to break down complex features into steps, design architecture before coding, estimate effort, identify blockers, or create a roadmap for development. Generates a structured plan.md file that can be used with `/plan` mode to guide implementation. Apply for backend features, API design, database schema planning, job queue workflows, or any multi-step engineering task.
---

# Plan Implementation

Structure complex features into actionable steps before coding. Generates `plan.md` for guided development.
This skill takes the current conversation context or specs and codebase understanding and produces a plan implementation.

# Constraints

- **DO NOT** interview the user — just synthesize what you already know.

## When to Use

- Breaking down feature or fix requirements into implementation steps
- Designing backend architecture (APIs, services, data models)
- Planning database schema and migrations
- Sequencing async job workflows
- Estimating effort and identifying blockers
- Creating development roadmaps for teams
- Pre-implementation discovery (what could break?)

## Planning Framework

### 1. Understand Requirements

- Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the context, and respect any ADRs in the area you're touching.

- Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can.

### 2. Define Data Model

Design before coding:

- **Resources:** What data objects? (users, projects, documents)
- **Relationships:** One-to-many? Many-to-many? Foreign keys?
- **Indexes:** Which columns queried frequently? (user_id, created_at, email)
- **Constraints:** Unique fields? Required fields? Cascading deletes?
- **Migrations:** How to add this to existing database?

### 3. Design API Endpoints

Define contracts:

- **Endpoints:** POST /users, GET /users/:id, DELETE /users/:id
- **Request:** What fields required? Validation rules?
- **Response:** Success (200, 201, 204) vs errors (400, 401, 404, 500)
- **Auth:** Which endpoints need tokens? Scopes?
- **Rate limits:** Protect from abuse?

### 4. Break Into Layers

Layer-by-layer implementation:

- **Routes** (Fastify) - Entry points, request handling
- **Controllers** - Validation, parameter extraction
- **Services** - Business logic, orchestration
- **Repositories** - Database operations
- **Tests** - Unit, integration, edge cases
- **Documentation** - JSDoc, README updates

### 5. Identify Async Work

Move heavy ops to job queue:

- **Email sending** (BullMQ job)
- **File processing** (conversion, cleanup)
- **Reporting** (aggregations, exports)
- **Third-party calls** (API integrations with retry logic)
- **Cleanup** (expired records, cache invalidation)

### 6. Risk Assessment

What could go wrong?

- **Race conditions:** Concurrent requests creating duplicates?
- **Data integrity:** What if a transaction fails mid-way?
- **Performance:** Will queries be fast with large datasets?
- **Security:** Input validation? Token handling? Error messages?
- **Scalability:** Can this scale horizontally?

### 7. Plan Output Structure

- Write the plan `plan-<issue-name>.md` using the template `./references/plan-template.md` then publish it to the project issue tracker.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)
