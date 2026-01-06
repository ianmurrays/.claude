---
description: Rebase current branch on main/master, fetching latest and resolving conflicts
allowed-tools:
  - Bash(git branch:*)
  - Bash(git fetch:*)
  - Bash(git rev-parse:*)
  - Bash(git rebase:*)
  - Bash(git diff:*)
  - Bash(git add:*)
---

# Rebase

## Steps

1. Get current branch and verify not on main:

   ```bash
   git branch --show-current
   ```

   If on `main` or `master`, abort with message.

1. Fetch latest from origin:

   ```bash
   git fetch origin
   ```

1. Detect main branch:

   ```bash
   git rev-parse --verify origin/main 2>/dev/null && echo "main" || echo "master"
   ```

1. Start rebase:

   ```bash
   git rebase origin/<detected-main-branch>
   ```

1. If rebase succeeds with no conflicts, report success and exit.

1. If conflicts occur, loop until resolved:

   a. List conflicting files:

      ```bash
      git diff --name-only --diff-filter=U
      ```

   b. For each conflicting file:
      - Read the file to see conflict markers
      - Analyze both versions (between `<<<<<<<` and `=======` is current
        branch, between `=======` and `>>>>>>>` is incoming from main)
      - If the resolution is clear (one side only has whitespace changes,
        or changes are in separate sections), resolve automatically
      - If ambiguous (both sides made substantive changes to same code),
        use `AskUserQuestion` to ask user which version to keep or how to
        merge
      - Edit the file to resolve conflicts (remove markers, apply chosen
        resolution)
      - Stage the resolved file:

        ```bash
        git add <file>
        ```

   c. Continue rebase:

      ```bash
      git rebase --continue
      ```

   d. Repeat if more conflicts arise.

1. Report completion with summary of any conflicts resolved.
