---
name: glab
description: Generate a merge request review (mr) in Gitlab
---

# GitLab MR Review

## Prerequisite

1. Install `glab`, then run `glab auth login` with a PAT that has the scopes api/read_api/write_repository.
2. Set the internal instance using `glab config set host https://gitlab.tech.orange` (or export `GL_HOST=https://gitlab.tech.orange`).
3. From a cloned repository, verify access with `glab repo view -w` to ensure that `glab` detects the correct remote.

## When to use this Skill

- Any request containing “review”, “code review”, “review !123”, “review MR-123”, or a request to check a diff before merging.
- When a user requests to inspect changes related to a given issue or to check the quality/security status of a branch.

## Glab Workflow

1. **Identify the MR**

- Provided IID: `glab mr view <iid> --comments --output json -R namespace/projet`.
- No IID: `glab mr list --state opened --reviewer @me --output json` to suggest pending MRs, then ask which one to process.
- From an issue: `glab mr issues <iid_issue>` to retrieve the associated MR before proceeding.

3. **Analyze Changes**

- Use `glab mr diff <iid> --color=never` (and `--raw` if you want to chain to `delta`, `less`, `grep`, etc.).
- If you need to test locally: `glab mr checkout <iid>` creates the working branch ready to run tests/interns.

4. **Existing Discussions**

- `glab mr view <iid> --comments` to view all threads and avoid duplicates before adding a comment.

5. **CI/CD Status**

- `glab ci status --branch <source_branch>` for a quick overview.
- `glab pipeline ci view -b <source_branch>` to analyze jobs, restart (`Ctrl+R`) or cancel (`Ctrl+D`) directly from the terminal.

6. **Summary & Report**

- Use the “Executive summary / Strengths / Issues (Critical, Important, Suggestions) / Security / Performance / Tests / Docs / Verdict” template from the initial skill.
- Quantify modified rows using `glab mr view --output json | jq` (extractions, additions/deletions) to populate the “Statistics” section.

7. **Feedback Publication (Optional)**

- Always request confirmation before any remote action.
- General comment: `glab mr note <iid> -m "Question about…?"`.
- To mark a personal follow-up, `glab mr todo <iid>` adds the MR to your list.

## Best practices

- Limit the review to the changes in the diff.
- Ask questions rather than give instructions.
- Review the pipeline before concluding.
- Mention positive points to balance the review.
- Rank findings by severity (Critical > Important > Suggestions).
- Verify the impacts on security, performance, testing, and documentation.
- Confirm with the user before submitting comments in the MR.

## Report template

```
# Code Review: !{MR_IID} - {MR_TITLE}

## Executive Summary
{aperçu}

## Merge Request Details
- **Project**: {path}
- **Author**: @{author}
- **Source**: {source_branch} → **Target**: {target_branch}
- **Pipeline Status**: {status}
- **Approvals**: {current}/{required}

## Statistics
| Metric | Count |
|--------|-------|
| Files Changed | {count} |
| Lines Added | +{additions} |
| Lines Removed | -{deletions} |
| Commits | {commit_count} |

## Strengths
- …

## Issues Found
### 🔴 Critical
{…}

### 🟠 Important
{…}

### 🟢 Suggestions
{…}

## Security Review
{…}

## Performance Review
{…}

## Testing Recommendations
- …

## Documentation Needs
- …

## Verdict
{APPROVED | CHANGES_REQUESTED | NEEDS_DISCUSSION}
```
