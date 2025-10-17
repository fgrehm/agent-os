# Prompt Engineering for Claude Code

This standard defines best practices for crafting effective prompts for commands and subagents.

## Core Principles

1. **Be specific** - Vague prompts produce vague results
2. **Provide context** - Give the agent necessary background
3. **Define boundaries** - State what NOT to do
4. **Structure clearly** - Use headings, lists, examples
5. **Show, don't just tell** - Include examples

## Prompt Structure

### Standard Format

```markdown
# [Clear Heading]

[Brief context about the task]

## What to Do

[Specific instructions with numbered steps]

## What NOT to Do

[Explicit boundaries and exclusions]

## Examples

[Concrete examples of expected output]

## Standards to Follow

{{standards/category/*}}
```

## Context Setting

### Good Context

```markdown
You are analyzing a Rails application to identify database
schema changes needed for a new feature.

The application uses PostgreSQL with UUID primary keys
and has these existing models: User, Post, Comment.
```

Clear, specific, provides necessary background.

### Bad Context

```markdown
Help with the database.
```

Too vague, missing critical details.

## Instruction Clarity

### Good Instructions

```markdown
## Create Migration

1. Analyze the spec in agent-os/specs/[date]-[feature]/spec.md
2. Identify new tables, columns, and relationships
3. Create a timestamped migration file
4. Use proper Rails migration DSL
5. Include indexes for foreign keys
6. Add comments for complex changes
```

Clear, sequential, actionable.

### Bad Instructions

```markdown
Make a migration for the new stuff.
```

Vague, no guidance on how or what.

## Defining Boundaries

Always state what the agent should NOT do:

```markdown
## What NOT to Do

Do NOT:
- Modify existing migrations (create new ones instead)
- Change model validations (that's separate work)
- Create seed data (not part of this task)
- Touch the API layer (out of scope)
```

This prevents:
- Scope creep
- Unexpected changes
- Agent confusion

## Using Examples

### Show Expected Output

```markdown
## Output Format

Create files in this structure:

\`\`\`
db/migrate/
└── 20250117120000_add_tags_to_posts.rb
\`\`\`

With content like:

\`\`\`ruby
class AddTagsToPosts < ActiveRecord::Migration[7.0]
  def change
    add_column :posts, :tags, :text, array: true, default: []
    add_index :posts, :tags, using: :gin
  end
end
\`\`\`
```

### Show Bad Patterns to Avoid

```markdown
## Anti-patterns to Avoid

❌ DON'T write migrations like this:
\`\`\`ruby
# Missing index, no default, wrong type
add_column :posts, :tags, :string
\`\`\`

✅ DO write them like this:
\`\`\`ruby
# Has index, default, proper array type
add_column :posts, :tags, :text, array: true, default: []
add_index :posts, :tags, using: :gin
\`\`\`
```

## Prompt Patterns

### Research and Analysis

```markdown
# Analyze [Subject]

Research [specific aspect] by:

1. **Read existing code**
   - Check [specific files/patterns]
   - Look for [specific patterns]

2. **Identify patterns**
   - How does [X] currently work?
   - What conventions are used?

3. **Document findings**
   - Summarize current approach
   - Note any inconsistencies
   - Suggest improvements

## Output

Provide a markdown summary with:
- Current patterns observed
- Recommendations for new implementation
- Open questions that need user input
```

### Implementation

```markdown
# Implement [Feature]

Implement [specific feature] following this workflow:

## Phase 1: Preparation
1. Read the spec: agent-os/specs/[date]-[feature]/spec.md
2. Read the tasks: agent-os/specs/[date]-[feature]/tasks.md
3. Identify which tasks you're responsible for

## Phase 2: Implementation
For each task in your scope:
1. Update code following standards
2. Test your changes work
3. Check off the task in tasks.md

## Phase 3: Documentation
1. Document what you implemented
2. Note any deviations from spec
3. Flag issues for user review

## Standards

{{standards/global/*}}
{{standards/your-domain/*}}
```

### Verification

```markdown
# Verify [Implementation]

Verify the implementation meets requirements:

## Checklist

Go through each item:

- [ ] Code follows standards
- [ ] Tests exist and pass
- [ ] Documentation is complete
- [ ] No security issues
- [ ] Error handling is robust

## For Each Issue Found

1. Document the issue clearly
2. Explain why it's a problem
3. Suggest a fix
4. Mark severity (critical/important/minor)

## Output Format

Provide a verification report in:
agent-os/specs/[date]-[feature]/verification/[role]-verification.md
```

## Multi-Agent Prompts

When orchestrating multiple agents:

```markdown
# Orchestrate [Task]

Coordinate multiple agents to complete [task].

## Agent Assignment

Analyze tasks and assign to appropriate agents:

**Database work** → database-engineer
- Task 1: Create users table migration
- Task 2: Add user model relationships

**API work** → api-engineer
- Task 3: Create authentication endpoint
- Task 4: Add user registration logic

**UI work** → ui-designer
- Task 5: Create login form
- Task 6: Add validation display

## Execution Strategy

1. **Parallel phase**: Database and API work (no dependencies)
   - Launch database-engineer
   - Launch api-engineer
   - Wait for both to complete

2. **Sequential phase**: UI work (depends on API)
   - Wait for API completion
   - Launch ui-designer

3. **Verification phase**: After all implementation
   - Launch verifiers for each domain

## Handoffs

Each agent should:
- Update tasks.md with progress
- Document their changes
- Signal completion clearly
```

## Conditional Logic

Handle different scenarios:

```markdown
## Determine Approach

Based on the project structure:

**If using Rails:**
- Create ActiveRecord models
- Use Rails migrations
- Follow Rails conventions

**If using Node.js:**
- Use Prisma schema
- Generate Prisma migrations
- Follow Node.js patterns

**If unclear:**
- Ask user which framework is in use
- Proceed based on their answer
```

## Error Handling

Guide agents on handling issues:

```markdown
## If Tests Fail

When test failures occur:

1. **Analyze the failure**
   - Read error messages carefully
   - Identify root cause
   - Check if it's a real issue or flaky test

2. **Decide action**
   - **If real bug**: Fix it now
   - **If out of scope**: Document and flag for user
   - **If flaky**: Run again to confirm

3. **Document**
   - Note what failed and why
   - Explain what action you took
   - Flag if user input needed
```

## User Interaction

### When to Ask Questions

```markdown
## Gather Requirements

Ask the user:

1. **Required information** (block until answered):
   - "What authentication method? (OAuth, JWT, sessions)"
   - "Which database? (PostgreSQL, MySQL, SQLite)"

2. **Optional preferences** (provide defaults):
   - "Use UUIDs for IDs? (recommended: yes)"
   - "Include soft deletes? (default: no)"

Wait for user responses before proceeding.
```

### Providing Options

```markdown
## Propose Approaches

I found two possible approaches:

### Option A: Use Service Objects
**Pros:**
- Testable in isolation
- Clear separation of concerns

**Cons:**
- More files to maintain

### Option B: Keep in Controller
**Pros:**
- Simpler, fewer files

**Cons:**
- Harder to test
- Less reusable

Which approach would you prefer?
```

## Common Mistakes

### ❌ Too Vague

```markdown
Do the database stuff for the feature.
```

No guidance on what, how, or where.

### ✅ Specific

```markdown
Create a PostgreSQL migration to add a tags column
to the posts table. Use an array column with GIN index.
```

### ❌ No Boundaries

```markdown
Implement the user authentication feature.
```

Too broad, will try to do everything.

### ✅ Clear Scope

```markdown
Implement the user authentication API endpoint.
Do NOT create:
- The database schema (already done)
- The UI forms (separate task)
- Email verification (future feature)
```

### ❌ No Examples

```markdown
Write good tests.
```

What does "good" mean?

### ✅ With Examples

```markdown
Write tests that cover:

\`\`\`ruby
# Example test structure
describe UsersController do
  describe "POST /users" do
    it "creates user with valid params" do
      expect {
        post users_path, params: valid_params
      }.to change(User, :count).by(1)
    end

    it "returns 422 with invalid params" do
      post users_path, params: invalid_params
      expect(response).to have_http_status(:unprocessable_entity)
    end
  end
end
\`\`\`
```

## Testing Prompts

Test your prompts:

1. **Manual execution**:
   - Run the prompt yourself
   - Does it produce expected results?
   - Is anything unclear?

2. **Edge cases**:
   - Test with missing information
   - Test with ambiguous input
   - Verify error handling

3. **Iteration**:
   - Refine based on actual output
   - Add clarifications where agents got confused
   - Include examples of common mistakes

## Prompt Length

**Too short**: Missing critical context
```markdown
Create a user model.
```

**Too long**: Agent gets lost in details
```markdown
[3000 words of every possible detail...]
```

**Just right**: Focused with key details
```markdown
Create a User model for Rails with these fields:
- email (string, unique, indexed)
- name (string)
- role (enum: user, admin)

Include validations and associations to Posts.
Follow Rails conventions and standards in {{standards/backend/*}}
```

**Guideline**: 200-500 words for most prompts. Use injection tags to keep main prompt focused while providing access to detailed standards.
