---
name: issue-to-bdd-plan
description: 'Issue to BDD plan phase for launching a TDD cycle . Enforces behvor-driven development with 80%+ coverage from an `issue.md` or `*.plan.md`. Use when applying Behavior-Driven Development  patterns including Given-When-Then structure, feature files, and acceptance criteria. Use when writing BDD-style tests and specifications. Do NOT interview the user — just synthesize what you already know.'
---

# Operational Workflow

1. **Requirements extraction** break down the jira ticket into a clear [Behavor-driven Development Flow](references/bdd.principles.md)

2. Helped with [Bdd pattern](references/bdd-pattern.md) convert each approved planned behavior into maintainable [BDD scenarios](references/bdd-scenario.md) that effectively capture business requirements and drive implementation.

3. Prepare a transition to TDD cycle with keeping a mapping from BDD plan -> test target -> RED evidence -> GREEN evidence.

4. Output the result as markdwon checklist `bdd-plan.md` using `[]` for each step and and save it `./plans/bdd-plan-<unique-name>` at then end.
