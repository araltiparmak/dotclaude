# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a personal Claude Code configuration repository ("dotclaude") — a collection of reusable skills that can be installed at the project or user level.

## Repository structure

```
skills/
  <skill-name>/
    SKILL.md          ← required: skill definition and procedure
    examples/         ← optional: worked examples referenced by SKILL.md
    references/       ← optional: detail docs when SKILL.md body would exceed ~300 lines
    scripts/          ← optional: scripts the skill runs repeatedly
    assets/           ← optional: templates or fixtures used in output
```

## Skills

Skills are SKILL.md files that tell Claude how to handle a specific repeatable task. They are installed either:
- **Project-level**: `.claude/skills/<skill-name>/` inside a repo (checked into version control)
- **User-level**: `~/.claude/skills/<skill-name>/` (private, available across all projects)

### SKILL.md conventions

Every SKILL.md requires YAML frontmatter:

```yaml
---
name: <skill-name>       # must match directory name (kebab-case)
description: <trigger>   # third-person, 3-8 quoted trigger phrases + "use it whenever..." clause
allowed-tools:           # whitelist only tools this skill actually invokes
  - Read
  - Bash(git log *)      # use specific patterns, not blanket Bash(*)
---
```

Body rules:
- Imperative form (verb-first, no "you should")
- Number steps when order matters
- Include `Arguments passed: $ARGUMENTS` near the top if the skill accepts slash-command arguments
- Reference bundled files by name so Claude knows they exist

### Registering a new skill

After creating a skill, add a line to this file under the **Installed skills** section below (or to the consuming project's CLAUDE.md):

```
- <skill-name>: <one-line description> (`skills/<name>/`)
```

## Installed skills

- create-skill: Packages a workflow into a new reusable SKILL.md (`skills/create-skill/`)
- git-commit: Creates a git commit — stages files, drafts a message, handles hook failures (`skills/git-commit/`)
- create-readme: Writes or rewrites a README.md based on actual project content (`skills/create-readme/`)
- create-pr: Creates a GitHub pull request — pushes branch, drafts title/body, returns PR URL (`skills/create-pr/`)
