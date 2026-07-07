---
name: issue-to-tdd-plan
description: 'Issue to TDD plan phase for launching a TDD cycle . Enforces test-driven development with 80%+ coverage from an `issue.md` or `*.plan.md` implementation .Use this skill when starting a new feature or fix development in a TDD cycle.This skill takes an issue and produces a TDD plan. Do NOT interview the user — just synthesize what you already know.'
---

# Operational Workflow

1. **Requirements extraction** break down the jira ticket into a clear **Test-driven Development Flow**
   where each step represent a test to be written and the logic to be impemented to pass use

2. Convert each approved planned behavior into a testable guarantee. If the issue ready contains user journeys, reuse them rather than inventing new ones.

3. **Definition of Done** - Use issue checklist items as test validation points

4. Keep a mapping from plan task -> test target -> RED evidence -> GREEN evidence.

5. Output the result as markdwon checklist `tdd-plan.md` using `[]` for each step and and save it `.ai-camm-bac/plans/tdd-plan-<unique-name>` at then end.

### Test-First Guidelines

- **Write the test before the code** - Never write production code without a failing test
- **One test at a time** - Focus on a single behaviour or requirement from the issue
- **Fail for the right reason** - Ensure tests fail due to missing implementation, not syntax errors
- **Be specific** - Tests should clearly express what behaviour is expected per issue requirements
