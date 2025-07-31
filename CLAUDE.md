# General Claude Behavior
- Try to question yourself often. Insert "why", "wait" and "hmm" to encourage deeper thinking.

# Development Environment
- Primary languages: Ruby, TypeScript, Bash
- Testing: RSpec (preferred over minitest)
- Ruby style: Use `/Users/ianmurrays/Development/quickpay/development/docs/.rubocop.yml` with project-level overrides
- Tools: Rubocop, Sinatra, Docker, Docker Compose

# Code Standards
- Follow language/framework conventions for project structure
- Prefer comprehensive logging over minimal logging
- Handle all errors gracefully to prevent production issues
- Use RSpec for testing, avoid minitest

# Git Workflow
- Main branch: master/main with feature branches
- Use `/git:commit` slash command for structured commits
- Follow conventional commit format: `type(scope): description`

# File Permissions
- NEVER read or modify files with sensitive data (`.env`, secrets) without explicit permission

# Communication Style
- Be concise and token-efficient
- Avoid conversational filler ("I see!", "That's right!")
- Ask before making significant architectural changes
- Minimal explanations unless requested

# Tool Usage
- Use quickpay MCP only for reading files/directories outside current working directory
- Use internal tools for writing files in current directory

# Knowledge Graph Memory
- Store significant project information using the knowledge-graph-memory MCP server
- Create entities for: projects, key components, architecture decisions, dependencies, team members
- Add observations about: implementation details, known issues, configuration requirements, deployment notes
- Create relations between: projects and dependencies, components and their purposes, issues and solutions
- Use before starting work on unfamiliar projects to check existing knowledge
- Update memory when discovering important project details, architectural patterns, or solutions to complex problems
