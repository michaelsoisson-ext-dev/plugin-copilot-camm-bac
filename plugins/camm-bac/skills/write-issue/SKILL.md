---
name: write-issue
description: Create a comprehensive issue from a Jira ticket or specification by synthesizing business rules, requirements, user journeys, and acceptance criteria. Use this skill when you need to anchor planned behavior to testable guarantees and document it in a strict issue schema without interviewing the user. Do NOT interview the user — just synthesize what you already know."
---

# Operational Workflow

1.  Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the context

2.  If a Jira ticket or specification is provided, read it as raw text; otherwise, create one from the input `context` file. Extract the user journeys and acceptance criteria from that plan first. Only write new journeys for gaps the plan does not cover.

3.  Validate and normalize extracted milestones, tasks, user journeys, acceptance criteria, and validation intent before using them.

4.  Convert each approved planned behavior into a testable guarantee. If the plan already contains user journeys, reuse them rather than inventing new ones.

5.  Generate the document using the [**Strict Issue Schema**](references/issue-template.md) below and save it `.ai-camm-bac/plans/issue-<unique-name>`
