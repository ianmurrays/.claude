---
description: Thoroughly reviews a Ruby pull request using GitHub CLI to fetch PR details and launches a specialized Ruby code reviewer subagent for comprehensive analysis.
---

# Ruby PR Review Command

**IMPORTANT**: Use the `execute_command` tool directly for all GitHub CLI operations. Do NOT use any MCP servers or tools for git/gh commands.

You are tasked with conducting a thorough review of a Ruby pull request by fetching PR information via GitHub CLI and launching a specialized Ruby code reviewer subagent. The PR number is $ARGUMENTS.

## Process

1. **PR Information Gathering**
   - Use `gh pr view <pr_number_or_branch>` to get PR overview
   - Use `gh pr diff <pr_number_or_branch>` to get the full diff
   - Use `gh pr checks <pr_number_or_branch>` to see CI status
   - Use `gh pr view <pr_number_or_branch> --json files` to get changed files list

2. **Context Analysis**
   - Identify Ruby files in the changeset (.rb, .rake, Gemfile, etc.)
   - Check for test files (spec/, test/ directories)
   - Look for database migrations
   - Review configuration changes

3. **Ruby Code Review Subagent Launch**
   - Create a new task in "code" mode with the ruby-code-reviewer agent
   - Provide comprehensive context including:
     - PR description and metadata
     - Full diff of Ruby files
     - List of changed files
     - CI status and any failures
     - Related test files

## GitHub CLI Commands

```bash
# Get PR overview with metadata
gh pr view <pr_number> --json title,body,author,createdAt,mergeable,reviewDecision

# Get full diff
gh pr diff <pr_number>

# Check CI status
gh pr checks <pr_number>

# List changed files
gh pr view <pr_number> --json files --jq '.files[].path'

# Get PR reviews
gh pr view <pr_number> --json reviews
```

## Ruby-Specific Focus Areas

**Code Quality**:
- Ruby style and conventions (RuboCop compliance)
- Method complexity and readability
- Proper use of Ruby idioms
- Error handling patterns

**Architecture & Design**:
- SOLID principles adherence
- Separation of concerns
- Model, View, Controller organization (if Rails)
- Service object patterns

**Performance**:
- N+1 query detection
- Database indexing needs
- Memory usage patterns
- Algorithm efficiency

**Security**:
- SQL injection vulnerabilities
- Mass assignment protection
- Authentication/authorization
- Sensitive data handling

**Testing**:
- Test coverage for new code
- Test quality and maintainability
- Edge case coverage
- Integration test needs

## Instructions

1. Use `execute_command` to run all GitHub CLI operations
2. Analyze the PR context thoroughly before launching the subagent
3. Focus on Ruby-specific concerns and best practices
4. Provide the subagent with complete context for effective review
5. Ensure the review covers both functionality and code quality

## Example Subagent Launch

After gathering PR information, launch the ruby-code-reviewer with:

```
Please conduct a thorough Ruby code review for PR #123: "Add user authentication endpoint"

**PR Context:**
- Author: developer-name
- Files changed: 8 Ruby files, 4 test files
- CI Status: Passing
- Description: [PR description here]

**Key Changes:**
- New authentication controller
- User model updates
- New migration for auth tokens
- Security middleware additions

**Full Diff:**
[Complete diff of Ruby files]

**Focus Areas:**
- Security implications of auth changes
- Performance impact of new queries
- Test coverage completeness
- Rails best practices adherence
```

**Goal**: Provide comprehensive Ruby code review that identifies issues, suggests improvements, and ensures code quality standards are met.
