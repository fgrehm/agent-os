# Claude Code Tools Profile

A general-purpose Agent OS profile for projects focused on developing Claude Code extensions.

## Purpose

This profile is tailored for projects that build and refine AI-assisted development tools, not application features. Use this profile when your project's primary goal is creating:

- **Slash commands** - Custom commands for Claude Code (`.claude/commands/`)
- **Subagents** - Specialized AI agents for specific tasks
- **Prompt templates** - Reusable prompt patterns
- **Workflow orchestration** - Multi-agent coordination patterns

## Inherits From

`default` - Includes core Agent OS workflows and structure

## Key Characteristics

### Specialized Roles

**Implementers:**
- `command-developer` - Creates and maintains slash commands
- `subagent-developer` - Designs focused subagent definitions
- `prompt-engineer` - Crafts effective prompts and patterns
- `workflow-engineer` - Designs multi-agent orchestration

**Verifiers:**
- `integration-verifier` - Tests commands/subagents in Claude Code
- `documentation-verifier` - Ensures clear, complete documentation

### Custom Standards

- `claude-code/command-structure.md` - Slash command best practices
- `claude-code/subagent-design.md` - Subagent patterns and anti-patterns
- `claude-code/prompt-engineering.md` - Effective prompt writing
- `documentation/markdown-standards.md` - Documentation formatting

### Excluded from Parent

This profile excludes frontend-heavy standards since Claude Code tool development focuses primarily on markdown and prompt engineering:
- `standards/frontend/responsive.md`
- `standards/frontend/css.md`

## Example Projects

This profile is ideal for:

- **digest-ai** - Building slash commands for content processing workflows
- **Custom Claude Code extensions** - Project-specific command libraries
- **AI workflow tools** - Multi-agent orchestration patterns
- **Prompt template libraries** - Reusable AI assistant patterns

## Usage

### Install in a New Project

```bash
cd /path/to/your/project
~/agent-os/scripts/project-install.sh --profile claude-code-tools
```

### Create Product Documentation

After installation, plan your Claude Code tools project:

```bash
# In Claude Code, run:
/plan-product
```

This creates:
- `agent-os/product/mission.md` - Project mission and goals
- `agent-os/product/roadmap.md` - Feature roadmap
- `agent-os/product/tech-stack.md` - Technology decisions

### Develop Commands and Subagents

Use the specialized roles to build your tools:

1. **command-developer** - Build slash command files
2. **subagent-developer** - Create focused agent definitions
3. **prompt-engineer** - Refine prompts for clarity
4. **workflow-engineer** - Orchestrate multi-agent workflows
5. **integration-verifier** - Test everything works
6. **documentation-verifier** - Ensure docs are complete

## Development Workflow

### Creating a Slash Command

1. Create `.claude/commands/your-command.md`
2. Add frontmatter with description
3. Write clear instructions following `command-structure.md` standard
4. Reference standards using `{{standards/category/*}}` injection tags
5. Test the command in Claude Code
6. Verify with `integration-verifier`

### Creating a Subagent

1. Define role in `roles/implementers.yml` or `roles/verifiers.yml`
2. Run `scripts/project-update.sh` to compile agent definitions
3. Test agent using Task tool in Claude Code
4. Verify with `integration-verifier`

### Refining Prompts

1. Analyze current prompt effectiveness
2. Apply patterns from `prompt-engineering.md` standard
3. Add examples and clear boundaries
4. Test with real scenarios
5. Document patterns for reuse

## Standards Highlights

### Command Structure

Commands should:
- Include frontmatter with description
- Provide clear sequential instructions
- Use injection tags for standards
- End with clear next steps
- Show examples of expected output

### Subagent Design

Subagents should:
- Have focused, non-overlapping responsibilities
- State explicit boundaries (what NOT to do)
- Reference appropriate standards
- Use minimal required tools
- Include clear workflow steps

### Prompt Engineering

Prompts should:
- Be specific and actionable
- Provide necessary context
- Show examples (good and bad)
- Define clear boundaries
- Structure with headings and lists

## Contributing

When adding new patterns or standards:

1. Create focused standard files (one concern per file)
2. Include concrete examples
3. Show both good and bad patterns
4. Keep language clear and actionable
5. Test with real usage

## Directory Structure

```
claude-code-tools/
├── README.md                          # This file
├── profile-config.yml                 # Inheritance and exclusions
├── roles/
│   ├── implementers.yml              # Command/subagent developers
│   └── verifiers.yml                 # Integration/doc verifiers
├── standards/
│   ├── claude-code/
│   │   ├── command-structure.md
│   │   ├── subagent-design.md
│   │   └── prompt-engineering.md
│   └── documentation/
│       └── markdown-standards.md
├── workflows/                        # (Inherited from default)
└── agents/                           # (Inherited from default)
```

## Learn More

- [Agent OS Documentation](https://buildermethods.com/agent-os)
- [Claude Code Docs](https://docs.claude.com/en/docs/claude-code)
- [Slash Commands Guide](https://docs.claude.com/en/docs/claude-code/slash-commands)
- [Sub-agents Guide](https://docs.claude.com/en/docs/claude-code/sub-agents)
