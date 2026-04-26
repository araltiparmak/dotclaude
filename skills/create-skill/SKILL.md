---
name: create-skill
description: This skill should be used when the user asks to "create a
  skill", "make a new skill", "add a skill", "write a skill for X", or
  wants to turn a workflow they just completed into a reusable skill.
  Use it whenever the user wants to package repeatable Claude behavior
  into a SKILL.md file. Also use it for adjacent requests like "turn
  this into a command" or "save this workflow for later".
model: claude-sonnet-4-6
color: purple
allowed-tools:
  - Read
  - Write
  - Bash(mkdir *)
  - Bash(git rev-parse *)
---

# Create Skill

Package a workflow into a new skill. A skill is a `SKILL.md` that tells
Claude how to handle a specific task category — what to do, in what
order, and what edge cases to handle.

Arguments passed: `$ARGUMENTS` — the text typed after the slash command
name (e.g. `/create-skill deploy-checker` → `$ARGUMENTS` is
`deploy-checker`).

---

## When NOT to create a skill

Skip it if:
- The task only runs once and won't repeat
- The procedure is obvious from context (no special steps to remember)
- It's better handled by updating CLAUDE.md or a regular prompt

A skill that never triggers again is just documentation nobody reads.

---

## Step 1: Understand the skill

If `$ARGUMENTS` names the skill, use that name and ask only for what's
missing. Otherwise ask (keep it to 1-2 exchanges):

1. What does it do? (one sentence — if it takes more, the skill is
   probably too broad; split it or narrow it)
2. When should it trigger? (what would a user actually type?)
3. What's the procedure? (or extract from the current conversation)

---

## Step 2: Choose where to put it

Decide now — don't revisit in Step 5.

- **Project-level** — `.claude/skills/<skill-name>/` inside the repo.
  Checked into version control; available to everyone on the project.
- **User-level** — `~/.claude/skills/<skill-name>/` in your home dir.
  Private; available across all your projects.

If unclear, ask. Get the repo root with `git rev-parse --show-toplevel`
for project-level placement.

---

## Step 3: Choose structure

Default to a single `SKILL.md`. Add directories only if genuinely needed:

```
<skill-name>/
├── SKILL.md        ← always required
├── references/     ← when SKILL.md body would exceed ~300 lines
│   └── <topic>.md
├── scripts/        ← when the skill runs the same code repeatedly
│   └── <script>.py
└── assets/         ← templates, images, or fixtures used in output
    └── <file>
```

Don't create any subdirectory speculatively.

---

## Step 4: Write SKILL.md

### Frontmatter

```yaml
---
name: <skill-name>          # what Claude reads; must match directory name
description: <see below>
model: claude-haiku-4-5-20251001  # optional: haiku for mechanical tasks,
                                  # sonnet (default) for writing/reasoning,
                                  # opus for high-stakes creative work
color: green                # optional: green, blue, yellow, purple, red,
                            # orange, cyan, pink — pick semantically
allowed-tools:              # Claude Code tool permission patterns —
  - Read                    # whitelist only what this skill invokes.
  - Bash(git log *)         # Use specific patterns, not blanket Bash(*).
---
```

The `name` field and directory name must match because Claude identifies
the skill by directory but reads the `name` for display and deduplication.
They're separate because the filesystem and the skill manifest have
different owners.

**Description** — the primary trigger. Rules:

- Third person: `"This skill should be used when the user asks to..."`
- Include 3-8 quoted trigger phrases users would actually type
- If the skill applies to adjacent cases the user didn't name, list those
  too — the description is the only trigger, so under-listing means
  under-triggering
- End with a "use it whenever..." clause covering edge-of-scope cases

Good:
```yaml
description: This skill should be used when the user asks to "rotate
  a PDF", "flip pages", "fix PDF orientation", or "change page layout".
  Use it whenever a PDF needs geometric transformation, even if the
  user doesn't say "PDF" explicitly.
```

Bad:
```yaml
description: Helps with PDF tasks.
```

### Body

- Imperative form — verb-first, no "you should"
- Number steps when order matters
- Keep each step concrete: what to run, what to look for, what to decide
- Include `Arguments passed: $ARGUMENTS` near the top if relevant
- Start directly at step 1 — don't open with "this skill does X"

### Worked example

See `examples/env-check.md` for a complete minimal skill. Its frontmatter:

```yaml
---
name: env-check
description: This skill should be used when the user asks to "check my
  environment", "verify setup", "debug missing deps", or "is my dev
  environment ready". Use it whenever a project setup task might fail
  due to missing tools.
allowed-tools:
  - Bash(which *)
  - Bash(node --version)
  - Bash(python3 --version)
  - Bash(git --version)
---
```

### Referencing bundled files

Name them explicitly so Claude knows they exist:

```markdown
For flag details, read `references/flags.md`.
Run `scripts/validate.py <path>` to check the output.
```

---

## Step 5: Create the files

1. `mkdir -p <root>/<skill-name>` (root from Step 2)
2. Write `SKILL.md`.
3. Create any `references/`, `scripts/`, or `assets/` files planned.
4. Confirm: skill name, install path, trigger phrases.

---

## Step 6: Register the skill

So future Claude sessions (and teammates) know it exists:

- **Project-level**: add a line to `CLAUDE.md` or the project README:
  `- <skill-name>: <one-line description> (`.claude/skills/<name>/`)`
- **User-level**: add to `~/.claude/CLAUDE.md` the same way.

Skip this step only if a registry already exists and is kept elsewhere.

---

## Edge cases

**Skill already exists**: Read the current SKILL.md and ask whether to
update it rather than replace it.

**Extracted from conversation**: Reconstruct steps from the tools called,
decisions made, and order of operations in this session. Don't ask the
user to re-explain what you can infer.

**Overlapping skills**: Note the overlap; ask whether to merge or keep
separate.

**No procedure yet**: Help think it through first — a skill without a
clear procedure won't help.

---

## Quality checklist

- [ ] `name` matches the directory name (kebab-case)
- [ ] `description` is third-person with 3-8 quoted trigger phrases
- [ ] Trigger phrases match what users would actually type, not jargon
- [ ] Description ends with a "use it whenever..." clause
- [ ] `allowed-tools` lists only what this skill invokes
- [ ] Body is imperative form (no "you should")
- [ ] Steps are concrete and ordered
- [ ] Referenced files actually exist
- [ ] SKILL.md is under ~400 lines
- [ ] Skill is registered in CLAUDE.md or README
