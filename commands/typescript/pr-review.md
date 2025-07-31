---
description: Thoroughly reviews a TypeScript pull request using GitHub CLI to fetch PR details and launches a specialized TypeScript code reviewer subagent for comprehensive analysis.
---

# TypeScript PR Review Command

**IMPORTANT**: Use the `execute_command` tool directly for all GitHub CLI operations. Do NOT use any MCP servers or tools for git/gh commands.

You are tasked with conducting a thorough review of a TypeScript pull request by fetching PR information via GitHub CLI and launching a specialized TypeScript code reviewer subagent. The PR number is $ARGUMENTS.

## Process

1. **PR Information Gathering**
   - Use `gh pr view <pr_number_or_branch>` to get PR overview
   - Use `gh pr diff <pr_number_or_branch>` to get the full diff
   - Use `gh pr checks <pr_number_or_branch>` to see CI status
   - Use `gh pr view <pr_number_or_branch> --json files` to get changed files list

2. **Context Analysis**
   - Identify TypeScript files in the changeset (.ts, .tsx, .d.ts, etc.)
   - Check for test files (spec/, test/ directories, .spec.ts, .test.ts)
   - Look for configuration changes (tsconfig.json, package.json, webpack.config.js)
   - Review build and deployment configuration changes

3. **TypeScript Code Review Subagent Launch**
   - Create a new task in "code" mode with the typescript-code-reviewer agent
   - Provide comprehensive context including:
     - PR description and metadata
     - Full diff of TypeScript files
     - List of changed files
     - CI status and any failures
     - Related test files and configuration changes

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

## TypeScript-Specific Focus Areas

**Code Quality**:
- TypeScript style and conventions (ESLint, Prettier compliance)
- Type safety and proper typing patterns
- Proper use of TypeScript features and language constructs
- Error handling and exception management

**Architecture & Design**:
- SOLID principles adherence
- Separation of concerns and modularity
- Interface design and type definitions
- Dependency injection and inversion of control patterns

**Performance**:
- Bundle size impact and tree-shaking optimization
- Memory usage patterns and leak prevention
- Algorithm efficiency and complexity analysis
- Async/await patterns and Promise handling

**Security**:
- Input validation and sanitization
- Authentication/authorization patterns
- Environment configuration and secrets management
- Third-party dependency security assessment

**Testing**:
- Test coverage for new TypeScript code
- Unit test quality and maintainability
- Integration test coverage
- Type safety in test implementations

**Build & Configuration**:
- TypeScript configuration (tsconfig.json) changes
- Build optimization and compilation settings
- Package.json dependency updates
- Development vs production configuration differences

## Instructions

1. Use `execute_command` to run all GitHub CLI operations
2. Analyze the PR context thoroughly before launching the subagent
3. Focus on TypeScript-specific concerns and best practices
4. Provide the subagent with complete context for effective review
5. Ensure the review covers both functionality and code quality

## Example Subagent Launch

After gathering PR information, launch the typescript-code-reviewer with:

```
Please conduct a thorough TypeScript code review for PR #123: "Add user authentication service"

**PR Context:**
- Author: developer-name
- Files changed: 12 TypeScript files, 6 test files, 2 configuration files
- CI Status: Passing with warnings
- Description: [PR description here]

**Key Changes:**
- New authentication service with JWT implementation
- User interface updates with proper typing
- Database integration with TypeORM
- Configuration updates for security middleware

**Full Diff:**
[Complete diff of TypeScript files]

**Focus Areas:**
- Type safety of authentication implementation
- Security implications of JWT handling
- Performance impact of new database queries
- Test coverage completeness
- TypeScript best practices adherence
```

**Goal**: Provide comprehensive TypeScript code review that identifies issues, suggests improvements, and ensures code quality standards are met.
