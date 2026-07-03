---
name: write-tdd-plan
description: "Plan to TDD phase for launching a TDD cycle . Enforces test-driven development with 80%+ coverage from a  `*.plan.md` implementation .Use this skill when starting a new feature or fix development in a TDD cycle.This skill takes the current conversation context or from the input `context` file and codebase understanding and produces a TDD plan. Do NOT interview the user — just synthesize what you already know."
---

# When to use this skill

- Starting a new feature or fix development cycle
- Defining requirements for AI-powered features
- Create a TDD plan

# Operational Workflow

1. If the plan is provided, read it as raw text; otherwise, create one from the input `context` file.
   extract the user journeys and acceptance criteria from that plan first. Only write new journeys for gaps the plan does not cover.

2. Validate and normalize extracted milestones, tasks, user journeys, acceptance criteria, and validation intent before using them.

3. Convert each approved planned behavior into a testable guarantee. If the plan already contains user journeys, reuse them rather than inventing new ones.

4. Generate the document using the **Strict TDD-PLAN Schema** below.

## Strict TDD-PLAN Schema

You **MUST** follow this exact structure for the output:

```md
### 1. Summary

- **Problem Statement**: 1-2 sentences on the pain point.
- **Proposed Solution**: 1-2 sentences on the fix.
- **Success Criteria**: 3-5 measurable KPIs.

### 2. User Experience & Functionality

- **User Stories**: `As a [user], I want to [action] so that [benefit].`
- **Scope**: Included behavior.
- **Acceptance Criteria**: Bulleted list of "Done" definitions for each story.
- **Non-Goals**: What are we NOT building?

### 3. Technical Specifications

- **Architecture Overview**: Data flow and component interaction.
- **Integration Points**: APIs, DBs, and Auth.
- **Security & Privacy**: Data handling and compliance.

### 4. Risks & Roadmap

- **Technical Risks**: Latency, cost, or dependency failures.
```
