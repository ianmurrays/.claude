---
description: Efficiently bring Claude up to speed with recent changes in the current branch by analyzing git history, diffs, documentation, and stored context.
---

# Prime Command

**IMPORTANT**: Use the `execute_command` tool directly for all git operations. Do NOT use any MCP servers or tools.

Get Claude efficiently up to speed with recent changes in the current branch by analyzing git history, diffs, documentation, and stored context.

$ARGUMENTS

## Process

1. **Git History Analysis**
   - Use `git log --oneline -10` to get recent commits
   - Use `git show --stat` for commit summaries
   - Use `git diff HEAD~5..HEAD` for recent changes overview

2. **Context Gathering**
   - Read `CLAUDE.md` for project-specific context
   - Search for any new/updated documentation files
   - Check knowledge-graph-memory for relevant stored information

3. **Efficient Analysis Strategy**
   - Focus on commits from last 5-10 changes
   - Prioritize files that appear frequently in recent commits
   - Read full diffs only for significant changes (>50 lines)
   - Use `search_files` to find related documentation updates

## Guidelines

**Token Efficiency**:
- Start with git log overview before diving into diffs
- Use `--stat` flag to see file change summaries first
- Only read full diffs for commits that seem architecturally significant
- Batch related file reads together

**Focus Areas**:
- New features or major changes
- Configuration updates
- Documentation changes
- Test additions/changes
- Breaking changes or refactors

**Memory Integration**:
- Query knowledge graph for project context
- Update memory with new insights from recent commits
- Connect new changes to existing project understanding

## Examples

```bash
# Get recent commit overview
git log --oneline -10

# Show file change stats for recent commits
git show --stat HEAD~3..HEAD

# Compare current state with 5 commits ago
git diff --stat HEAD~5..HEAD

# Find recently modified documentation
find . -name "*.md" -mtime -7 | head -10
```

## Instructions

1. Use `execute_command` for all git operations
2. Start with broad overview using `git log` and `--stat` flags
3. Use `search_files` to find documentation patterns
4. Query `memory` to connect changes to existing knowledge
5. Focus on understanding **what changed** and **why** rather than every detail
6. Summarize key insights for efficient context building

**Goal**: Build comprehensive understanding of recent changes while minimizing token usage through strategic information gathering.
