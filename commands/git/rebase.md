---
description: Rebase the current branch on top of the main branch (master or main) with interactive conflict resolution.
---

# Git Rebase Command

**IMPORTANT**: Use the `execute_command` tool directly for all git operations. Do NOT use any MCP servers or tools.

You are tasked with rebasing the current branch on top of the main branch (automatically detecting whether it's `master` or `main`) and automatically resolving conflicts when possible.

## Process

1. Detect the main branch name (master or main)
   1. Remote candidates: !`git branch -r | grep -E 'origin/(main|master)$' | head -1 | sed 's|.*origin/||'`
   2. Local candidates: !`git branch | grep -E '^[* ]*\b(main|master)\b' | head -1 | sed 's/^[* ]*//'`
2. Fetch the latest changes from origin
3. Start a rebase onto the main branch
4. Automatically resolve conflicts when possible
5. For unresolvable conflicts, guide user through manual resolution
6. Continue rebase until completion or abort if requested

## Main Branch Detection

Determine which is the main branch from the remote and local candidates.

## Rebase Strategy

1. **Fetch latest changes**: `git fetch origin`
2. **Start rebase**: `git rebase origin/<main-branch>`
3. **Handle conflicts automatically**:
   - When conflicts occur, attempt automatic resolution using merge tools
   - Use `git status` and `git diff --name-only --diff-filter=U` to identify conflicts
   - For simple conflicts, attempt resolution with `git checkout --ours/--theirs`
   - Read conflicted files and analyze conflict markers to resolve programmatically
   - Stage resolved files: `git add <resolved-files>`
   - Continue rebase: `git rebase --continue`
   - For complex conflicts that cannot be auto-resolved, ask user for guidance

## Automatic Conflict Resolution Strategy

When conflicts occur, attempt resolution in this order:

### 1. Simple File-Level Conflicts
- **Deleted vs Modified**: If one side deletes a file and the other modifies it, choose to keep the modified version
- **Added files**: If both sides add different files with same name, rename one with suffix

### 2. Content-Level Conflict Analysis
For each conflicted file:
1. **Read the conflict**: Parse conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
2. **Analyze changes**:
   - **Import/dependency conflicts**: Merge both sets of imports, remove duplicates
   - **Whitespace-only conflicts**: Choose the version with consistent formatting
   - **Comment conflicts**: Merge both comment additions
   - **Configuration merges**: Combine non-conflicting config changes
3. **Apply resolution**:
   - Remove conflict markers
   - Implement the merged solution
   - Verify syntax if possible (for code files)

### 3. Fallback to Manual Resolution
When automatic resolution fails:
1. **Show status**: Display `git status` to show remaining conflicted files  
2. **Show conflicts**: Display the actual conflict content with context
3. **Ask for guidance**: Present options (ours, theirs, manual edit, abort)
4. **Apply user choice**: Execute the selected resolution strategy

## Error Handling

- **No commits to rebase**: Inform user that branch is already up to date
- **Rebase already in progress**: Check if rebase is ongoing and offer to continue or abort
- **Dirty working directory**: Stash changes or ask user to commit first
- **Cannot abort/continue**: Provide clear error messages and suggested actions

## User Interaction

When conflicts occur, ask the user:
1. Whether they want to continue resolving conflicts or abort the rebase
2. Confirm when they've finished editing each conflicted file
3. Whether to continue after staging resolved files

## Commands Reference

- `git rebase --continue` - Continue after resolving conflicts
- `git rebase --abort` - Cancel the rebase and return to original state
- `git rebase --skip` - Skip the current commit (rarely used)
- `git status` - Show current rebase status
- `git add <file>` - Stage resolved files

## Examples

### Successful rebase:
```bash
git fetch origin
git rebase origin/main
# No conflicts - rebase completes successfully
```

### Rebase with automatic conflict resolution:
```bash
git fetch origin
git rebase origin/main
# Conflicts detected in src/app.js
# Automatically analyze and resolve conflicts
# If successful: git add src/app.js && git rebase --continue
git rebase --continue
# Process repeats until complete
```

## Supported Arguments

Parse additional instructions from `$ARGUMENTS`:

- `--abort`: Abort any rebase in progress and start fresh
- `--continue`: Continue an existing rebase (useful if command was interrupted)
- `--onto <branch>`: Rebase onto a specific branch instead of main
- `--interactive` or `-i`: Use interactive mode (not default behavior)
- `--autosquash`: Automatically squash commits marked with fixup!/squash!

### Example Usage
- `/git:rebase` - Standard rebase onto main branch
- `/git:rebase --abort` - Abort current rebase
- `/git:rebase --onto develop` - Rebase onto develop branch instead of main
- `/git:rebase --continue` - Continue interrupted rebase

## Instructions

Use `execute_command` to run git commands directly. Always fetch latest changes before rebasing. Attempt automatic conflict resolution first, falling back to user guidance only for complex conflicts that cannot be resolved programmatically. Provide clear status updates throughout the process.
