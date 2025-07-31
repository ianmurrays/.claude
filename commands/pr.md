# Create GitHub Pull Request

Create a comprehensive GitHub pull request with detailed description based on git history.

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
1. Determine base branch (main or master) using `git symbolic-ref refs/remotes/origin/HEAD` or check which exists
2. Run `git log --oneline <base-branch>..HEAD` to see commits
3. Run `git diff <base-branch>...HEAD` to understand all changes
4. Analyze the code changes to understand the technical implementation
5. Create PR using `gh pr create` with comprehensive title and body, making `@ianmurrays` the assignee
6. Include specific testing instructions for reviewers
7. Mention any configuration changes, database migrations, or deployment notes

The pull request should give reviewers complete context to understand the change's purpose, implementation, and how to verify it works correctly.
