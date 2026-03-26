---
name: git-commit
description: 'Execute git commit with conventional commit message analysis, intelligent staging, and message generation. Use when user asks to commit changes, create a git commit, or mentions "/commit". Supports: (1) Auto-detecting type and scope from changes, (2) Generating conventional commit messages from diff, (3) Intelligent file staging for logical grouping'
---

# Git Commit with Conventional Commits

## Overview

Create standardized, semantic git commits using the Conventional Commits specification. Analyze the actual diff to determine appropriate type, scope, and message.

## Conventional Commit Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Commit Types

| Type       | Purpose                        |
| ---------- | ------------------------------ |
| `feat`     | New feature                    |
| `fix`      | Bug fix                        |
| `docs`     | Documentation only             |
| `style`    | Formatting/style (no logic)    |
| `refactor` | Code refactor (no feature/fix) |
| `perf`     | Performance improvement        |
| `test`     | Add/update tests               |
| `build`    | Build system/dependencies      |
| `ci`       | CI/config changes              |
| `chore`    | Maintenance/misc               |
| `revert`   | Revert commit                  |

### Breaking Changes

Append `!` after the type/scope to indicate a breaking change:

```
feat(api)!: remove deprecated /users endpoint
```

Include a `BREAKING CHANGE:` footer in the body when explanation is needed.

## Workflow

### 1. Analyze Diff

Check staged changes first. If nothing is staged, fall back to the working tree diff.

```bash
git diff --staged
# If nothing staged:
git diff
# Always check status
git status --porcelain
```

### 2. Stage Files

Never mix unrelated changes in a single commit. Group files by logical relationship and commit each group separately.

If nothing is staged or changes need to be split:

```bash
# Stage specific files
git add path/to/file1 path/to/file2

# Stage by pattern (quote globs for shell safety)
git add '*.test.*'
git add 'src/components/*'

# Interactive staging for partial files
git add -p
```

**Never commit secrets** (.env, credentials.json, private keys).

### 3. Generate Commit Message

Analyze the diff to determine:

- **Type**: What kind of change is this?
- **Scope**: The affected module or directory (e.g., `auth`, `api`, `cli`). Omit if the change is project-wide.
- **Description**: One-line summary of what changed (present tense, imperative mood, <72 chars).
- **Body** (preferred): Briefly explain _what_ changed and _why_, not _how_. Wrap at 128 chars.
- **Footer** (when applicable): `Closes #123`, `Refs #456`, or `BREAKING CHANGE: <explanation>`.

### 4. Execute Commit

Never use literal `\n` in commit message strings. Use separate `-m` flags for each paragraph — git inserts a blank line between them automatically.

```bash
# Subject only
git commit -m "<type>[scope]: <description>"

# Subject + body (use multiple -m flags)
git commit -m "<type>[scope]: <description>" -m "<body-line-1>" -m "<body-line-2>"

# Subject + body + footer
git commit -m "<type>[scope]: <description>" -m "<body>" -m "<footer>"
```

## Best Practices

- One logical change per commit
- Present tense: "add" not "added"
- Imperative mood: "fix bug" not "fixes bug"
- Keep description under 72 characters
- Always include a body unless the change is trivially obvious

## Git Safety Protocol

- NEVER run destructive commands (--force, hard reset) without explicit request
- NEVER skip hooks (--no-verify) unless user asks
- NEVER force push to main/master
- NEVER modify global git config
- If commit fails due to hooks, fix the issue and create a NEW commit (don't amend)
