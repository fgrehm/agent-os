## File Naming and Organization Conventions

### Overview

Consistent file naming and organization makes Claude Code projects discoverable, maintainable, and collaborative. This standard covers directory structure, file naming patterns, and organizational best practices.

### Directory Structure

**Standard Claude Code Layout:**

```
project/
├── .claude/
│   ├── commands/              # User-invoked slash commands
│   │   ├── simple-command.md
│   │   └── complex-command/
│   │       ├── command.md
│   │       ├── step-1.md
│   │       └── step-2.md
│   ├── agents/               # Specialized sub-agents
│   │   ├── code-reviewer.md
│   │   ├── test-writer.md
│   │   └── doc-generator.md
│   ├── skills/               # Model-invoked capabilities
│   │   ├── pdf-processor/
│   │   │   ├── SKILL.md
│   │   │   ├── reference.md
│   │   │   └── examples.md
│   │   └── api-client/
│   │       └── SKILL.md
│   ├── settings.json         # Claude Code configuration
│   └── CLAUDE.md            # Project-specific instructions
├── .git/
└── [project files]
```

**Guidelines:**
- `.claude/` at project root
- Separate directories for commands, agents, skills
- Simple items as single files, complex items as directories
- `settings.json` for permissions and preferences
- `CLAUDE.md` for project context

### File Naming Patterns

**Commands:**

```
.claude/commands/
├── format-code.md              # Simple command
├── add-test.md                 # Simple command
├── review-pr/                  # Complex multi-step
│   ├── command.md
│   ├── 01-fetch.md
│   ├── 02-analyze.md
│   └── 03-report.md
└── create-spec/
    ├── command.md
    └── helpers/
        └── validate.md
```

**Naming Rules:**
- kebab-case (lowercase with hyphens)
- Verb-noun pattern: `action-target`
- Descriptive but concise
- `.md` extension
- Match command invocation: `/format-code` → `format-code.md`

**Good Names:**
- `format-code.md` - Clear action and target
- `add-test.md` - Concise and descriptive
- `review-pr.md` - Matches user expectation
- `generate-docs.md` - Action-oriented

**Bad Names:**
- `formatter.md` - Not a clear action
- `tests.md` - Too vague
- `pr.md` - Not descriptive enough
- `Gen_Docs.md` - Wrong casing

**Agents:**

```
.claude/agents/
├── code-reviewer.md
├── test-writer.md
├── doc-generator.md
├── python-linter.md
└── spec-validator.md
```

**Naming Rules:**
- kebab-case
- Role-based naming: `role-specialty`
- Descriptive of agent's purpose
- Must match `name` field in frontmatter
- `.md` extension

**Good Names:**
- `code-reviewer.md` - Clear role
- `python-linter.md` - Specific specialty
- `test-writer.md` - Action-oriented role
- `api-client.md` - Clear responsibility

**Bad Names:**
- `helper.md` - Too generic
- `agent1.md` - Not descriptive
- `CodeReviewer.md` - Wrong casing
- `cr.md` - Too abbreviated

**Skills:**

```
.claude/skills/
├── pdf-processor/
│   ├── SKILL.md              # Required
│   ├── reference.md          # Optional detailed docs
│   ├── examples.md           # Optional examples
│   └── scripts/              # Optional supporting files
│       └── extract.py
├── api-client/
│   └── SKILL.md
└── data-analyzer/
    ├── SKILL.md
    └── reference.md
```

**Naming Rules:**
- Directory name: kebab-case
- Main file: Always `SKILL.md` (capitalized)
- Supporting files: lowercase kebab-case
- Match skill purpose
- Descriptive of capability

**Good Names:**
- `pdf-processor/` - Clear capability
- `api-client/` - Descriptive
- `data-analyzer/` - Specific purpose
- `typescript-checker/` - Clear scope

**Bad Names:**
- `processor/` - Too vague
- `helper/` - Not descriptive
- `skill-1/` - No semantic meaning
- `PDFProcessor/` - Wrong casing

### Frontmatter Conventions

**Command Frontmatter:**

```yaml
---
description: Brief description of what the command does
argument-hint: [optional-arg]
allowed-tools: Read, Write, Edit
---
```

**Conventions:**
- Field names: lowercase with hyphens
- Order: description, argument-hint, allowed-tools
- Values: concise and clear
- See `@agent-os/standards/global/frontmatter-conventions.md` for details

**Agent Frontmatter:**

```yaml
---
name: agent-name
description: What the agent does and its role
tools: Read, Write, Edit
color: blue
model: sonnet
---
```

**Conventions:**
- `name` matches filename (without .md)
- Field names: lowercase
- Order: name, description, tools, color, model
- Tool names: exact casing (Read, Write, Edit)

**Skill Frontmatter:**

```yaml
---
name: skill-name
description: What skill does and when Claude should use it
allowed-tools: Read, Write
---
```

**Conventions:**
- `name` matches directory name
- Must include "when to use" in description
- Order: name, description, allowed-tools

### Template Variables

**Naming Patterns:**

```markdown
$VARIABLE_NAME        # Template variables (all caps with underscores)
[placeholder]         # User-replaceable (lowercase in brackets)
[CONSTANT_VALUE]      # Constants (uppercase in brackets)
{{injection/path}}    # File injection (double braces)
```

**Examples:**

```markdown
Process the file at: $FILE_PATH
Component name: [component-name]
Environment: [PRODUCTION|DEVELOPMENT]
Include standards: {{standards/global/coding-style.md}}
```

**Guidelines:**
- Be descriptive
- Use consistent casing
- Document what each variable represents
- Don't mix patterns

### Content Organization

**Standard File Structure:**

```markdown
---
[YAML frontmatter]
---

# Title

[Brief overview paragraph]

## Overview

[Detailed description]

## Main Content Sections

[Organized by topic]

## Examples

[Concrete examples]

## Related Standards

- [Related file 1]
- [Related file 2]

## References

- [External link 1]
- [External link 2]
```

**Section Ordering:**
1. Frontmatter (always first)
2. Title (H1)
3. Brief overview
4. Main content (logical grouping)
5. Examples
6. Related standards
7. External references

**Multi-File Organization:**

```
complex-command/
├── command.md           # Entry point, overview, navigation
├── 01-prepare.md       # Numbered steps (sequential)
├── 02-analyze.md
├── 03-implement.md
├── 04-verify.md
└── helpers/            # Supporting prompts (optional)
    ├── validate.md
    └── format.md
```

**Guidelines:**
- Entry point: `command.md` (not `index.md`)
- Sequential steps: numbered prefixes (01-, 02-, etc.)
- Helpers: separate directory
- Each file should be self-contained but reference others

### Path References

**Relative Paths:**

```markdown
See: [Skill Design](../claude-code/skill-design.md)
Include: {{standards/global/coding-style.md}}
Script: ./scripts/helper.py
```

**Absolute Paths (in injection tags):**

```markdown
{{standards/global/conventions.md}}
{{workflows/implementation/implement-tasks.md}}
```

**Guidelines:**
- Use relative paths for markdown links
- Use absolute paths (from standards/) for injection tags
- Always use forward slashes (not backslashes)
- Verify paths exist

### Version Control

**Git Ignore Patterns:**

```gitignore
# .gitignore

# Don't commit
.claude/cache/
.claude/temp/
.claude/*.log

# Do commit
.claude/commands/
.claude/agents/
.claude/skills/
.claude/settings.json
.claude/CLAUDE.md
```

**Commit Organization:**

```
feat(commands): add code formatter command
fix(agents): correct test-writer tool restrictions
docs(skills): update pdf-processor examples
chore(settings): update permissions
```

**Guidelines:**
- Commit `.claude/` directory to version control
- Group related changes
- Use conventional commits
- Document rationale for complex changes

### Settings File Conventions

**settings.json Structure:**

```json
{
  "allowedTools": ["Read", "Write", "Edit", "Bash"],
  "restrictions": {
    "maxFileSize": 1000000,
    "allowedPaths": ["src/", "tests/"]
  },
  "preferences": {
    "defaultModel": "sonnet",
    "agentColor": "blue"
  }
}
```

**Guidelines:**
- Valid JSON format
- Lowercase field names (camelCase for multi-word)
- Tool names: exact casing
- Document non-obvious settings
- Keep minimal (defaults are often fine)

### Documentation Files

**CLAUDE.md Location:**

```
# Project-specific (most common)
project/.claude/CLAUDE.md

# Global (user-wide)
~/.claude/CLAUDE.md
```

**CLAUDE.md Structure:**

```markdown
# Project Name

[Brief project description]

## Overview

[What this project does]

## Key Conventions

[Project-specific patterns]

## Common Tasks

- Task 1: /command-name
- Task 2: /other-command

## Resources

[Links to docs, APIs, etc.]
```

**Guidelines:**
- Keep concise (Claude reads this often)
- Focus on project-specific context
- Link to detailed docs (don't duplicate)
- Update as project evolves

### Cross-References

**Internal Links:**

```markdown
Related standards:
- `@agent-os/standards/claude-code/skill-design.md` - Skill patterns
- `@agent-os/standards/global/coding-style.md` - Formatting rules

See also:
[Command Design](../claude-code/command-design.md)
[Agent Design](../claude-code/agent-design.md)
```

**Conventions:**
- Use `@agent-os/standards/` prefix for absolute references
- Use relative paths for markdown links
- Include brief description of what link provides
- Verify links work

**Injection References:**

```markdown
{{standards/global/conventions.md}}
{{workflows/implementation/implement-tasks.md}}
{{UNLESS condition}}content{{ENDUNLESS}}
```

**Guidelines:**
- Double braces for injection
- Path relative to profile root
- Document what gets injected
- Test that injection works

### Naming Anti-Patterns

**Avoid:**

```
❌ .claude/commands/cmd1.md          # Not descriptive
❌ .claude/agents/Helper.md          # Wrong case, too vague
❌ .claude/skills/skill_name/        # Underscores instead of hyphens
❌ .claude/commands/FormatCode.md    # PascalCase instead of kebab-case
❌ .claude/agents/a.md               # Too abbreviated
❌ .claude/skills/processor/         # Too generic
```

**Use:**

```
✓ .claude/commands/format-code.md
✓ .claude/agents/code-reviewer.md
✓ .claude/skills/pdf-processor/
✓ .claude/commands/add-test.md
✓ .claude/agents/python-linter.md
✓ .claude/skills/api-client/
```

### Directory Organization Best Practices

**Group by Type:**

```
.claude/
├── commands/         # All commands together
├── agents/          # All agents together
└── skills/          # All skills together
```

**Not by Feature:**

```
❌ .claude/
   ├── testing/
   │   ├── add-test.md        # Command
   │   └── test-writer.md     # Agent
   └── documentation/
       ├── generate-docs.md    # Command
       └── doc-writer.md       # Agent
```

**Rationale:**
- Claude Code expects specific directory structure
- Type-based organization is standard
- Easier to discover available commands/agents/skills
- Matches Claude Code UI organization

### Skill Directory Best Practices

**Progressive Disclosure:**

```
skill-name/
├── SKILL.md           # Core (< 500 lines)
├── reference.md       # Detailed docs
├── examples.md        # Comprehensive examples
└── scripts/           # Supporting code
    └── helper.py
```

**Not Everything in SKILL.md:**

```
❌ skill-name/
   └── SKILL.md        # 2,500 lines - too much
```

**Rationale:**
- SKILL.md competes with conversation context
- Link to detailed docs instead of including
- Claude can read reference.md if needed
- Keeps activation cost low

### Related Standards

- `@agent-os/standards/global/frontmatter-conventions.md` - YAML frontmatter
- `@agent-os/standards/global/coding-style.md` - Markdown formatting
- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/agent-design.md` - Agent structure
- `@agent-os/standards/claude-code/skill-design.md` - Skill organization

### References

- [Claude Code File Structure](https://docs.claude.com/en/docs/claude-code/file-structure)
- [Command Organization](https://docs.claude.com/en/docs/claude-code/commands#organization)
- [Skill Structure](https://docs.claude.com/en/docs/claude-code/skills#structure)
