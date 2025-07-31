---
description: Intelligently manages RSpec test suites for Git changes with automated test generation, comprehensive coverage, and flexible execution commands (docker, bundle exec, etc.).
---

# RSpec Test Management Command

**IMPORTANT**: Use the `execute_command` tool directly for all git/RSpec operations. Do NOT use any MCP servers or tools for git/rspec commands.

Execute comprehensive RSpec workflow for Git changes with custom execution command: $ARGUMENTS

## Setup & Memory Integration

First, extract and store the RSpec execution command from arguments:
```bash
# Example arguments:
# "docker-compose exec app bundle exec rspec"
# "bundle exec rspec"
# "bin/rspec"
```

Use `knowledge-graph-memory` MCP server to:
- Store the RSpec execution command for future use
- Query previous test configurations and patterns
- Remember project-specific testing conventions

## 10-Phase Workflow

### Phase 1: Baseline Establishment
```bash
# Capture baseline metrics without heavy JSON output
$RSPEC_COMMAND --format progress --profile 5 > baseline_summary.txt 2>&1
COVERAGE=true $RSPEC_COMMAND --format progress > baseline_coverage.txt 2>&1
```

### Phase 2: Git Analysis
```bash
# Detect base branch (main or master)
BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@')
git diff origin/$BASE_BRANCH...HEAD --name-only | grep -E '\.(rb|rake)$|Gemfile'
git status --porcelain
```

### Phase 3: Test File Generation
Auto-generate RSpec files following Rails conventions:
- `app/models/user.rb` → `spec/models/user_spec.rb`
- `app/controllers/users_controller.rb` → `spec/controllers/users_controller_spec.rb`

### Phase 4: Comprehensive Coverage
Generate tests following [Better Specs](https://www.betterspecs.org/) best practices:
```ruby
# spec/models/user_spec.rb
require 'rails_helper'

RSpec.describe User, type: :model do
  subject(:user) { build(:user) }

  describe 'validations' do
    context 'when email is valid' do
      it { is_expected.to be_valid }
    end

    context 'when email is blank' do
      before { user.email = '' }
      it { is_expected.not_to be_valid }
    end
  end

  describe 'associations' do
    it { is_expected.to have_many(:posts) }
    it { is_expected.to belong_to(:organization) }
  end

  describe '.authenticate' do  # Class method
    let(:password) { 'secret123' }

    context 'when credentials are valid' do
      it 'returns the user' do
        expect(described_class.authenticate(user.email, password)).to eq(user)
      end
    end

    context 'when credentials are invalid' do
      it 'returns nil' do
        expect(described_class.authenticate(user.email, 'wrong')).to be_nil
      end
    end
  end

  describe '#admin?' do  # Instance method
    context 'when user is admin' do
      before { user.role = 'admin' }
      it { is_expected.to be_admin }
    end

    context 'when user is not admin' do
      before { user.role = 'user' }
      it { is_expected.not_to be_admin }
    end
  end
end
```

### Phase 5: Test Execution
```bash
# Execute tests with lightweight reporting
$RSPEC_COMMAND --format documentation --profile 10 > test_results.txt 2>&1
COVERAGE=true $RSPEC_COMMAND --format progress > coverage_results.txt 2>&1
```

### Phase 6: Iterative Refinement
```bash
$RSPEC_COMMAND --only-failures
$RSPEC_COMMAND --format documentation --backtrace
```

### Phase 7: SimpleCov Integration
Configure SimpleCov for 100% coverage requirement with justified exceptions.

### Phase 8: 100% Coverage Achievement
Validate coverage and document any justified exceptions.

### Phase 9: Summary Generation
Generate lightweight before/after analysis:
```bash
# Compare test counts and timing
echo "=== Baseline vs Final Comparison ==="
echo "Baseline:" && grep -E "(examples?|failures?|pending)" baseline_summary.txt
echo "Final:" && grep -E "(examples?|failures?|pending)" test_results.txt

# Coverage comparison
echo "Coverage:" && cat coverage/.last_run.json
```

### Phase 10: Flexible Execution Options
Support dry-run mode, specific file targeting, and custom execution environments.

## Key Operations

**Memory Management**:
- Use `knowledge-graph-memory` MCP server to store RSpec execution command
- Query previous configurations and testing patterns
- Remember project-specific conventions and settings

**Git Analysis**:
```bash
# Detect base branch and find changed Ruby files
BASE_BRANCH=$(git symbolic-ref refs/remotes/origin/HEAD | sed 's@^refs/remotes/origin/@@')
git diff origin/$BASE_BRANCH...HEAD --name-only | grep '\.rb$'

# Get file change details
git diff origin/$BASE_BRANCH...HEAD --name-status
```

**Test Generation**:
- Extract public methods from changed files
- Generate comprehensive test templates
- Follow Rails/Ruby naming conventions
- Include validations, associations, edge cases

**Coverage Analysis**:
```bash
# Run with coverage
COVERAGE=true $RSPEC_COMMAND

# Check coverage results from SimpleCov output
grep -E "(covered|missed)" coverage/index.html
cat coverage/.last_run.json
```

## Better Specs Guidelines

**Test Structure**:
- Use `describe '.method'` for class methods, `describe '#method'` for instance methods
- Use `context 'when condition'` for different scenarios
- Keep descriptions under 40 characters
- One expectation per test when possible

**Best Practices**:
- Use `expect().to` syntax, never `should`
- Use `subject` and `let`/`let!` for DRY tests
- Use FactoryBot factories if available
- Test valid, edge, and invalid cases
- Use readable matchers and shared examples
- Don't use "should" in test descriptions

## Ruby Testing Focus

**Code Quality**: RuboCop compliance, Ruby idioms, method complexity
**Architecture**: SOLID principles, service patterns, separation of concerns
**Performance**: N+1 queries, algorithm efficiency, database optimization
**Security**: SQL injection, mass assignment, authentication patterns
**Coverage**: Public methods, edge cases, error handling, integrations

## Instructions

1. **Extract RSpec command** from $ARGUMENTS and store in knowledge-graph-memory
2. **Query memory** for previous project configurations and patterns
3. **Execute 10-phase workflow** systematically with custom execution command
4. **Generate comprehensive tests** for all changed Ruby files
5. **Achieve 100% coverage** with properly justified exceptions
6. **Provide detailed reporting** with before/after metrics
7. **Support flexible execution** (docker, bundle exec, custom commands)

**Examples**:
- `/rspec docker-compose exec app bundle exec rspec`
- `/rspec bundle exec rspec --tag ~slow`
- `/rspec bin/rspec --parallel`

**Goal**: Achieve 100% test coverage for Git changes using flexible execution commands, with automated test generation and comprehensive reporting.
