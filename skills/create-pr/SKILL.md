---
name: create-pr
description: This skill should be used when the user asks to "create a pull
  request", "open a PR", "make a PR", "submit a PR", "raise a PR", or
  "open a pull request". Use it whenever the user wants to propose changes
  for review on GitHub, even if they don't say "pull request" explicitly.
model: claude-sonnet-4-6
color: blue
allowed-tools:
  - Bash(git status)
  - Bash(git log *)
  - Bash(git diff *)
  - Bash(git push *)
  - Bash(git branch *)
  - Bash(git rev-parse *)
  - Bash(gh pr create *)
  - Bash(gh pr view *)
---

# Create Pull Request

Arguments passed: `$ARGUMENTS`

## Step 1: Understand the branch state

Run in parallel:

```bash
git status
git branch -vv
git log --oneline main..HEAD
git diff main...HEAD
```

Identify:
- Current branch name
- Whether the branch has a remote tracking branch and is pushed
- All commits that will be in the PR (everything ahead of `main`)
- The full diff to understand what changed

## Step 2: Push if needed

If the branch has no remote tracking branch or is ahead of remote:

```bash
git push -u origin HEAD
```

## Step 3: Draft the PR

**Title** — ≤70 characters, imperative mood, describes the change not the task ("add retry logic to payment client" not "payment client changes").

If `$ARGUMENTS` describes the PR, use it to inform the title.

**Body** — use this template:

```
## Summary
- <bullet: what changed and why>
- <bullet: only if genuinely distinct from above>

## Test plan
- [ ] <specific thing to verify>
- [ ] <another specific check>
```

Rules:
- Summary bullets: focus on *why*, not *what* — the diff shows what
- 1–3 bullets in Summary; skip if the title is already self-explanatory
- Test plan items must be specific and checkable, not generic ("tested locally")
- Omit any section you'd be filling with vague filler

## Step 4: Create the PR

```bash
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
- ...

## Test plan
- [ ] ...
EOF
)"
```

Add `--draft` if the changes aren't ready for review.

## Step 5: Return the PR URL

Run `gh pr view --json url -q .url` and return the URL to the user.

## Edge cases

**Already on main**: Stop and ask which branch to use.

**No commits ahead of main**: Nothing to PR — report it and stop.

**PR already exists for this branch**: Run `gh pr view` to show it instead of creating a duplicate.
