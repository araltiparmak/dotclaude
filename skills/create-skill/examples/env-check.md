---
name: env-check
description: This skill should be used when the user asks to "check my
  environment", "verify setup", "debug missing deps", or "is my dev
  environment ready". Use it whenever a project setup task might fail
  due to missing tools, even if the user doesn't mention env checks.
allowed-tools:
  - Bash(which *)
  - Bash(node --version)
  - Bash(python3 --version)
  - Bash(git --version)
---

# Environment Check

Arguments passed: `$ARGUMENTS`

## Step 1: Check required tools

Run each version check and collect failures:

```bash
which node && node --version
which python3 && python3 --version
which git && git --version
```

## Step 2: Report

List what's missing. If nothing is missing, say so in one line.
