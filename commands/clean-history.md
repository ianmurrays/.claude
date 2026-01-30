---
description: Rewrite branch history into clean, atomic commits based on logical changes
allowed-tools: Bash(git branch:*), Bash(git fetch:*), Bash(git rev-parse:*), Bash(git rebase:*), Bash(git diff:*), Bash(git add:*), Bash(git log:*), Bash(git reset:*), Bash(git commit:*), Bash(git merge-base:*), Read, Edit, AskUserQuestion
---

# Clean History

Rewrite branch history into clean, atomic commits while preserving all code changes.

## Workflow

### 1. Verify Not on Protected Branch

```bash
current_branch=$(git branch --show-current)
```

If `current_branch` is `main` or `master`, **abort immediately** with a warning:
> "Cannot rewrite history on protected branch. Please checkout a feature branch first."

### 2. Fetch Origin

```bash
git fetch origin
```

### 3. Detect Main Branch

Check which main branch exists:
```bash
git rev-parse --verify origin/main 2>/dev/null && echo "main" || echo "master"
```

Use whichever exists (`origin/main` or `origin/master`) for all subsequent operations. Store as `$main_branch`.

### 4. Create Backup Branch

Create a timestamped backup before any modifications:
```bash
backup_branch="backup/${current_branch}-$(date +%Y%m%d-%H%M%S)"
git branch "$backup_branch"
```

Inform the user: "Created backup branch: `$backup_branch`"

### 5. Check If Rebase Needed

```bash
merge_base=$(git merge-base origin/$main_branch HEAD)
origin_head=$(git rev-parse origin/$main_branch)
```

If `merge_base` != `origin_head`, the branch is behind or diverged from origin:
- Run `git rebase origin/$main_branch`
- Handle conflicts:
  - For clear cases (one side deleted, other modified → keep modification), auto-resolve
  - For ambiguous conflicts, use `AskUserQuestion` to present options
- After successful rebase, recalculate: `merge_base=$(git merge-base origin/$main_branch HEAD)`

### 6. Analyze the Branch

Gather information for planning commits:

```bash
# Get commit history
git log --oneline $merge_base..HEAD

# Get full diff
git diff $merge_base..HEAD

# Get recent repo commit style (for message formatting)
git log --oneline -20
```

**Analysis tasks:**
- Review original commit messages to understand intent
- Identify logical groupings:
  - Files that changed together for a single purpose
  - Refactoring vs feature vs bugfix changes
  - Test files paired with implementation
- Infer commit message style (conventional commits, imperative, etc.)
- Plan the new commit sequence (typically 2-5 commits for most branches)

### 7. Reset to Merge Base

```bash
git reset --mixed $merge_base
```

This unstages all changes while preserving the working directory.

### 8. Create Atomic Commits

For each logical change unit identified in step 6:

1. **Stage relevant changes:**
   ```bash
   git add <specific-files>
   ```
   For files with changes belonging to multiple commits, use hunk-level staging:
   ```bash
   git add -p <file>
   ```
   (Answer y/n for each hunk based on which commit it belongs to)

2. **Create commit:**
   ```bash
   git commit -m "<message matching inferred style>"
   ```

**Commit ordering principles:**
- Infrastructure/config changes first
- Refactoring before new features
- Implementation before tests (or together)
- Documentation/cleanup last

### 9. Verify Integrity

**Critical check** - ensure no code was lost or changed:

```bash
git diff $backup_branch
```

- If output is **empty**: Success! All changes preserved.
- If output is **not empty**:
  - **Abort** - do not proceed
  - Warn user: "Integrity check failed. Working tree differs from backup. Backup preserved at `$backup_branch`. Please investigate manually."
  - Show the diff to help diagnose

### 10. Report Completion

Summarize the result:
```bash
git log --oneline $merge_base..HEAD
```

Output:
- Number of original commits → number of new commits
- List of new commits with messages
- Reminder: "Backup branch `$backup_branch` preserved. Delete manually when confident: `git branch -D $backup_branch`"

## Error Handling

- **Rebase conflicts**: Pause and ask user for resolution strategy
- **Staging errors**: If `git add -p` needed but changes are too interleaved, ask user if approximate grouping is acceptable
- **Integrity failure**: Always preserve backup, never force completion

## Notes

- This command does **not** push changes. User must force-push manually if desired.
- Backup branches accumulate - user should clean up periodically.
- For very large branches (10+ commits), consider asking user to confirm the planned commit structure before executing.
