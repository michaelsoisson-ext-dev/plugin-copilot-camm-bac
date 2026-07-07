```md
# [issue or story title]

## Summary

### Problem Statement

    [1-2 sentences on the pain point]

### Proposed Solution

    [1-2 sentences on the fix.]

## User Stories

[ Bulleted list of user stories with pattern : `As a [user], I want to [action] so that [benefit].`]

### Scope

    [Included behavior]

### Acceptance Criteria

[ Bulleted list of "Done" definitions for each story ]

## Technical Specifications

### Architecture Overview

Data flow and component interaction.

### High level Implementations

modules that will be built/modified , The interfaces only of those modules that will be modified ,

### Integration Points

API contracts, Schema changes and Auth.

## Risks & Roadmap

| Risk                     | Impact         | Mitigation                             | Severity                      |
| ------------------------ | -------------- | -------------------------------------- | ----------------------------- |
| Race condition on create | Data duplicate | DB unique constraint + optimistic lock | {critical\|high\|medium\|low} |
| ...                      | ...            | ...                                    |

### Security & Privacy

Data handling and compliance.

### Technical Risks

Latency, cost, or dependency failures.
```
