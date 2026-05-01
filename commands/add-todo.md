---
description: Add a todo to project-root TODO.md (locally gitignored)
argument-hint: <todo text>
allowed-tools: Bash(git rev-parse:*), Bash(git ls-files:*), Bash(cat:*), Bash(grep:*), Bash(mkdir:*), Bash(touch:*), Bash(printf:*), Bash(test:*)
---

## Argument

```text
$ARGUMENTS
```

## Your task

Add `$ARGUMENTS` as a new open todo in the project-root `TODO.md`. Follow the steps in order; stop on any error.

### 1. Validate the argument

- If `$ARGUMENTS` is empty or whitespace-only, print `Usage: /add-todo <todo text> — text required` and stop.
- If `$ARGUMENTS` contains a newline, print `Todo text must be a single line.` and stop.

### 2. Locate the repo and paths

```bash
git rev-parse --show-toplevel
```

If this fails, print `Not in a git repository.` and stop. Call the output `$ROOT`.

```bash
git rev-parse --git-path info/exclude
```

Call the output `$EXCLUDE` (worktree/submodule-safe). The target file is `$ROOT/TODO.md`; call it `$TODO`.

### 3. Ensure TODO.md exists

If `$TODO` does not exist, create it:

```bash
printf '# TODO\n\n' > "$TODO"
```

Remember whether you just created it (`CREATED`).

### 4. Check whether TODO.md is tracked

```bash
git ls-files --error-unmatch "$TODO"
```

Exit 0 = tracked. Nonzero = not tracked. Remember the result (`TRACKED`).

### 5. If not tracked, ensure `TODO.md` is in `$EXCLUDE`

Skip entirely if `TRACKED`.

Make sure `$EXCLUDE` exists (create its parent directory with `mkdir -p` and the file with `touch` if missing). Then:

```bash
grep -Fxq "TODO.md" "$EXCLUDE"
```

If that exits nonzero (no exact-line match), append:

```bash
printf 'TODO.md\n' >> "$EXCLUDE"
```

Remember whether you added the entry (`EXCLUDE_ADDED`).

### 6. Read current TODO.md and dedup-check

Read `$TODO`. For each line matching `^- \[ \] (.+)$`, extract the captured text, lowercase it, and collapse internal whitespace runs to single spaces. Apply the same normalization to `$ARGUMENTS`. If any normalized existing item equals the normalized new text, print:

> Already on the list: `<existing line verbatim>`

and stop — do not modify the file.

Only match `- [ ]` (open) items. Ignore `- [x]` checked items, so re-adding the text of a completed todo creates a fresh open one.

### 7. Append

Using `Edit`, add `- [ ] $ARGUMENTS` as the last line of `$TODO`. The file must end in exactly one trailing newline. If the existing file does not end in a newline, include a leading `\n` in the new content so the new entry sits on its own line.

### 8. Report

One line:

> Added: `- [ ] <text>` → `<path>`

Then add suffix notes as applicable:
- `CREATED` → `(created TODO.md)`
- `EXCLUDE_ADDED` → `(added TODO.md to .git/info/exclude)`
- `TRACKED` → `(TODO.md is tracked by git; left .git/info/exclude alone)`
