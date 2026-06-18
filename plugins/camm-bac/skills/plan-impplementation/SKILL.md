---
name: plan-implementation
description: Create detailed implementation plans for features and backend systems. Use this skill when the user needs to break down complex features into steps, design architecture before coding, estimate effort, identify blockers, or create a roadmap for development. Generates a structured plan.md file that can be used with `/plan` mode to guide implementation. Apply for backend features, API design, database schema planning, job queue workflows, or any multi-step engineering task.
---

# Plan Implementation

Structure complex features into actionable steps before coding. Generates `plan.md` for guided development.

## When to Use

- Breaking down feature requirements into implementation steps
- Designing backend architecture (APIs, services, data models)
- Planning database schema and migrations
- Sequencing async job workflows
- Estimating effort and identifying blockers
- Creating development roadmaps for teams
- Pre-implementation discovery (what could break?)

## Planning Framework

### 1. Understand Requirements

Ask clarifying questions:

- **What problem does this solve?** (user journey, business impact)
- **What's in scope?** (features, integrations, performance targets)
- **What's out of scope?** (future work, non-goals)
- **Who depends on this?** (other services, frontend, third-party APIs)
- **Constraints?** (performance, security, compliance, timeline)

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

## Plan Output Structure

The skill generates `plan.md` with:

```markdown
# Feature: [Feature Name]

## Overview

[1-2 sentence description]
[Why this matters]

## Requirements

- [Functional requirement 1]
- [Functional requirement 2]
- [Constraint 1]
- [Constraint 2]

## Scope

### In Scope

- [What we build]
- [What we integrate]

### Out of Scope

- [What we defer]
- [What's future work]

## Data Model

[Entity-relationship description]
[Table definitions with columns, types, constraints]

## API Design

[Endpoints with methods, params, responses]

## Implementation Steps

1. [Layer 1: Routes]
   - Subtask 1.1
   - Subtask 1.2

2. [Layer 2: Controllers]
   - Subtask 2.1
   - Subtask 2.2

[... continue for each layer]

## Async Jobs (BullMQ)

[If applicable]

- Job 1: Trigger, payload, processing logic
- Job 2: ...

## Testing Strategy

- Unit tests: [What to test]
- Integration tests: [End-to-end flows]
- Edge cases: [Boundary conditions]

## Risk Analysis

| Risk                     | Impact         | Mitigation                             |
| ------------------------ | -------------- | -------------------------------------- |
| Race condition on create | Data duplicate | DB unique constraint + optimistic lock |
| ...                      | ...            | ...                                    |

## Effort Estimate

- Routes + Controllers: 1–2 hours
- Services + Repositories: 2–3 hours
- Tests: 2–3 hours
- **Total: 5–8 hours**

## Blockers

- [ ] Clarification on payment integration
- [ ] Third-party API documentation

## Next Steps

1. Review plan with team
2. Create issue/PR with plan.md attached
3. Use `/plan` mode during implementation (see usage below)
```

## `/plan` Mode Usage

After generating `plan.md`, reference it during implementation:

```
/plan path/to/plan.md

What's next?
```

The system:

1. Loads `plan.md` as context
2. Suggests the next logical step
3. Breaks down implementation details
4. Tracks progress (mark steps complete)
5. Flags deviations from plan (scope creep)

## Example: User Registration Feature

**Plan Phase:**

1. Understand: What auth strategy? Email or social login? Password rules?
2. Design: User table (id, email, password_hash, created_at), JWT tokens
3. Endpoints: POST /auth/register (email, password) → {token, user}
4. Layers: Route → Controller (validate) → Service (hash, save) → Repository (insert)
5. Tests: Happy path (new user), duplicate email, weak password, DB error
6. Blockers: None

**Implementation Phase:**

- Create route: `POST /users/register`
- Validation: Email format, password strength (8+ chars, mixed case, numbers)
- Hashing: bcrypt with salt rounds = 10
- DB: Insert user, return without password
- JWT: Sign {userId, email}, return in response
- Error handling: 400 (validation), 409 (duplicate), 500 (DB error)
- Tests: 3 success + 5 error scenarios

## Checklist: Before You Code

- ✅ Requirements documented and agreed
- ✅ Data model designed (entities, relationships, constraints)
- ✅ API contracts defined (endpoints, request/response)
- ✅ Layer responsibilities assigned (route → service → repository)
- ✅ Async jobs identified (BullMQ)
- ✅ Test cases drafted (happy path + edge cases)
- ✅ Risks assessed and mitigations noted
- ✅ Effort estimated
- ✅ Blockers flagged
- ✅ Plan reviewed with team

If all checked, start implementation. If gaps, update plan first.

## Anti-Patterns (What Kills Plans)

- ❌ "Just start coding" without design (refactoring hell)
- ❌ Over-specifying trivial details (analysis paralysis)
- ❌ Ignoring edge cases (bugs in production)
- ❌ No risk assessment (surprises mid-sprint)
- ❌ No effort estimate (blown deadlines)
- ❌ Scope creep (plan never matches implementation)

## Template: Minimal Plan (2-hour task)

```markdown
# Feature: Add user email verification

## Requirements

- Send verification email on signup
- User clicks link to activate account
- Activated users can log in

## Data Model

- users table: add `verified` (boolean, default false), `verification_token` (string)

## API

- POST /auth/register - sends email, creates unverified user
- GET /auth/verify?token=X - activates user
- POST /auth/login - checks verified=true

## Steps

1. Route: POST /register, GET /verify
2. Service: Generate token, send email (BullMQ job), check token
3. Repository: Save user, update verified, find by token
4. Tests: Send email, activate, try login unverified

## Effort: 3 hours

## Blockers: None
```
