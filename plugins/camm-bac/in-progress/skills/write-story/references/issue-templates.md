# Issue templates

Use these templates as starting points. Adapt them to the PRD instead of filling them mechanically.

## 1. Main epic issue

```md
# [Initiative title]

## Summary

[Short explanation of the initiative and the value it creates.]

## Problem

[What problem this initiative solves.]

## Scope

- [In-scope capability or workstream]
- [In-scope capability or workstream]

## Success indicators

- [Observable outcome]
- [Observable outcome]

## Planned child issues

- [ ] [Child issue title]
- [ ] [Child issue title]

## Dependencies

- [Dependency or prerequisite]

## Notes

- [Assumption, risk, or open question]
```

Suggested labels:

## 2. User story or capability issue

```md
# [Capability or story title]

## Summary

[Short explanation of the user-facing capability.]

## User value

[Why this matters to the user.]

## Scope

- [Included behavior]
- [Included behavior]

## Acceptance criteria

- [Observable acceptance criterion]
- [Observable acceptance criterion]

## Dependencies

- [Blocking dependency if any]

## Related PRD references

- [Capability group, story IDs, or section names]
```

Suggested labels:

- `type:user-story`
- matching priority label
- area label when stable

## 3. Technical issue

```md
# [Technical foundation title]

## Summary

[Technical or enabling work required for delivery.]

## Why this exists

[Why the team needs this issue separately.]

## Scope

- [Technical task or outcome]
- [Technical task or outcome]

## Completion conditions

- [Observable completion condition]
- [Observable completion condition]

## Dependencies

- [Upstream or downstream issue]
```

Suggested labels:

- `type:technical`
- matching priority label

## 4. QA / testing issue

```md
# [QA or validation title]

## Summary

[What needs to be validated.]

## Test focus

- [Workflow or scenario]
- [Workflow or scenario]

## Exit criteria

- [Condition that signals testing is complete]
- [Condition that signals testing is complete]

## Dependencies

- [Feature or environment dependency]
```

Suggested labels:

- `type:qa`

## 5. Rollout or documentation issue

```md
# [Rollout or documentation title]

## Summary

[Launch, communication, migration, or documentation work.]

## Scope

- [Included item]
- [Included item]

## Done when

- [Observable completion condition]
- [Observable completion condition]

## Dependencies

- [Dependent feature or milestone]
```

Suggested labels:

- `type:rollout` or `type:docs`
