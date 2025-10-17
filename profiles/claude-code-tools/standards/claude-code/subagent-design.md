# Claude Code Subagent Design

This standard defines best practices for creating effective subagent definitions.

## What is a Subagent?

A subagent is a specialized AI agent with:
- **Focused role** - Handles specific type of work
- **Defined tools** - Access to only necessary tools
- **Clear boundaries** - Knows what it should NOT do
- **Appropriate model** - Matched to task complexity

## Subagent File Structure

Subagents are defined in `.claude/agents/`:

```
.claude/agents/
├── role-implementer.md        # Generated from roles/implementers.yml
├── role-verifier.md          # Generated from roles/verifiers.yml
└── custom-agent.md           # Hand-written specialized agent
```

## When to Create a Subagent

Create a subagent when you need:
- **Specialization** - Focused expertise (e.g., "database engineer", "prompt engineer")
- **Parallelization** - Multiple agents working simultaneously
- **Separation of concerns** - Different agents for implementation vs verification
- **Tool restrictions** - Limit what certain agents can access

Don't create subagents for:
- One-off tasks (use regular prompts instead)
- Tasks that need cross-domain knowledge
- Simple linear workflows (use slash commands)

## Subagent Definition Template

```markdown
---
role: [role-name]
description: [One-line description of expertise]
tools: [comma-separated tool list]
model: [inherit/sonnet/opus]
---

# [Role Name]

You are a [role name]. Your role is to [specific responsibility].

## Your Responsibilities

You are responsible for:
- [Specific task 1]
- [Specific task 2]
- [Specific task 3]

## Outside Your Scope

You should NOT:
- [Task outside scope 1]
- [Task outside scope 2]
- [Task outside scope 3]

## Standards to Follow

Follow these coding standards:

{{standards/category/*}}
{{standards/other-category/*}}

## Your Workflow

1. [Step in your process]
2. [Step in your process]
3. [Step in your process]

## Output Format

When complete, provide:
- [What you should output]
- [Format expectations]
```

## Role Clarity

### Good Role Definition

```markdown
You are a database engineer. Your role is to implement
database migrations, models, and queries.

## Your Responsibilities

You are responsible for:
- Creating database migrations
- Defining database models and relationships
- Writing efficient database queries
- Creating indexes for performance
```

### Bad Role Definition

```markdown
You help with the database and backend stuff.
```

Issues: Too vague, overlapping responsibilities, unclear boundaries.

## Boundaries and Scope

Always explicitly state what the agent should NOT do:

```markdown
## Outside Your Scope

You should NOT:
- Create API endpoints (that's the API engineer's job)
- Create UI components (that's the UI designer's job)
- Write test files (that's the testing engineer's job)
```

This prevents:
- Overlap between agents
- Scope creep
- Confusion about responsibilities

## Tool Selection

### Standard Tools

Most agents need: `Write, Read, Bash, WebFetch, Glob, Grep`

### Specialized Tools

Add specialized tools when needed:
- **Playwright** - For UI testing agents
- **Custom tools** - For domain-specific needs

### Tool Restrictions

Restrict tools for safety:
```markdown
---
tools: Read, Grep, Glob    # Read-only agent
---
```

## Model Selection

Choose appropriate model for task complexity:

- **`inherit`** - Use project's default (most common)
- **`sonnet`** - For straightforward implementation tasks
- **`opus`** - For complex reasoning (architecture, verification)

```markdown
---
model: opus    # Verifier needs strong reasoning
---
```

## Standards Integration

Reference relevant standards using injection tags:

```markdown
## Standards to Follow

Follow these coding standards:

{{standards/global/*}}
{{standards/backend/*}}
{{standards/testing/*}}
```

The agent will have access to all matching standards at runtime.

## Multi-Agent Coordination

When agents work together, define clear handoffs:

```markdown
## Coordination

After completing your work:
1. Update the tasks list with your changes
2. Document your implementation
3. Signal completion for the next agent

The verifier agent will review your work after you complete.
```

## Examples

### Good Subagent: Database Engineer

```markdown
---
role: database-engineer
description: Handles migrations, models, schemas, database queries
tools: Write, Read, Bash, WebFetch, Glob, Grep
model: inherit
---

# Database Engineer

You are a database engineer. Your role is to implement database
migrations, models, schemas, and database queries.

## Your Responsibilities

You are responsible for:
- Creating database migrations
- Defining models and relationships
- Writing efficient queries
- Creating indexes
- Setting up associations

## Outside Your Scope

You should NOT:
- Create API endpoints
- Create UI components
- Write backend business logic
- Write test files

## Standards to Follow

{{standards/global/*}}
{{standards/backend/*}}

## Your Workflow

1. Read the spec and identify database requirements
2. Create migrations for schema changes
3. Update or create model files
4. Add indexes for query performance
5. Test migrations run successfully
```

### Bad Subagent: Backend Developer

```markdown
---
role: backend-dev
description: Does backend stuff
tools: Write, Read, Bash
---

# Backend Developer

You work on the backend. Do whatever backend work is needed.
```

Issues:
- Too broad (what is "backend stuff"?)
- No clear boundaries
- No standards reference
- Vague instructions
- Will overlap with other agents

## Testing Subagents

Test subagent definitions:

1. **Manual test**:
   - Use Task tool to invoke the agent
   - Provide a realistic prompt
   - Verify output matches expectations

2. **Boundary test**:
   - Ask agent to do something outside its scope
   - Verify it declines or redirects appropriately

3. **Integration test**:
   - Run multiple agents in sequence
   - Verify handoffs work correctly
   - Check no duplicate work occurs

## Common Mistakes

❌ **Too Broad**
```markdown
You are a full-stack developer.
```
Too many responsibilities, will overlap with other agents.

✅ **Focused**
```markdown
You are an API engineer focused on REST endpoints.
```

❌ **No Boundaries**
```markdown
You handle database operations.
```
Will likely step on other agents' toes.

✅ **Clear Boundaries**
```markdown
You handle database migrations and models,
but NOT business logic or API endpoints.
```

❌ **Vague Instructions**
```markdown
Do good work.
```

✅ **Specific Workflow**
```markdown
1. Read the spec
2. Identify database requirements
3. Create migrations
4. Update models
5. Verify changes
```

## Agent Template Usage

Agent OS provides templates in `agents/templates/`:
- `implementer.md` - Template for implementation agents
- `verifier.md` - Template for verification agents

These templates get compiled with role data from `roles/*.yml` during profile installation.

## Role YAML Structure

Roles are defined in YAML and compiled into agent files:

```yaml
implementers:
  - id: database-engineer
    description: Handles migrations, models, schemas
    your_role: You are a database engineer...
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: orange
    areas_of_responsibility:
      - Create database migrations
      - Create database models
    example_areas_outside_of_responsibility:
      - Create API endpoints
      - Create UI components
    standards:
      - global/*
      - backend/*
    verified_by:
      - backend-verifier
```

The `project-install.sh` script compiles these into agent markdown files.
