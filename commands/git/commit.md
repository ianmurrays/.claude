---
description: Commit changes in the working directory with meaningful messages that explain both what was changed and why.
---

# Git Commit Command

## Pre-run Commands

- Working tree status: !`git status`
- Unstaged changes: !`git diff`
- Staged changes: !`git diff --staged`
- Recent commit history: !`git log --oneline -5`

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

## Supported Arguments

Parse additional instructions from `$ARGUMENTS` to guide commit behavior:

### Flag Implementation

When processing arguments:
- `--all` or `-a`: Stage all modified files with `git add .`
- `--patch` or `-p`: Force use of patch mode with `git add -p`
- `--no-patch`: Skip patch mode, stage entire files
- `--type <type>`: Override commit type (feat, fix, refactor, etc.)
- `--scope <scope>`: Specify commit scope

### Extra Instructions for Commit Generation

Any additional text in `$ARGUMENTS` (after flags are parsed) should be treated as instructions for HOW to generate the commit message or which files to focus on.

Examples of instructions you might receive:
- "focus on authentication changes only"
- "separate database and API changes into different commits"
- "include performance impact in commit message"
- "mention breaking changes in commit body"
- "commit only the controller changes"

When extra instructions are provided:
1. Parse and remove all flags first
2. Treat remaining text as commit generation instructions
3. Follow these instructions when staging files and creating commit messages
4. Still follow conventional commit format but enhance with requested details

### Example Usage
- `/git:commit --all` - Stage all files and commit
- `/git:commit --patch` - Force patch mode for selective staging
- `/git:commit --type fix focus on error handling` - Fix-type commit focusing on error handling
- `/git:commit separate frontend and backend changes` - Create separate commits for different areas

## Instructions

Use `execute_command` to run git commands directly. Parse `$ARGUMENTS` for flags and instructions. Review changes, stage appropriate files based on arguments, and create meaningful commits that help future developers understand the codebase evolution.
