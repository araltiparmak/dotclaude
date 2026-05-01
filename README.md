# skills

Personal Claude Code skills — reusable workflows packaged as `SKILL.md` files that Claude picks up automatically.

## What are skills?

A skill is a `SKILL.md` that tells Claude how to handle a specific repeatable task: what steps to follow, what tools to use, and what edge cases to handle. Claude triggers the right skill based on what you type.

## Using a skill

**Option 1 — Symlink (recommended)**

Clone the repo anywhere and symlink its `skills/` folder into `~/.claude/`. Skills are available across all projects and stay in sync with `git pull` — no copying needed, existing `~/.claude` config stays untouched.

```bash
git clone https://github.com/araltiparmak/skills.git <path/to/skills>
ln -s <path/to/skills>/skills ~/.claude/skills
```

**Option 2 — Copy skills manually**

Clone wherever you like, then copy individual skills into your project or user config:

```bash
git clone https://github.com/araltiparmak/skills.git

# user-level (available across all projects)
cp -r skills/<skill-name> ~/.claude/skills/

# project-level (checked into version control)
cp -r skills/<skill-name> <your-repo>/.claude/skills/
```

## Skills

| Skill | Trigger phrases |
|---|---|
| `create-skill` | "create a skill", "make a new skill", "turn this into a command" |
| `git-commit` | "commit my changes", "create a git commit", "commit this" |
| `create-readme` | "create a readme", "write a readme", "document this project" |
| `create-pr` | "create a pull request", "open a PR", "make a PR", "submit a PR" |
| `git-push` | "push", "push my changes", "push to remote", "push this branch" |

## Adding a new skill

```bash
# in Claude Code
create a skill for <what it does>
```

The `create-skill` skill guides the process: it asks what the workflow does, where to install it, and writes the `SKILL.md` following the conventions in `CLAUDE.md`.
