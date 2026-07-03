---
name: write-issue
description: Create an issue from a jira ticket or specs. Use this skill when you need to create issues from plans or specifications. Do NOT interview the user — just synthesize what you already know."
---

Refactor jira ticket to a well formatted issue. Generates `issue.md` with requirements development and business rules.
This skill takes the current conversation context or specs and codebase understanding and produces a issue .

# When to use this skill

- Refactor business rules from specs to an issue
- Defining requirements for AI-powered features
- Create an Issue well formatted

# Operational Workflow

1.  Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the context

2.  If a jira ticket or spec is provided, read it as raw text; otherwise, create one from the input `context` file.
    extract the user journeys and acceptance criteria from that plan first. Only write new journeys for gaps the plan does not cover.

3.  Validate and normalize extracted milestones, tasks, user journeys, acceptance criteria, and validation intent before using them.

4.  Convert each approved planned behavior into a testable guarantee. If the plan already contains user journeys, reuse them rather than inventing new ones.

5.  Generate the document using the **Strict Issue Schema** below.

## Strict Issue Schema

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

| Risk                     | Impact         | Mitigation                             | Severity                      |
| ------------------------ | -------------- | -------------------------------------- | ----------------------------- |
| Race condition on create | Data duplicate | DB unique constraint + optimistic lock | {critical\|high\|medium\|low} |
| ...                      | ...            | ...                                    |

- **Technical Risks**: Latency, cost, or dependency failures.
```
