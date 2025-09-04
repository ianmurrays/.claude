---
allowed-tools: Bash(git show-ref:*), Bash(git checkout:*), Bash(git pull:*), Bash(git commit:*), Bash(git push:*), Bash(gh pr create:*), Bash(gh pr comment:*)
description: Create security docker bump PR with empty commit to trigger image rebuild
model: claude-3-7-sonnet-latest
argument-hint: --ticket UQ-1234 | --comment-apply-branch | --ticket UQ-1234 --comment-apply-branch
---

# Docker Bump Security Workflow

Creates an empty commit PR to trigger Docker image rebuild for security/maintenance purposes.

## Pre-run Commands

- Branch detection (main): !`git show-ref --verify --quiet refs/heads/main && echo "main exists" || echo "main not found"`
- Branch detection (master): !`git show-ref --verify --quiet refs/heads/master && echo "master exists" || echo "master not found"`
- Current date: !`date +%Y%m%d`

## Supported Flags

Parse the following flags from `$ARGUMENTS`:

- `--ticket <ticket-id>`: Use Jira ticket ID for branch naming (e.g., `--ticket UQ-1234`)
- `--comment-apply-branch`: Add "@apply branch" comment to created PR

### Flag Implementation

When processing arguments:
1. Check for `--ticket <ticket-id>` flag - extract ticket ID and use ticket-based naming
2. Check for `--comment-apply-branch` flag - add comment after PR creation
3. Use ticket-based naming if `--ticket` provided, otherwise use date-based naming

## Instructions

Create a security maintenance PR that triggers Docker image rebuild:

1. **Parse arguments** to extract flags and determine naming convention
2. **Checkout and pull the main branch** (main or master, whichever exists)
3. **Create new branch** with appropriate naming:
   - With `--ticket`: `security/{TICKET}-docker-bump`
   - Without `--ticket`: `security/docker-bump-YYYYMMDD`
4. **Create empty commit** with message `chore: trigger docker image rebuild`
5. **Push branch** to origin
6. **Create pull request** with appropriate title and description

### Implementation Steps:

1. Parse `$ARGUMENTS` to extract `--ticket` and `--comment-apply-branch` flags
2. Determine main branch from pre-run commands
3. Checkout and pull the main branch
4. Set branch name based on flags:
   - With `--ticket`: `security/$TICKET-docker-bump`
   - Without `--ticket`: `security/docker-bump-$(date +%Y%m%d)`
5. Create empty commit: `git commit --allow-empty -m "chore: trigger docker image rebuild"`
6. Push branch to origin
7. Create PR with appropriate formatting:
   - With `--ticket`: Title `[$TICKET] chore: trigger docker image rebuild`
   - Without `--ticket`: Title `chore: trigger docker image rebuild YYYY-MM-DD`
   - Body explaining this triggers Docker rebuild for security updates
   - Example: `gh pr create --title "[$TICKET] chore: trigger docker image rebuild" --body "Empty commit to trigger Docker image rebuild for security/maintenance purposes."`
8. If `--comment-apply-branch` flag was provided: Add comment `@apply branch` to the PR using `gh pr comment`

### Usage Examples:
- `/qp:docker-bump --ticket UQ-1234` - Creates `security/UQ-1234-docker-bump` branch and `[UQ-1234] chore: trigger docker image rebuild` PR
- `/qp:docker-bump` - Creates `security/docker-bump-20250904` branch and `chore: trigger docker image rebuild 2025-09-04` PR
- `/qp:docker-bump --ticket UQ-1234 --comment-apply-branch` - Creates ticket-based PR and adds "@apply branch" comment
- `/qp:docker-bump --comment-apply-branch` - Creates date-based PR and adds "@apply branch" comment

### Error Handling:
- Exit if neither main nor master branch exists
- Show clear error messages for any git failures
