---
name: git-push
description: This skill should be used when the user asks to "push", "push my changes", "push to remote", "push this branch", "push commits", "git push", "send my changes to GitHub", or "upload my changes". Use it whenever the user wants to upload local commits to a remote repository, even if they don't use the word "push" explicitly.
model: claude-haiku-4-5-20251001
color: blue
allowed-tools:
  - Bash(git status)
  - Bash(git branch *)
  - Bash(git remote *)
  - Bash(git push *)
  - Bash(git log *)
  - Bash(git rev-parse *)
---

## Step 1: Check current state

Run in parallel:

```bash
git status
git log --oneline -5
git rev-parse --abbrev-ref HEAD
```

If `git status` shows uncommitted or unstaged changes, warn the user and ask whether to proceed anyway or commit first. Do not push unless the user confirms.

## Step 2: Check what will be pushed

Determine whether the branch already has a remote tracking branch:

```bash
git rev-parse --abbrev-ref @{upstream} 2>/dev/null
```

- **Has upstream** — run `git log @{u}..HEAD --oneline` to show commits that will be pushed. If it returns nothing, report "already up to date" and stop.
- **No upstream** — note that this is a new branch with no remote counterpart and confirm the push target (default: `origin`).

## Step 3: Warn before pushing to main or master

If the current branch is `main` or `master`, confirm with the user before pushing directly. Mention that pushing to the default branch bypasses PR review.

## Step 4: Push

- **New branch** (no upstream):
  ```bash
  git push -u origin <branch-name>
  ```
- **Existing tracked branch**:
  ```bash
  git push
  ```

## Step 5: Handle failures

| Error | Action |
|---|---|
| `rejected ... non-fast-forward` | Tell the user to pull and rebase/merge first: `git pull --rebase` |
| `remote: Permission denied` | Confirm the user has write access to the remote |
| `error: src refspec ... does not match any` | The branch name is wrong; show `git branch` output |
| `[remote rejected] ... protected branch` | The branch is protected; push to a feature branch and open a PR instead |

## Edge cases

**Nothing to push**: Report "already up to date with remote" and stop — do not create a new commit or amend.

**Multiple remotes**: Show the list via `git remote -v` and ask which remote to push to.

**Detached HEAD**: Do not push. Explain the detached state and ask the user to check out a branch first.
