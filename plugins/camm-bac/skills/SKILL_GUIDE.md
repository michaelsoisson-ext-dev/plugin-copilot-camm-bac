# Backend Development Skills Guide

Refactored from `backend-dev-camm-bac.agent.md` into 3 focused, composable skills.

## Quick Start

### Scenario 1: Build a Feature from Scratch

```
# Step 1: Create structured plan
I need to build a user registration API with email verification.

→ Claude triggers: plan-implementation skill
→ Output: plan.md with requirements, data model, endpoints, steps

# Step 2: Reference plan during implementation
/plan path/to/plan.md
/invoke backend-dev
Create the authentication service layer first.

→ Claude loads plan.md as context
→ Suggests next step in sequence
→ Keeps you on track

# Step 3: Keep tokens lean
terse mode
What's the error I'm getting in the password hashing?

→ 50-70% fewer tokens in response
→ Technical accuracy preserved
```

---

### Scenario 2: Fix a Bug in Existing Code

```
terse mode
Help me debug the N+1 query in the user details endpoint.

→ Triggers: backend-dev + terse-mode
→ Output: Concise diagnosis and fix
```

---

### Scenario 3: Deep Dive into Architecture

```
Explain the transaction handling in concurrent user creation.

→ Triggers: backend-dev (without terse mode)
→ Output: Full explanation with code examples, sequence diagrams
```

### Scenario 4: Build a Feature with glab

```
# Step 1: Create structured plan
I need to build a user registration API with email verification.

→ Claude triggers: plan-implementation skill
→ Output: plan.md with requirements, data model, endpoints, steps

# Step 2: Save this plan as an issue in Gitlab
→ Claude triggers: glab-issue skill
->Output: a created issue in gitlab with the plan.md as description and provides the title

# Step 3: Create a draft mr from this issue
→ Claude triggers: glab-issue skill
->Output: a created issue in gitlab with the plan.md as description and provides the title

# Step 4: Checkout the branch created by the draft merge request
→ Claude triggers: glab-issue skill or git command
->Output: a branch ready for commit change on terminal

# Step 5: Reference plan during implementation
/plan path/to/plan.md
/invoke backend-dev
Create the authentication service layer first.

→ Claude loads plan.md as context
→ Suggests next step in sequence
→ Keeps you on track

# Step 6: Keep tokens lean
terse mode
What's the error I'm getting in the password hashing?

→ 50-70% fewer tokens in response
→ Technical accuracy preserved
```

---

## Skill Combinations

### Backend Development Only

```
Claude triggers: backend-dev
Use when: building features, writing code, architecture decisions
Output: Full explanations, code examples, patterns
```

### + Terse Mode

```
Claude triggers: backend-dev + terse-mode
Use when: speed is critical, token budget tight, brief guidance
Output: Concise explanations (50-70% fewer tokens)
```

### + Planning

```
Claude triggers: plan-implementation
Use when: designing features before coding
Output: Structured plan.md → use with `/plan` mode

Then: /invoke backend-dev
→ Continue with detailed implementation guidance
```

### All Three Combined

```
# Day 1: Planning
plan-implementation
→ Create plan.md for new feature

# Day 2-3: Coding
/plan path/to/plan.md
terse mode
/invoke backend-dev
→ Implement step-by-step, token-efficient

# Code Review
terse mode
/invoke backend-dev
→ Review code changes concisely
```

---

## Skill Descriptions

### 1. backend-dev

**When to trigger:** Building backend features, APIs, databases

**Covers:**

- REST/SOAP endpoint design and implementation
- Service layer patterns (business logic)
- Repository patterns (data access)
- Database design and migrations
- Async job processing (BullMQ)
- Security (validation, sanitization, auth)
- Performance (indexing, caching, scaling)
- Testing (unit, integration, edge cases)
- Development workflow (5 phases)

**Example triggers:**

- "Create a POST /users endpoint"
- "Design the payment service"
- "How do I handle race conditions?"
- "Optimize this slow query"
- "Write tests for the auth middleware"

---

### 2. terse-mode

**When to trigger:** Any task where token efficiency matters

**Covers:**

- Concise sentence structure (3-6 words)
- Removing filler and elaboration
- Preserving technical accuracy
- Code output remains standard
- 50-70% token reduction

**Example triggers:**

- "terse mode"
- "be brief"
- "caveman mode"
- "fewer tokens"
- "less verbose"

**Works with:**

- Any other skill (backend dev, debugging, code review, docs)
- Any domain (not limited to backend)

---

### 3. plan-implementation

**When to trigger:** Creating implementation plans before coding

**Covers:**

- Planning framework (6 steps)
- Requirements gathering
- Data model design
- API endpoint design
- Layer-by-layer breakdown
- Risk analysis
- Effort estimation
- Structured plan.md output
- Pre-implementation checklist

**Example triggers:**

- "Create an implementation plan for..."
- "Design the architecture for..."
- "Break down this feature into steps"
- "What could go wrong with...?"
- "Estimate effort for..."

**Output:**

- `plan.md` file with:
  - Overview, requirements, scope
  - Data model, API contracts
  - Implementation steps (layered)
  - Testing strategy
  - Risk analysis matrix
  - Effort estimate
  - Blockers and next steps

**Usage:**

```
/plan path/to/plan.md
What's next?
```

→ System loads plan as context, suggests next step, tracks progress

---

## Best Practices

### ✅ Do

1. **Start with planning** for complex features

   ```
   plan-implementation
   → generates plan.md
   → reference with /plan during coding
   ```

2. **Use terse mode** when speed matters

   ```
   terse mode
   /invoke backend-dev
   → Implement feature token-efficiently
   ```

3. **Combine skills strategically**

   ```
   # Design phase
   /invoke plan-implementation

   # Implementation phase
   /plan path/to/plan.md
   terse mode
   /invoke backend-dev

   # Review phase
   terse mode
   /invoke backend-dev
   → Code review concisely
   ```

4. **Ask for elaboration when needed**

   ```
   terse mode → [concise answer]

   Explain that in more detail
   → [expands, then resumes terse mode]
   ```

---

### ❌ Don't

1. ❌ Skip planning for large features
   - Leads to scope creep and rework

2. ❌ Use terse mode for security warnings
   - Always expand for security/irreversible actions

3. ❌ Ignore the plan.md during implementation
   - Plan exists to keep you on track

4. ❌ Mix vague requirements into implementation
   - Clarify in planning phase first

---

## Migration from Old Agent

**Old workflow:**

```
@plugins/camm-bac/agents/backend-dev-camm-bac.agent.md
→ Mixed concerns (planning, coding, communication)
→ Terse mode locked to backend only
→ No structured planning output
```

**New workflow:**

```
1. @plugins/camm-bac/skills/plan-implementation
   → Create plan.md (reusable across projects)

2. /plan path/to/plan.md
   @plugins/camm-bac/skills/backend-dev
   → Implement with structured guidance

3. terse mode
   → Keep tokens down (works with any skill)
```

---

## Examples

### Example 1: User Registration Feature

**Planning:**

```
plan-implementation
Create an implementation plan for user registration with JWT tokens.

→ Output: plan.md
   - Requirements: email, password validation, JWT
   - Data model: users table (email unique, password_hash)
   - API: POST /auth/register → {token, user}
   - Steps: routes → controller → service → repository
   - Tests: happy path, duplicate email, weak password
   - Blockers: none
   - Effort: 4 hours
```

**Implementation:**

```
/plan path/to/plan.md
terse mode
/invoke backend-dev

Start building the controller.

→ Creates controller with validation
→ Calls service layer
→ Handles errors concisely
```

---

### Example 2: Debug Slow Query

```
terse mode
/invoke backend-dev

The user list endpoint is slow with 10K users. How do I optimize?

→ Response (terse):
  Check N+1 queries. Add indexes on frequently filtered columns.
  Implement pagination. Use batch loading for related data.
  Consider Redis caching.
```

---

### Example 3: Code Review

```
terse mode
/invoke backend-dev

Review this authentication service for security issues.

[Code block]

→ Response (terse):
  Issue 1. Token not validated on every request. Check in middleware.
  Issue 2. Password returned in error messages. Use generic "Invalid credentials".
  Strength. Hash function correct. Input sanitization present.
```

---

## Summary

| Aspect                  | Benefit                                         |
| ----------------------- | ----------------------------------------------- |
| **Focused skills**      | Each skill does one thing well                  |
| **Composable**          | Combine based on your needs                     |
| **Reusable**            | Terse mode applies to any skill                 |
| **Structured planning** | plan.md → /plan mode → guided implementation    |
| **Token-efficient**     | 50-70% reduction with terse mode                |
| **Portable**            | Skills installable, independent of agent config |

---

## Files

```
plugins/camm-bac/skills/
├── backend-dev/SKILL.md
├── terse-mode/SKILL.md
├── plan-implementation/SKILL.md
└── SKILLS_GUIDE.md (this file)
```

---

**Questions?** See individual SKILL.md files for detailed documentation.
