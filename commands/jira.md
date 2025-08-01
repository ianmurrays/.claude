# Jira Issue Creation Best Practices (via Atlassian MCP)

This is a command for priming the context for creating Jira issues using the Atlassian MCP. It provides guidelines for issue hierarchy, relationships, and best practices to ensure effective issue management.

## Discovery Phase

1. Get Cloud ID: Use getAccessibleAtlassianResources to find the correct cloud ID
2. Find Project: Use getVisibleJiraProjects with search string to locate target project
3. Verify Issue Types: Use getJiraProjectIssueTypesMetadata to confirm available issue types and their IDs

## Issue Hierarchy & Relationships

Epic → Story → Sub-task Structure

- Epic: Top-level container (issue type ID varies by project)
- Story: Child of Epic (linked via customfield_10014 for epic link)
- Sub-task: Child of Story/Epic (uses parent parameter during creation)

## Key Findings

- Epic Linking: Stories link to Epics using customfield_10014: "EPIC-KEY"
- Parent-Child: Only Sub-tasks can have parent relationships via parent: "PARENT-KEY"
- Tasks Cannot Have Parents: Regular Tasks cannot be converted to Sub-tasks or have parent relationships after creation
- Hierarchy Limitations: Tasks exist independently and must reference related issues in descriptions

## Issue Creation Parameters

### Required Fields

{
  cloudId: "uuid-format-cloud-id",
  projectKey: "PROJECT-KEY",
  issueTypeName: "Epic|Story|Task|Sub-task",
  summary: "Issue title",
  description: "Issue description"
}

### Optional Fields

{
  parent: "PARENT-KEY", // Only for Sub-tasks
  additional_fields: {
    components: [{"name": "component-name"}],
    customfield_10014: "EPIC-KEY" // Epic link for Stories
  }
}

## Common Pitfalls

1. Cannot Edit Hierarchy: Cannot change issue type or add parent relationships after creation
2. Task Limitations: Tasks cannot be converted to Sub-tasks - must delete and recreate
3. Parent Validation: Parent issues must exist and be appropriate hierarchy level
4. Component Structure: Components must be specified as array of objects with "name" property

## Best Practices

1. Plan Hierarchy First: Design Epic → Story → Sub-task structure before creating issues
2. Create Sub-tasks Initially: Use Sub-task type from start if parent relationship needed
3. Use Descriptions for References: Include related issue keys in descriptions when formal linking isn't possible
4. Add Disclaimers: Include "AI-generated, investigate and verify" for AI-created issues
5. Batch Creation: Create issues in logical order (Epic → Stories → Sub-tasks)

## JQL for Management

### Find issues by type and key range
project = PROJ AND issueType = Task AND key >= PROJ-100 AND key <= PROJ-200

### Find specific issues for bulk operations
project = PROJ AND key IN (PROJ-1, PROJ-2, PROJ-3)
