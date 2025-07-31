---
description: Thoroughly reviews an Angular pull request using GitHub CLI to fetch PR details and launches a specialized Angular code reviewer subagent for comprehensive analysis.
---

# Angular PR Review Command

**IMPORTANT**: Use the `execute_command` tool directly for all GitHub CLI operations. Do NOT use any MCP servers or tools for git/gh commands.

You are tasked with conducting a thorough review of an Angular pull request by fetching PR information via GitHub CLI and launching a specialized Angular code reviewer subagent. The PR number is $ARGUMENTS.

## Process

1. **PR Information Gathering**
   - Use `gh pr view <pr_number_or_branch>` to get PR overview
   - Use `gh pr diff <pr_number_or_branch>` to get the full diff
   - Use `gh pr checks <pr_number_or_branch>` to see CI status
   - Use `gh pr view <pr_number_or_branch> --json files` to get changed files list

2. **Context Analysis**
   - Identify Angular files in the changeset (.ts, .html, .scss/.css, .spec.ts)
   - Check for component, service, directive, and pipe files
   - Look for module configuration changes (app.module.ts, feature modules)
   - Review Angular-specific configuration (angular.json, environment files)
   - Check for routing configuration changes
   - Identify state management files (NgRx actions, reducers, effects, selectors)

3. **Angular Code Review Subagent Launch**
   - Create a new task in "code" mode with the angular-code-reviewer agent
   - Provide comprehensive context including:
     - PR description and metadata
     - Full diff of Angular files
     - List of changed files categorized by type
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

## Angular-Specific Focus Areas

**Component Architecture**:
- Component design patterns (smart/dumb components)
- Lifecycle hook usage and optimization
- Input/Output property design
- Change detection strategies
- Template and style organization

**Module Organization**:
- Feature module design and lazy loading
- Shared module patterns
- Core module singleton services
- Import/export strategies
- Module dependency management

**State Management**:
- NgRx patterns (actions, reducers, effects, selectors)
- Service-based state management
- Observable patterns and reactive programming
- Data flow and immutability principles

**Performance**:
- Bundle size impact and lazy loading optimization
- Change detection performance
- Memory leak prevention
- Image and asset optimization
- Virtual scrolling and performance patterns

**Security**:
- Angular security features utilization
- Authentication and authorization guards
- Input sanitization and XSS prevention
- HTTP interceptor security patterns
- Environment configuration security

**Testing**:
- Component unit testing with TestBed
- Service testing and mocking strategies
- Integration testing patterns
- E2E testing coverage
- Accessibility testing

**Build & Configuration**:
- Angular CLI configuration changes
- Build optimization settings
- Environment-specific configurations
- Progressive Web App (PWA) features
- Server-side rendering (SSR) considerations

## Instructions

1. Use `execute_command` to run all GitHub CLI operations
2. Analyze the PR context thoroughly before launching the subagent
3. Focus on Angular-specific patterns and best practices
4. Provide the subagent with complete context for effective review
5. Ensure the review covers architecture, performance, and user experience

## Example Subagent Launch

After gathering PR information, launch the angular-code-reviewer with:

```
Please conduct a thorough Angular code review for PR #123: "Add user dashboard with real-time notifications"

**PR Context:**
- Author: developer-name
- Files changed: 15 Angular files (8 components, 3 services, 2 modules, 2 guards)
- Test files: 12 spec files updated/added
- CI Status: Passing
- Description: [PR description here]

**Key Changes:**
- New dashboard feature module with lazy loading
- Real-time notification service with WebSocket integration
- User preference component with reactive forms
- Route guards for authentication
- NgRx state management for dashboard data
- Responsive design with Angular Material components

**Full Diff:**
[Complete diff of Angular files]

**File Categories:**
- Components: user-dashboard.component.ts, notification-panel.component.ts, user-preferences.component.ts
- Services: notification.service.ts, user-preferences.service.ts, websocket.service.ts
- Modules: dashboard.module.ts, shared.module.ts
- Guards: auth.guard.ts, dashboard.guard.ts
- State: dashboard.actions.ts, dashboard.reducer.ts, dashboard.effects.ts
- Templates: 8 HTML templates with Material Design components
- Styles: 5 SCSS files with responsive design patterns

**Focus Areas:**
- Component architecture and communication patterns
- Real-time WebSocket implementation security and performance
- NgRx state management best practices
- Responsive design and accessibility compliance
- Test coverage for new dashboard functionality
- Lazy loading and bundle optimization impact
- Authentication guard implementation
```

**Goal**: Provide comprehensive Angular code review that identifies architectural issues, suggests improvements, and ensures Angular best practices are followed.
