# Claude Code Slash Command Structure

This standard defines best practices for creating Claude Code slash commands.

## Command File Organization

Slash commands live in `.claude/commands/` and follow this structure:

```
.claude/commands/
├── simple-command.md          # Single-file command
└── complex-command/           # Multi-step command
    ├── 1-first-step.md
    ├── 2-second-step.md
    └── 3-third-step.md
```

## Frontmatter Requirements

Every command file MUST include frontmatter with a description:

```markdown
---
description: Brief description of what this command does (shown in Claude Code UI)
---

# Command Content
```

**Good descriptions:**
- `description: Create a new feature spec with requirements and tasks`
- `description: Bootstrap a new Agent OS profile for a tech stack`
- `description: Run tests and analyze failures`

**Bad descriptions:**
- Too vague: `description: Help with project`
- Too long: `description: This command will help you create...` (save details for body)
- Missing entirely

## Command Content Structure

### Clear Initial Instructions

Start with a clear statement of what the command will do:

```markdown
---
description: Plan product mission and roadmap
---

This begins a multi-step process for planning and documenting
the mission and roadmap for the current product.

The FIRST STEP is to confirm the product details...
```

### Sequential vs. Single-Step Commands

**Single-step command** - All instructions in one file:
```markdown
---
description: Analyze test coverage
---

Analyze the test coverage for this project:

1. Run the test suite
2. Generate coverage report
3. Identify gaps
4. Suggest improvements
```

**Multi-step command** - Separate files for each phase:
```markdown
# commands/plan-product/1-gather-info.md
---
description: Gather product information
---

Gather information about the product...

Once complete, run: `/plan-product/2-create-mission.md`
```

Use multi-step when:
- User needs to review/provide input between steps
- Each step has significant output to review
- Steps may be run independently

## Workflow Injection

Use injection tags to include reusable workflow blocks:

```markdown
## Gather Requirements

{{workflows/planning/gather-product-info}}

## Next Steps

Once you've gathered the info, proceed to...
```

**Injection tag format:**
- `{{workflows/path/to/file}}` - Expands full content of the workflow
- `{{standards/path/*}}` - Creates a reference list of standards

## Standards Reference

Reference relevant standards for context:

```markdown
## Standards to Follow

Use these standards as baseline assumptions:

{{standards/global/*}}
{{standards/backend/*}}
```

## Multi-Agent Orchestration

For commands that coordinate multiple subagents, place orchestration logic in `multi-agent/` subdirectory:

```
commands/implement-spec/
├── multi-agent/
│   └── implement-spec.md    # Orchestrates multiple implementer agents
└── single-agent/
    ├── 1-implement-spec.md
    └── 2-verify-implementation.md
```

Multi-agent orchestration file structure:

```markdown
---
description: Implement spec using specialized agents
---

# Implementation Orchestration

This command coordinates multiple specialized agents to implement a feature spec.

## Roles Assignment

Analyze the spec tasks and assign to appropriate implementers:

1. Database work → database-engineer
2. API endpoints → api-engineer
3. UI components → ui-designer
4. Tests → testing-engineer

## Parallel Execution

Launch implementers in parallel using Task tool...
```

## User Interaction

### Clear Output and Next Steps

Always end with clear next steps:

```markdown
## Display Confirmation

Once you've gathered all necessary information, output:

\`\`\`
I have all the info I need to proceed.

Next step: Run `/command-name/2-next-step.md`
\`\`\`
```

### Asking Questions

Use structured questions when user input is needed:

```markdown
Ask the user:

1. **What type of project is this?**
   - Provide examples: "Rails API", "NextJS app", "Python CLI"

2. **Should this inherit from an existing profile?**
   - List available options
   - Explain implications
```

## Error Handling

Include guidance for handling common issues:

```markdown
## If Tests Fail

If test failures occur:
1. Show the failures to the user
2. Ask if they want to fix them now or proceed
3. Document any known issues in the spec
```

## Examples

### Good Command Structure

```markdown
---
description: Create and verify a new feature spec
---

# Create Feature Spec

This command helps you research and document a new feature.

## Phase 1: Research

First, research the feature requirements:

{{workflows/specification/research-spec}}

## Phase 2: Write Spec

Based on research, write the spec:

{{workflows/specification/write-spec}}

## Phase 3: Verification

Verify the spec is complete:

{{workflows/specification/verify-spec}}

## Output

When complete, display:

\`\`\`
✓ Spec created: agent-os/specs/[date]-[feature]/spec.md

Next step: Run `/create-tasks` to break down into tasks
\`\`\`

## Standards

Follow these standards:

{{standards/global/*}}
```

### Bad Command Structure

```markdown
# do stuff

do the thing i asked about
```

Issues:
- No frontmatter description
- Vague instructions
- No structure or phases
- No output guidance
- No standards reference

## Testing Commands

Test commands manually:
1. Copy command file to `.claude/commands/` in a test project
2. Reload Claude Code
3. Execute the command
4. Verify it produces expected output
5. Check that injection tags resolve correctly

## Common Mistakes

❌ **Too Much in One Command**
- Split long commands into multiple steps

❌ **No Clear Exit**
- Always tell the user what to do next

❌ **Assuming Context**
- Commands should be self-contained with proper standards references

❌ **No User Feedback**
- Always output status and next steps

❌ **Hardcoded Paths**
- Use relative paths and injection tags for portability
