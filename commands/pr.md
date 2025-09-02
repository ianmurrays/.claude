# Create GitHub Pull Request

Create a comprehensive GitHub pull request with detailed description based on git history.

## Pre-run Commands

- Base branch detection: !`git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null || echo "none"`
- Repository status: !`git status`
- Existing PR check: !`gh pr view --json url,title 2>/dev/null || echo "none"`
- Current branch: !`git branch --show-current`
- Commit history since main: !`git log --oneline main..HEAD 2>/dev/null || echo "main branch not found"`
- Commit history since master: !`git log --oneline master..HEAD 2>/dev/null || echo "master branch not found"`
- Changes from main: !`git diff main...HEAD 2>/dev/null || echo "main branch not found"`
- Changes from master: !`git diff master...HEAD 2>/dev/null || echo "master branch not found"`

## Instructions

1. **Analyze the current branch's commits** since it diverged from the base branch
2. **Extract the what, why, and how** from the commit history and code changes
3. **Create a pull request** with a structured description that includes:

### PR Description Structure:
- **What**: Clear summary of changes made
- **Why**: Business justification or problem being solved
- **How**: Technical approach and implementation details
- **Testing**: What should be tested and how
- **Notes**: Any deployment considerations, breaking changes, or reviewer guidance

### Process:
1. Check if existing PR exists for current branch using pre-run `gh pr view` output
2. If `--update` flag is provided:
   - If PR exists: Update existing PR description using `gh pr edit --body`
   - If no PR exists: Show error message asking user to create PR first or remove --update flag
3. If no `--update` flag (normal PR creation):
   - Determine base branch from pre-run commands (check if main or master exists)
   - Use the appropriate commit history and diff from pre-run commands
   - Analyze commits and changes to understand what was implemented
   - Create PR using `gh pr create` with comprehensive title and body, making `ianmurrays` the assignee
   - If PR already exists, show warning but proceed (GitHub will handle the conflict)
4. Include specific testing instructions for reviewers  
5. Mention any configuration changes, database migrations, or deployment notes
The pull request should give reviewers complete context to understand the change's purpose, implementation, and how to verify it works correctly.

## Supported Flags

Parse the following flags from `$ARGUMENTS`:

- `--draft` or `-d`: Create as draft pull request
- `--no-assign`: Skip self-assignment
- `--base <branch>`: Override base branch detection
- `--title <title>`: Override auto-generated title
- `--update` or `-u`: Update existing PR description instead of creating new PR

### Flag Implementation

When processing arguments:
1. Check for `--draft` or `-d` flag - add `--draft` to `gh pr create` command
2. Check for `--no-assign` flag - skip the assignee parameter
3. Check for `--base <branch>` - use specified branch instead of auto-detection
4. Check for `--title <title>` - use provided title instead of generating from commits
5. Check for `--update` or `-u` flag - update existing PR instead of creating new one

### Extra Instructions for PR Generation

Any additional text in `$ARGUMENTS` (after flags are parsed) should be treated as instructions for HOW to generate the PR description. These instructions guide the content creation process.

Examples of instructions you might receive:
- "Include a mermaid diagram showing the request flow"
- "Add a table comparing before/after performance metrics"
- "Include code examples of how to use the new API"
- "Add a sequence diagram of the authentication process"
- "Include migration steps for existing users"
- "Add screenshots of the UI changes"

When extra instructions are provided:
1. Parse and remove all flags first
2. Treat remaining text as generation instructions
3. Follow these instructions when creating the PR description
4. Still include standard sections (What/Why/How/Testing) but enhance with requested content

### Example Usage
- `/pr --draft` - Creates draft PR
- `/pr --base develop --draft` - Creates draft PR against develop branch
- `/pr --title "Fix critical bug" --no-assign` - Custom title without self-assignment
- `/pr --update` - Updates existing PR description with latest changes
- `/pr -u --title "Updated: Fix critical bug"` - Updates existing PR with new title
- `/pr include a mermaid diagram of the data flow` - PR with data flow diagram
- `/pr --draft add performance comparison table and migration guide` - Draft PR with specific content
- `/pr --update show revised API usage examples` - Updates existing PR with new examples
