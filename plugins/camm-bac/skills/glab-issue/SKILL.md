---
name: glab-issue
description: 'Create GitLab issues from plans,requirements or specifications and generate draft merge requests (MRs) linked to issue IDs.Use this skill to automate GitLab issue/MR workflows with structured content'
---

# Check install

1. Shell Check GitLab CLI availability and repository remote

```bash
cd ~/workspace/ommt && git remote -v && echo '---' && (snap run glab --version || glab --version || true)
```

2. Verify GitLab authentication and repository access via snap glab

```bash
cd /home/michael/workspace/ommt && snap run glab auth status && echo '---' && snap run glab repo view -w
```

## Prerequisites

1. Install `glab`: Download from https://gitlab.com/gitlab-org/cli or use package manager
2. Authenticate: Run `glab auth login` with a PAT that has scopes: `api`, `read_api`, `write_repository`
3. Set GitLab instance: `glab config set host https://gitlab.tech.orange` (or export `GL_HOST=https://gitlab.tech.orange`)
4. From a cloned repository, verify access: `glab repo view -w` to ensure `glab` detects the correct remote

## GitLab Issue Note

Since `glab`command isn't available in this environment, you must replace by `snap run glab`

## Workflow: Phase 1 → Create Issue from Plan

### Input

- Plan document (markdown, text, or specifications)
- Issue title and description content

### Steps

1. **Prepare Issue Content**
    - Extract title from plan (or use provided title)
    - Use issue content as issue description
    - Preserve formatting and structure

2. **Create Issue**

    ```bash
    glab issue create \
      --title "Feature: {title}" \
      --description "$(cat issue.md)" \
      -R /mail/agenda-contacts/ommt
    ```

    - Output: Issue created with IID (e.g., #42)
    - Note the IID for next phase

3. **Verify Creation**
    - Check issue URL:

    ```bash
        glab issue view {IID}
    ```

    - Confirm issue is visible and properly formatted

## Workflow: Phase 2 → Create Draft MR from Issue

### Input

- Issue IID from Phase 1 (or provided directly)

### Steps

1. **Create Draft Merge Request**

```bash
    glab mr create --i {IID}  --target-branch next-ai  --draft  --fill --push
```

    - Links MR to issue automatically
    - Marks as draft (ready for work, not for review)
    - Creates branch with naming convention: `{IID}-{slug}`

2. **Checkout Branch (Optional)**

    ```bash
    glab mr checkout {MR_IID}
    ```

    - Switches to newly created branch
    - Ready for development

3. **Verify Creation**
    - Check MR URL output
    - Confirm draft status and linked issue

### Update MR description

```bash
cd ~/workspace/ommt && snap run glab mr update <iid> --description "$(cat <plan_path>)
```

## Commands Reference

See [guidelines](references/guidelines.md) for detailed `glab` command documentation including:

- **Quick reference**: Common commands for issues, MRs, and CI/CD
- **Comments & discussions**: How to add comments, threaded replies, and diff comments
- **API calls**: Using `glab api` for advanced operations
- **Common mistakes**: Patterns to avoid and best practices

Quick reference for this workflow:

| Task            | Command                                               |
| --------------- | ----------------------------------------------------- |
| Create issue    | `glab issue create --title "..." --description "..."` |
| List issues     | `glab issue list --all`                               |
| View issue      | `glab issue view {IID}`                               |
| Create MR       | `glab mr create -i {IID} --draft --push`              |
| List MRs        | `glab mr list --all`                                  |
| View MR         | `glab mr view {IID}`                                  |
| Checkout branch | `glab mr checkout {IID}`                              |

## Best Practices

- Always confirm issue/MR creation with the user before executing remote operations
- Use descriptive titles that reflect feature/issue scope
- Preserve plan content structure in issue description
- Verify GitLab connectivity before running commands
- Handle authentication failures gracefully
- Report issue/MR URLs and IIDs clearly for user reference

## Output Format

**After Issue Creation:**

```
✅ Issue Created Successfully
- Title: Feature: {title}
- IID: #{issue_id}
- URL: https://gitlab.com/org/repo/-/issues/{issue_id}
```

**After MR Creation:**

```
✅ Draft MR Created Successfully
- Title: {issue_title}
- MR IID: !{mr_id}
- Branch: {branch_name}
- URL: https://gitlab.com/org/repo/-/merge_requests/{mr_id}
```
