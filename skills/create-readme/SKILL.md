---
name: create-readme
description: This skill should be used when the user asks to "create a
  readme", "write a readme", "add a readme", "generate a readme", "make
  a readme for this project", or "document this project". Use it whenever
  the project is missing a README or the existing one needs a full rewrite,
  even if the user doesn't say "README" explicitly.
allowed-tools:
  - Read
  - Write
  - Bash(find *)
  - Bash(git log *)
  - Bash(ls *)
---

# Create README

Arguments passed: `$ARGUMENTS`

## Step 1: Gather context

Run in parallel:

```bash
find . -maxdepth 2 -name "*.json" -o -name "*.gradle" -o -name "*.kts" \
  -o -name "Makefile" -o -name "Dockerfile" | head -20
ls -1
git log --oneline -10
```

Then read whichever of these exist:
- `package.json` — name, description, scripts, dependencies
- `build.gradle` / `build.gradle.kts` — project name, dependencies, tasks
- `Makefile` — available targets
- `CLAUDE.md` — architecture and commands already documented there
- `.claude/skills/` — list any bundled skills worth mentioning
- Any existing `README.md` — extract anything worth keeping

Goal: understand what the project does, how to install it, and how to run it — without making anything up.

## Step 2: Identify sections to include

Only include a section if you have real content for it. Do not invent placeholder text.

| Section | Include when |
|---|---|
| Title + one-liner | Always |
| Description / motivation | Purpose isn't obvious from the name |
| Prerequisites | There are non-obvious dependencies |
| Installation | There are setup steps beyond `git clone` |
| Usage / Quick start | There's a command or API entry point |
| Configuration | There are env vars or config files |
| Contributing | It's a public or team repo |
| License | A LICENSE file exists |

Skip any section where you'd be writing vague filler.

## Step 3: Write the README

Rules:
- Lead with what the project *does*, not what it *is* ("Syncs Figma tokens to CSS variables" not "A tool for...")
- Use fenced code blocks with language tags for all commands and code
- Keep installation steps numbered and exact — copy commands from package.json scripts or Makefile targets
- If `$ARGUMENTS` specifies a focus or tone, apply it

## Step 4: Check for an existing README

If `README.md` already exists, read it first. Preserve any sections that have content not derivable from the code (e.g. deployment notes, architectural decisions, external links). Replace only what's stale or missing.

## Step 5: Write the file

Write to `README.md` at the repo root (or the path the user specified).

Confirm: what sections were included and which were skipped and why.
