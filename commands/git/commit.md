---
description: Commit changes in the working directory with meaningful messages that explain both what was changed and why.
---

# Git Commit Command

**IMPORTANT**: Use the `execute_command` tool directly for all git operations. Do NOT use any MCP servers or tools.

You are tasked with committing changes in the working directory with meaningful commit messages that explain both **what** was changed and **why**.

## Process

1. Use `git status` and `git diff` to review changes
2. Stage files with `git add` or stage partial changes with `git add -p`
3. Commit with clear messages using `git commit -m "message"`

## Selective Staging

**Prefer `git add -p` (patch mode) when**:
- File contains multiple unrelated changes
- Only part of a file should be in this commit
- Separating logical changes improves git history
- Creating atomic commits for better rollback capability

**Use `git add <file>` when**:
- Entire file represents a single logical change
- All changes in file belong together

## Commit Message Guidelines

**Format**: `type(scope): description`

**Types**: feat, fix, refactor, docs, style, test, chore

**Rules**:
- Use present tense ("Add" not "Added")
- Start with verb
- Keep first line under 72 characters
- Explain what AND why
- Group related changes in single commits

## Examples

```
feat(auth): add JWT token refresh mechanism

Prevents unexpected logouts during active sessions
```

```
fix(api): handle null response in user profile

Prevents crash when profile data missing
```

```
refactor(utils): extract date formatting utility

Reduces duplication and centralizes formatting logic
```

## Instructions

Use `execute_command` to run git commands directly. Review changes, stage appropriate files, and create meaningful commits that help future developers understand the codebase evolution.
