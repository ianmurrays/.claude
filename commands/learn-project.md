---
description: Analyze project and generate/update CLAUDE.md with technical documentation
allowed-tools: Read, Glob, Grep, Bash(ls:*), Bash(find:*), Bash(git log:*), Bash(git diff:*), Bash(git show:*), Bash(git branch:*), Edit, Write, Task, AskUserQuestion
---

# Learn Project Command

Analyze this project and generate/update its CLAUDE.md with technical documentation while preserving any existing developer-specific instructions.

**REQUIRED: When calling the Task tool, always set the `model` parameter to `"opus"`.**

## Workflow Overview

Tell the user: "Analyzing project in 6 phases: technology detection, structure analysis, git history, deep feature analysis, CLAUDE.md generation, and verification."

## Phase 1-3: Parallel Analysis

Launch THREE subagents in parallel using the Task tool (all in a single message):

### Subagent 1: Technology Detection
```
Analyze this project's technology stack. Examine:
- package.json, Cargo.toml, go.mod, pyproject.toml, Gemfile, etc.
- Framework indicators (Next.js, Rails, Django, etc.)
- Database configurations
- Build tools and bundlers
- Testing frameworks
- CI/CD configurations

Output a concise summary: languages, frameworks, databases, key dependencies.
```

### Subagent 2: Structure Analysis
```
Analyze this project's directory structure and conventions:
- Top-level directory layout
- Source code organization patterns (MVC, feature-based, etc.)
- Configuration file locations
- Test file conventions
- Asset/resource organization

Output: directory tree overview, naming conventions, architectural patterns detected.
```

### Subagent 3: Git History Analysis
```
Analyze git history for project insights:
- git log --oneline -100 for recent activity
- git log --format='%s' | head -200 for commit message patterns
- Files changed most frequently (git log --pretty=format: --name-only | sort | uniq -c | sort -rn | head -20)
- Active branches

Output: commit conventions, active development areas, hot files list.
```

## Phase 4: Deep Feature Analysis

Based on Phase 1-3 outputs, identify 3-6 major domains/features in the project. Then launch ONE subagent PER domain in parallel:

```
For each domain, the subagent should:
- Trace the main code paths
- Identify key files and their responsibilities
- Document public APIs or interfaces
- Note dependencies between domains

Output: 1-2 paragraph summary of the domain, list of 3-5 key files.
```

## Phase 5: CLAUDE.md Generation/Merge

### Step 5.1: Check for existing CLAUDE.md
- Look for CLAUDE.md in project root
- If found, read it completely

### Step 5.2: Classify existing sections
For each section in existing CLAUDE.md, classify as PRESERVE or REGENERATE:

**PRESERVE if section contains:**
- Directive language: "MUST", "SHOULD", "DO NOT", "NEVER", "ALWAYS", "REQUIRED"
- Topics: coding style, conventions, workflow preferences, communication style, permissions, file restrictions, interaction guidelines

**REGENERATE if section covers:**
- Architecture descriptions
- Directory structure documentation
- Tech stack listings
- Key files documentation
- Feature descriptions
- Environment setup (factual, not prescriptive)

### Step 5.3: Generate new content
Compile findings from all phases into this structure:

```markdown
# [Project Name]

[1-2 sentence factual overview based on analysis]

## Tech Stack
[From Phase 1 - languages, frameworks, databases, key tools]

## Project Structure
[From Phase 2 - directory layout, patterns, conventions detected]

## Key Commands
[Detected from package.json scripts, Makefile, etc.]

---
## Developer Guidelines
[INSERT ALL PRESERVED SECTIONS HERE - maintain their original order and exact wording]
---

## Features & Domains
[From Phase 4 - one paragraph per domain identified]

## Active Development Areas
[From Phase 3 - recently modified areas, hot spots]

## Key Files
[Top 10-15 most important files from all phases, with one-line descriptions]
```

### Step 5.4: Compress if needed
If output exceeds ~300 lines:
- Reduce Key Files to top 10
- Shorten domain descriptions to 2-3 sentences each
- Remove redundant information
- Keep ALL preserved developer sections intact (never compress these)

### Step 5.5: User confirmation
Before writing, show the user:
1. Summary of what will be generated/updated
2. List of sections being preserved (if any)
3. List of sections being regenerated
4. Ask: "Write this CLAUDE.md? (The file will be created/updated at [path])"

Use AskUserQuestion with options:
- "Yes, write it"
- "Show me the full content first"
- "Cancel"

If user wants to see content first, display it, then ask again.

### Step 5.6: Write the file
Write CLAUDE.md to the project root directory.

## Phase 6: Verification

After writing, spawn a validation subagent to verify the CLAUDE.md contents against the actual codebase:

### Step 6.1: Validation subagent
```
Read the CLAUDE.md file that was just written. For each factual claim, verify it against the codebase:

- Tech Stack: Do the listed languages/frameworks match what's in package.json, Cargo.toml, etc.?
- Project Structure: Do the directories and patterns described actually exist?
- Key Commands: Do the listed commands exist in package.json scripts, Makefile, etc.?
- Features & Domains: Are the described features accurate? Do the key files exist?
- Key Files: Do all listed files exist? Are the descriptions accurate?

Output a list of issues found, categorized as:
- INCORRECT: Factually wrong information
- MISSING: Important information that should be included
- OUTDATED: References to files/features that no longer exist

If no issues found, output "VALIDATION PASSED"
```

### Step 6.2: Fix issues (if any)
If the validation subagent reports issues:

1. Summarize the issues to the user
2. Spawn a fix subagent:
```
Read the CLAUDE.md file and the validation issues below. Fix each issue:

[INSERT VALIDATION ISSUES HERE]

For each issue:
- INCORRECT: Correct the information based on what actually exists in the codebase
- MISSING: Add the missing information in the appropriate section
- OUTDATED: Remove or update the outdated references

Output the corrected CLAUDE.md content, maintaining the same structure and preserving all developer guidelines sections exactly as they were.
```

3. Show the user what changed and ask for confirmation before writing the fixes
4. If more than 3 issues were found, run validation again after fixes (max 2 iterations)

## Important Guidelines

- All Task tool calls MUST include `model: "opus"` parameter
- Launch parallel subagents in a SINGLE message with multiple Task tool calls
- Never invent or assume information - only document what you find
- Preserve developer instructions exactly as written
- Keep technical descriptions factual, no superlatives
- If no CLAUDE.md exists, skip the preserve/merge logic
