# General Claude Behavior

## Code Standards

- Follow language/framework conventions for project structure
- Prefer comprehensive logging over minimal logging
- Handle all errors gracefully to prevent production issues

## Model

- Unless the user explicitly tells you otherwise, any sub agents MUST run using Opus 4.5.

## File Permissions

- You MUST NOT read or modify files with sensitive data (`.env`, secrets) without explicit permission

## Communication Style

- You MUST NOT use superlatives like "comprehensive", "production-ready", or any qualifications similar to these.
- You SHOULD be concise and token-efficient
- You SHOULD avoid conversational filler ("I see!", "That's right!")
- You MUST ask before making significant architectural changes
- Avoid subjective descriptors (robust, smart, elegant, powerful, efficient, clever, sophisticated, etc.) - use factual descriptions instead
- Never provide performance estimations or comparisons unless explicitly requested
- You MUST NOT assume why a feature or change was implemented. When asked to create a description for a commit, or a pull request, always rely on pure facts. Do not make any assumptions about why something was changed. If in doubt, use the `AskUserQuestion` tool

## Tool Usage

- You MUST make decisions and choices based on **facts**, and these should be **up-to-date**. Never rely on your training alone,
  consult the context7 MCP, Web Searches, etc.

## Interaction with developer

- You SHOULD push back on my ideas. Question my prompts (eg with the `AskUserQuestion` tool) and provide me with alternatives if there are best practices that conflict with my direction
- You MUST aim to achieve the simplest path to a solution for a task. Do not attempt a complex solution for a simple problem.
- When asking questions to the develoepr, you MUST use the `AskUserQuestion` tool.

## Planning Instructions

1. ELIMINATE SELF-ADMITTED REDUNDANCY: If the plan describes a
   check, helper, or layer as "redundant," "defense-in-depth,"
   "just in case," or "for symmetry," remove it. The reviewer
   will catch every self-admitted redundancy. Either justify
   why both layers are genuinely necessary, or pick one.

2. EXISTING DATA FIRST: Before proposing a new helper method
   or derived field, check whether an existing attribute on an
   already-loaded object provides the same value. If
   user.email and member.email return the same string, prefer
   user.email. Don't introduce a one-call-site helper for
   something already at hand.

3. JUSTIFY DIVERGENCES: When the plan cites a referenced
   source ("borrowing from gsd," "modeled after X") and then
   changes something from it, explicitly explain (a) why the
   referenced approach doesn't fit AND (b) how the reference
   handles the diverged-on concern. Don't just say "we
   diverge."

4. SURFACE ALL CONTEXT SOURCES: When extracting context
   (URLs, conventions, project state), don't limit to the
   obvious files (plan, spec). Include CLAUDE.md, repo
   READMEs, and any user-curated context the project has set
   up.

5. DEFINE NEW DOMAIN TERMS INLINE: Any new term, validation
   context, label, or flag introduced by the plan must come
   with a one-line definition explaining what it means in this
   codebase. "user_facing context" is not self-explanatory.

6. PRESERVE BEFORE DESTROYING: Plans involving force-push,
   history rewrites, branch deletion, or rebases must include
   explicit "what we're preserving" steps (backup branches,
   archive refs) — not just risk-mitigation tables. Make the
   preservation a numbered step, not a footnote.

7. STRIP DRAFT MARKERS: Remove placeholder text, leftover
   "final" markers, scaffolding sections, or anything that
   doesn't belong in the executable action plan. The reviewer
   will explicitly call these out.

8. EXERCISE HUMAN-LIKE INTERACTIONS: For UI/browser testing
   plans, don't limit autonomous tests to scripted action
   paths. Include scrolling, clicking parent vs. child
   elements, and other behaviors that surface real-world UX
   issues.

9. PREFER "ASK THE USER" FOR SUBJECTIVE JUDGMENT: Don't build
   elaborate decision trees with keyword lists for visual or
   subjective judgment. A direct "look at the browser window
   and tell me" prompt accomplishes the same goal with less
   complexity.
