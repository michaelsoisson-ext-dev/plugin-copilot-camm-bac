---
plan: NN
type: execute
wave: N # Execution wave (1, 2, 3...)
depends_on: [] # Use `01-01`/`01-01-auth-hardening`
files_modified: [] # Files this plan touches
autonomous: true # false if plan has checkpoints
requirements: [] # REQUIRED — Requirement IDs from ROADMAP this plan addresses. MUST NOT be empty.
user_setup: [] # Human-required setup (omit if empty)

must_haves:
  truths: [] # Observable behaviors
  artifacts: [] # Files that must exist
  key_links: [] # Critical connections
---

## Executive Summary

- **Problem Statement**: [What this plan accomplishes] 1-2 sentences on the pain point.
- **Proposed Solution**: [Why this matters] 1-2 sentences on the fix.
- **Success Criteria**: 3-5 measurable KPIs.

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

| Risk                     | Impact         | Mitigation                             | Severity                      |
| ------------------------ | -------------- | -------------------------------------- | ----------------------------- |
| Race condition on create | Data duplicate | DB unique constraint + optimistic lock | {critical\|high\|medium\|low} |
| ...                      | ...            | ...                                    |

## Blockers

- [ ] Clarification on payment integration
- [ ] Third-party API documentation

<tasks>
<task type="auto">
  <name>Task 1: [Action-oriented name]</name>
  <files>path/to/file.ext</files>
  <action>[Specific implementation]</action>
  <verify>[Command or check]</verify>
  <done>[Acceptance criteria]</done>
</task>
</tasks>

<verification>
[Overall phase checks]
</verification>

<success_criteria>
[Measurable completion]
</success_criteria>
