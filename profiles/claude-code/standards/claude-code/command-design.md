## Claude Code Commands design standards

### Overview

Claude Code Commands are user-invoked workflows that extend Claude's functionality through slash command syntax. Commands provide structured, repeatable processes for common development tasks.

**Key Difference:**
- **Commands**: User explicitly invokes with `/command-name [args]`
- **Skills**: Claude automatically decides to use based on description and context

### File Structure

**Single-file commands:**
```
.claude/commands/
└── command-name.md      # Simple commands in one file
```

**Multi-file commands:**
```
.claude/commands/command-name/
├── command.md           # Main command entry point
├── step-1.md           # First step
├── step-2.md           # Second step
└── helpers/            # Optional helper prompts
    └── validate.md
```

### Command Frontmatter

**Required Structure:**
```yaml
---
description: Brief description of what the command does
argument-hint: [optional-arg]  # Optional
allowed-tools: Tool1, Tool2    # Optional
---
```

**Field Descriptions:**

**description** (Required):
- One-line summary shown in command palette
- Written as imperative: "Add a link" not "Adds links"
- Keep under 80 characters
- Focus on user benefit, not implementation

**argument-hint** (Optional):
- Shows expected argument format in palette
- Examples: `[URL]`, `<file-path>`, `[search-term]`
- Square brackets `[]` = optional
- Angle brackets `<>` = required
- Multiple args: `<source> <destination> [options]`

**allowed-tools** (Optional):
- Comma-separated list of tools command can use
- Restricts available tools for security/focus
- Common tools: Read, Write, Edit, Bash, Task, WebFetch, Glob, Grep
- Omit to allow all available tools

### Command Design Principles

**The Single Responsibility Principle:**
> Each command should do one thing well.

**Good Commands:**
- `/format-code` - Format code according to project standards
- `/add-test` - Add test file for a given source file
- `/review-pr` - Review a pull request for issues

**Bad Commands (too broad):**
- `/improve` - Vague, unclear what it does
- `/fix-everything` - Too many responsibilities
- `/work` - No clear scope

**Scope Definition:**

**Narrow Commands** (better UX):
- Clear purpose
- Predictable behavior
- Easy to document
- Easy to test

**Broad Commands** (harder to maintain):
- Unclear when to use
- Unpredictable results
- Hard to document all cases
- Difficult to test

### Argument Design

**When to Use Arguments:**

**Good use cases:**
- File paths: `/add-test src/utils.ts`
- URLs: `/fetch-docs https://example.com`
- Identifiers: `/review-pr 123`
- Simple options: `/deploy production`

**Bad use cases:**
- Complex objects (use interactive prompts instead)
- Multi-step configuration (use multi-file command)
- Optional flags with many variations (too complex)

**Argument Validation:**

Always validate arguments in command prompt:
```markdown
## Validate Input

{{#if $ARG1}}
Process the argument: $ARG1
{{else}}
ERROR: This command requires a file path argument.
Usage: /command-name <file-path>
{{/if}}
```

### Multi-File Command Structure

**When to Use Multi-File:**
- Command has distinct phases (plan → implement → verify)
- Steps can be reviewed independently
- User needs to approve intermediate results
- Complex workflows benefit from progression

**File Naming Conventions:**
```
command-name/
├── command.md           # Entry point, overview
├── 01-analyze.md        # Numbered for ordering
├── 02-plan.md
├── 03-implement.md
└── 04-verify.md
```

**Navigation Pattern:**

Each step should guide to the next:
```markdown
---
description: Analyze the codebase structure
---

# Step 1: Analysis

[Perform analysis...]

## Next Steps

Once you've completed this analysis, proceed to:
`/command-name/02-plan`
```

### Interactive vs Automated Commands

**Interactive Commands:**
- Ask questions during execution
- Use AskUserQuestion tool
- Get approval for significant changes
- Good for: planning, design decisions, destructive operations

**Automated Commands:**
- Execute without user input
- Good for: formatting, linting, repetitive tasks
- Must handle errors gracefully
- Should log what they're doing

**Example Interactive Pattern:**
```markdown
Use the AskUserQuestion tool to ask:
1. Which approach do you prefer?
   - Option A: Fast but approximate
   - Option B: Slow but precise

Based on user choice, proceed with selected approach.
```

### Error Handling

**Graceful Degradation:**

Commands should handle common errors:
```markdown
## Error Handling

If the file doesn't exist:
1. Inform the user clearly
2. Suggest valid alternatives
3. Do NOT proceed with invalid state

If dependencies are missing:
1. List what's needed
2. Provide installation instructions
3. Exit cleanly
```

**Clear Error Messages:**

**Good:**
```
ERROR: Could not find package.json in current directory.
This command requires a Node.js project.

Try: cd /path/to/project && /command-name
```

**Bad:**
```
Error: null reference
```

### Tool Selection

**Principle:** Use the minimum set of tools needed.

**Read-Only Commands:**
```yaml
allowed-tools: Read, Grep, Glob
```

**File Processing:**
```yaml
allowed-tools: Read, Write, Edit
```

**Development Workflow:**
```yaml
allowed-tools: Read, Write, Edit, Bash
```

**Web Integration:**
```yaml
allowed-tools: Read, Write, WebFetch
```

**Multi-Agent Orchestration:**
```yaml
allowed-tools: Task, Read, Write
```

### Documentation Within Commands

**Command.md Structure:**

```markdown
---
description: One-line description
argument-hint: [args]
allowed-tools: Read, Write
---

# Command Name

Brief explanation of what this command does and when to use it.

## Prerequisites

- What must be true before running this command
- Required files, tools, or setup

## Usage

/command-name [argument]

## What This Command Does

1. Step one
2. Step two
3. Step three

## Examples

/command-name src/utils.ts
Creates test file at src/utils.test.ts

## Error Handling

How the command handles common errors.
```

### Testing Commands

**Manual Testing Process:**

1. **Basic functionality**: Does it work in happy path?
2. **Argument handling**: Valid args work? Invalid args fail gracefully?
3. **Tool restrictions**: Can only use allowed tools?
4. **Error cases**: Handles missing files, network errors, etc.?
5. **Edge cases**: Empty files, special characters, large files?

**Test Checklist:**

```markdown
- [ ] Command appears in palette with correct description
- [ ] Argument hint shows correctly
- [ ] Command executes without errors (valid input)
- [ ] Command handles invalid input gracefully
- [ ] Tool restrictions work (cannot use forbidden tools)
- [ ] Error messages are clear and actionable
- [ ] Multi-file commands navigate correctly
- [ ] Documentation is accurate
```

### Performance Considerations

**Token Efficiency:**

Commands compete with conversation history for context.

**Keep commands concise:**
- Core instructions only
- Link to external docs for details
- Use progressive disclosure (don't front-load everything)

**Example Optimization:**

**Before (verbose):**
```markdown
This command will help you create a new test file. It analyzes your source
file, determines the appropriate test framework, creates a test file with
the correct naming convention, adds boilerplate test structure, and ensures
all imports are correct. It handles TypeScript, JavaScript, Python, and more.

[500 more lines of detailed examples...]
```

**After (concise):**
```markdown
Creates test file for given source file using project's test framework.

Usage: /add-test <source-file>

See: docs/testing.md for framework details
```

### Common Patterns

**Code Generation:**
```yaml
description: Generate boilerplate for a new component
argument-hint: <component-name>
allowed-tools: Read, Write, Glob
```

**Analysis:**
```yaml
description: Analyze codebase for common issues
allowed-tools: Read, Grep, Glob
```

**Refactoring:**
```yaml
description: Refactor code to use new pattern
argument-hint: <file-path>
allowed-tools: Read, Write, Edit
```

**External Integration:**
```yaml
description: Fetch and process external data
argument-hint: <url>
allowed-tools: Read, Write, WebFetch
```

**Multi-Agent Delegation:**
```yaml
description: Orchestrate complex multi-step workflow
allowed-tools: Task, Read, Write
```

### Version Control

**Track Command Changes:**
- Document rationale for command design decisions
- Notify team when changing existing commands
- Test thoroughly before committing
- Consider backward compatibility

**Breaking Changes:**
- Change argument structure
- Remove functionality
- Change tool restrictions
- Modify expected behavior

When making breaking changes:
1. Document in commit message
2. Update documentation
3. Consider deprecation period
4. Communicate with team

### Examples: Good vs Bad

**Good Command:**
```yaml
---
description: Add test file for a given source file
argument-hint: <source-file>
allowed-tools: Read, Write, Glob
---

# Add Test File

Creates a test file for the given source file using project conventions.

## Usage

/add-test src/components/Button.tsx

Creates: src/components/Button.test.tsx

## Process

1. Read source file to understand exports
2. Detect test framework from project
3. Create test file with boilerplate
4. Add imports for tested functions

## Prerequisites

- Source file must exist
- Project must have a test framework configured
```

**Bad Command:**
```yaml
---
description: Does testing stuff
---

# Test Helper

This command helps with testing. It can create tests, run tests,
debug tests, and more.

Just run it and it will figure out what you need.

[No clear structure, vague purpose, unclear usage]
```

### Related Standards

- `@agent-os/standards/claude-code/skill-design.md` - Skill creation patterns
- `@agent-os/standards/claude-code/agent-design.md` - Agent design principles
- `@agent-os/standards/global/frontmatter-conventions.md` - YAML frontmatter standards
- `@agent-os/standards/claude-code/prompt-engineering.md` - Effective prompt patterns

### References

- [Claude Code Commands Documentation](https://docs.claude.com/en/docs/claude-code/commands)
- [Command Best Practices](https://docs.claude.com/en/docs/claude-code/best-practices)
