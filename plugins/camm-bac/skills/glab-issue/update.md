### Phase 2: Create GitLab Issue (glab-issue skill)

**Input:** plan.md from Phase 1

**Action 1: Create Issue from Plan**

```bash
glab issue create \
  --title "Feature: User Registration API with Email Verification" \
  --description "$(cat plan.md)"

# Output: Created issue #42
# Note the IID (42) for next step
```

**Output:**

- GitLab issue created with full plan as description
- Issue IID: 42 (example)
- Issue URL: `https://gitlab.com/org/repo/-/issues/42`
- Links to related issues automatically parsed from plan.md

**Decision Point:**

- ✅ Issue created successfully → Continue to Action 2
- ❌ Issue creation failed → Check permissions, repo access

---

### Phase 3: Create Draft MR (glab-issue skill)

**Input:** Issue IID from Phase 2

**Action 2: Create Draft MR from Issue**

```bash
glab mr create \
  --issue 42 \
  --draft \
  --push

# Output: Created merge request !156
# Branch name: shown in output (e.g., 42-user-registration-api)
```
