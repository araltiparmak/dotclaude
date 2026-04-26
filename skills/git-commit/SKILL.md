---
name: git-commit
description: This skill should be used when the user asks to "create a git
  commit", "commit my changes", "make a commit", "commit this", "save my
  changes to git", "write a commit message", or "push a commit". Use it
  whenever the user wants to record current work into git history, even if
  they don't say "commit" explicitly.
model: claude-haiku-4-5-20251001
color: green
allowed-tools:
  - Read
  - Write
  - Bash(git status)
  - Bash(git diff *)
  - Bash(git log *)
  - Bash(git add *)
  - Bash(git commit *)
  - Bash(ls *)
---

# Git Commit

Arguments passed: `$ARGUMENTS`

## Step 1: Update CLAUDE.md and README.md

Read the current `CLAUDE.md`, `README.md`, and `skills/` directory. If either file is missing or out of sync with the actual skills present, rewrite it to reflect the current state. Stage any changes:

```bash
git add CLAUDE.md README.md
```

Skip this step if the repo has no `skills/` directory.

## Step 2: Understand what's changed

Run these in parallel:

```bash
git status
git diff --staged
git diff
git log --oneline -5
```

Use `git status` to identify untracked files and the staging state. Use
`git diff --staged` + `git diff` to read the actual changes. Use `git log`
to match the repo's existing commit message style.

## Step 3: Decide what to stage

If files are already staged, commit exactly those — do not add more without
asking.

If nothing is staged:
- Stage only files relevant to the user's stated intent (or all modified
  files if no intent was given).
- Never stage files that look like secrets, build artifacts, or unrelated
  changes without asking first.
- Use specific file paths, not `git add -A` or `git add .`.

## Step 4: Draft the commit message

Rules:
- Subject line: ≤72 characters, imperative mood ("add X", "fix Y", not
  "added X" or "fixes Y")
- Focus on *why*, not *what* — the diff already shows what changed
- No trailing period on the subject line
- If `$ARGUMENTS` describes the change, use it to inform the message

Match the style from `git log` (e.g. if the repo uses conventional commits
like `feat:` / `fix:`, follow that format).

## Step 5: Commit

Pass the message via heredoc to preserve formatting:

```bash
git commit -m "$(cat <<'EOF'
<subject line>

<optional body>

EOF
)"
```

## Step 6: Handle hook failures

If a pre-commit hook fails, the commit did NOT happen. Do not use `--amend`.
Instead:
1. Read the hook's error output
2. Fix the underlying issue (format errors, lint failures, etc.)
3. Re-stage the affected files
4. Create a **new** commit

## Edge cases

**Nothing to commit**: Report the clean state; do not create an empty commit.

**Untracked files only**: Ask whether to include them before staging.

**Mixed concerns in one diff**: Note that the changes span multiple concerns
and ask whether the user wants one commit or several.

**Merge conflicts present**: Do not commit. Surface the conflicts and ask
the user to resolve them first.
