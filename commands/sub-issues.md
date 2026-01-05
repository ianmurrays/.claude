---
description: Analyze current session and create GitHub sub-issues for a parent issue
---

## Arguments

```text
$ARGUMENTS
```

Parse as: `<parent_issue_number>` (required)

## Steps

1. Get repo info:
```bash
gh repo view --json owner,name -q '"\(.owner.login)/\(.name)"'
```

2. Fetch the parent issue to understand context:
```bash
gh issue view <parent_issue_number>
```

3. Check existing sub-issues to avoid duplicates:
```bash
gh api /repos/{owner}/{repo}/issues/{parent_issue_number}/sub_issues --jq '.[].title'
```

4. Analyze the current conversation to identify discrete tasks that should become sub-issues. Look for:
   - Tasks discussed or planned
   - Todo items mentioned
   - Implementation steps identified
   - Bugs or fixes discovered

   Skip any that duplicate existing sub-issues.

5. For each new task, create an issue and link it:
```bash
# Create issue
NEW_ISSUE=$(gh issue create --title "<task title>" --body "<description>" | grep -oE '[0-9]+$')

# Get internal ID and link as sub-issue
CHILD_ID=$(gh api /repos/{owner}/{repo}/issues/$NEW_ISSUE --jq '.id')
gh api /repos/{owner}/{repo}/issues/{parent_number}/sub_issues -X POST -F sub_issue_id=$CHILD_ID
```

6. Report: list existing sub-issues found and new ones created.
