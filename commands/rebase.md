---
description: Rebase current branch on main/master, fetching latest and resolving conflicts
allowed-tools: Bash(git branch:*), Bash(git fetch:*), Bash(git rev-parse:*), Bash(git rebase:*), Bash(git diff:*), Bash(git add:*), Task
---

# Rebase

You are a lightweight orchestrator. Your job is to run simple git commands and delegate
all heavy-context work (reading conflicting files, interpreting range-diff output) to
isolated subagents via the `Task` tool. This keeps the main context window clean.

**Critical rule:** Never read file contents or range-diff output directly. Always delegate.

## Context Protection

Do **NOT** use `run_in_background: true` when spawning subagents. Without it, the `Task` tool
returns only the subagent's final response — which is concise by design. With `run_in_background`,
you must use `TaskOutput` or read output files to get results, and these return the **full subagent
conversation transcript** (every Read result, Edit param, hook event), polluting the orchestrator
context. In a multi-commit rebase this can exceed 600K characters and trigger "Prompt is too long"
errors.

**Rules:**
- **Never** set `run_in_background: true` on `Task` calls
- **Never** use `TaskOutput` or read subagent output files
- The synchronous `Task` return value is all you need — it contains the agent's concise result

## Subagents

This command uses two specialized agents defined in `~/.claude/agents/`:

- **`conflict-resolver`** — reads a conflicting file, analyzes markers, resolves or reports back
- **`rebase-verifier`** — runs `git range-diff` and returns an interpreted summary

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

1. Record current HEAD for verification:

   ```bash
   git rev-parse HEAD
   ```

   Store this as `$OLD_HEAD` for post-rebase comparison.

1. Start rebase:

   ```bash
   git rebase origin/<detected-main-branch>
   ```

1. If rebase succeeds with no conflicts, proceed to verification (step 8).

1. If conflicts occur, resolve them using subagents:

   a. List conflicting files (this is small output, orchestrator handles it):

      ```bash
      git diff --name-only --diff-filter=U
      ```

   b. **Spawn a `conflict-resolver` agent for each conflicting file.**

      If there are multiple conflicting files, spawn them **in parallel** — since they
      only edit their own file and don't touch the git index, parallel execution is safe.

      Each agent's prompt should be just the absolute path to the conflicting file.
      The agent already knows what to do (its instructions are in `~/.claude/agents/conflict-resolver.md`).

   c. Process subagent results:
      - If the response starts with "Resolved:": note the file for staging
      - If the response starts with "AMBIGUOUS": use `AskUserQuestion` to present the
        summary to the user and ask how to resolve. Then spawn a new `conflict-resolver`
        agent with the file path AND the user's decision (e.g., "Resolve <path>: keep theirs
        for conflict 1, keep ours for conflict 2")

   d. **Stage all resolved files in one batch** (orchestrator does this):

      ```bash
      git add <file1> <file2> ...
      ```

   e. Continue rebase:

      ```bash
      git rebase --continue
      ```

   f. If more conflicts arise, repeat from step 7a.

1. Verify rebase integrity using the verification subagent:

   **Spawn a `rebase-verifier` agent** with the following prompt:
   ```
   Main branch: <main|master>
   OLD_HEAD: <old-head-sha>
   New HEAD: <current-head-sha>
   ```

   The agent will run `git range-diff` and return a summary.

1. Report completion with the verification summary and list of conflicts resolved.
