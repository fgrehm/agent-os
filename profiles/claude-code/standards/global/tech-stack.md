## Tech Stack for Claude Code Development

### Overview

Claude Code development uses Markdown, YAML, and various Claude Code APIs and tools. Understanding the tech stack enables effective command, agent, and skill creation.

### Core Technologies

**Markdown (CommonMark):**
- Primary format for commands, agents, skills
- GitHub Flavored Markdown (GFM) extensions supported
- Used for all documentation and prompts
- Specification: [CommonMark](https://commonmark.org/)

**YAML (YAML Ain't Markup Language):**
- Frontmatter metadata for commands, agents, skills
- Configuration in settings.json (JSON, not YAML)
- Human-readable data serialization
- Specification: [YAML 1.2](https://yaml.org/)

**Claude Code Tools API:**
- Read: File reading
- Write: File creation
- Edit: File modification
- Bash: Shell command execution
- Task: Subagent invocation
- WebFetch: Web content retrieval
- Glob: Pattern-based file finding
- Grep: Content search
- And more...

### File Formats

**Markdown (.md):**
```markdown
---
name: example
description: Example file
---

# Content

Markdown content here.
```

**Purpose:**
- Commands: `.claude/commands/*.md`
- Agents: `.claude/agents/*.md`
- Skills: `.claude/skills/*/SKILL.md`
- Documentation: `CLAUDE.md`, README files

**JSON (.json):**
```json
{
  "allowedTools": ["Read", "Write"],
  "preferences": {
    "defaultModel": "sonnet"
  }
}
```

**Purpose:**
- Settings: `.claude/settings.json`
- Configuration files
- Data interchange

### Claude Code Tools

**File Operations:**

**Read:**
```markdown
Use the Read tool to examine the file at src/utils.ts
```
- Read file contents
- Supports line ranges
- Can read images, PDFs
- Returns formatted output

**Write:**
```markdown
Use the Write tool to create a new file at src/new.ts
```
- Create new files
- Overwrites existing files
- Creates parent directories
- Returns success/error

**Edit:**
```markdown
Use the Edit tool to modify src/existing.ts
```
- Targeted file modifications
- Find and replace
- Preserves formatting
- Returns changes made

**File Discovery:**

**Glob:**
```markdown
Use Glob to find all TypeScript files: **/*.ts
```
- Pattern-based file finding
- Fast and efficient
- Returns file paths
- Supports complex patterns

**Grep:**
```markdown
Use Grep to search for "function" in codebase
```
- Content-based search
- Regex support
- Returns matching lines
- Context options (-A, -B, -C)

**Execution:**

**Bash:**
```markdown
Use Bash to run: npm test
```
- Execute shell commands
- Access to system tools
- Git operations
- Build and test commands
- Requires permission in settings

**Task:**
```markdown
Use Task to invoke code-reviewer agent
```
- Invoke specialized subagents
- Async execution
- Returns agent results
- Enables multi-agent workflows

**External:**

**WebFetch:**
```markdown
Use WebFetch to retrieve: https://api.example.com/data
```
- Fetch web content
- API calls
- HTML to markdown conversion
- Returns processed content

### Tool Restrictions

**Command Tool Restrictions:**

```yaml
---
description: Read-only analysis command
allowed-tools: Read, Grep, Glob
---
```

**Purpose:**
- Limit command capabilities
- Security boundaries
- Clear scope
- Prevent unintended changes

**Agent Tool Restrictions:**

```yaml
---
name: analyzer
description: Analyzes code without modifications
tools: Read, Grep
---
```

**Purpose:**
- Define agent capabilities
- Minimum necessary tools
- Clear responsibilities
- Composability

**Skill Tool Restrictions:**

```yaml
---
name: pdf-reader
description: Reads and extracts text from PDFs
allowed-tools: Read
---
```

**Purpose:**
- Read-only skills
- Processing skills (Read, Write)
- Automation skills (Read, Write, Bash)
- Security boundaries

### Model Selection

**Available Models:**

**Haiku:**
- Fast and economical
- Simple, repetitive tasks
- Pattern-based work
- Good for: formatting, boilerplate generation

**Sonnet:**
- Balanced capability and cost
- Most general-purpose work
- Code review, test generation
- Good for: most agent tasks

**Opus:**
- Most capable model
- Complex reasoning
- Critical accuracy needs
- Good for: architectural decisions, security reviews

**Inherit:**
- Use parent's model (default)
- User controls cost
- Maintains consistency
- Recommended for agents

**Model Configuration:**

```yaml
# Agent with specific model
---
name: simple-formatter
model: haiku
---

# Agent using parent's model
---
name: code-reviewer
model: inherit  # or omit field
---
```

### Claude Code File Structure

**Standard Layout:**

```
.claude/
├── commands/              # Slash commands
│   ├── *.md              # Simple commands
│   └── */                # Complex commands
│       ├── command.md
│       └── *.md
├── agents/               # Subagents
│   └── *.md
├── skills/               # Skills
│   └── */
│       ├── SKILL.md
│       └── [supporting files]
├── settings.json         # Configuration
└── CLAUDE.md            # Project context
```

**Purpose:**
- Commands: User-invoked workflows
- Agents: Specialized sub-agents
- Skills: Model-invoked capabilities
- Settings: Permissions and preferences
- CLAUDE.md: Project-specific instructions

### Injection System

**Workflow Injection:**

```markdown
{{workflows/implementation/implement-tasks.md}}
```

**Expands to:**
- Full content of workflow file
- Processed during profile installation
- Enables prompt reuse
- DRY principle

**Standards Injection:**

```markdown
{{standards/global/coding-style.md}}
```

**Expands to:**
- Reference to standard
- Claude can read when needed
- Reduces token usage
- Context-efficient

**Conditional Injection:**

```markdown
{{UNLESS standards_as_claude_code_skills}}
Include standards content
{{ENDUNLESS}}
```

**Purpose:**
- Conditional content inclusion
- Profile-specific variations
- Feature flags
- Configuration-driven behavior

### Development Tools

**Linting:**

```bash
# Markdown linting
markdownlint .claude/**/*.md

# YAML linting
yamllint .claude/**/*.md
```

**Testing:**

```bash
# Manual testing
/command-name test-arg

# Agent testing
Task(subagent_type="agent-name", prompt="test task")
```

**Validation:**

```bash
# Check frontmatter
grep -A 5 "^---$" .claude/commands/*.md

# Verify file structure
tree .claude/
```

### Version Control

**Git Configuration:**

```gitignore
# .gitignore

# Commit these
.claude/commands/
.claude/agents/
.claude/skills/
.claude/settings.json
.claude/CLAUDE.md

# Ignore these
.claude/cache/
.claude/temp/
.claude/*.log
```

**Best Practices:**
- Commit all commands, agents, skills
- Commit settings.json (if not sensitive)
- Commit CLAUDE.md
- Ignore cache and temporary files
- Use conventional commits

### External Dependencies

**Optional but Common:**

**Bash/Shell:**
- Git operations
- Build processes
- Test runners
- System commands

**Node.js/npm:**
- If using JavaScript/TypeScript tools
- Package management
- Build scripts
- Testing frameworks

**Python:**
- If using Python tools
- Data processing
- Script execution
- Utility scripts

**Docker:**
- Isolated environments
- Testing without host modification
- Reproducible builds
- CI/CD integration

### Settings Configuration

**settings.json Schema:**

```json
{
  "allowedTools": [
    "Read",
    "Write",
    "Edit",
    "Bash",
    "Task",
    "WebFetch",
    "Glob",
    "Grep"
  ],
  "restrictions": {
    "maxFileSize": 1000000,
    "allowedPaths": ["src/", "tests/"],
    "blockedPaths": ["node_modules/", ".git/"]
  },
  "preferences": {
    "defaultModel": "sonnet",
    "agentColor": "blue"
  }
}
```

**Common Settings:**
- `allowedTools`: Which tools are available
- `restrictions`: File size, path limits
- `preferences`: Defaults for agents, commands

### Tool Ecosystem

**Core Tools (Always Available):**
- Read, Write, Edit
- Glob, Grep
- TodoWrite
- AskUserQuestion

**Optional Tools (Permission Required):**
- Bash (shell execution)
- WebFetch (network access)
- Task (subagent spawning)

**MCP Tools (If Configured):**
- MCP server tools
- Prefix: `mcp__server__tool`
- Configured separately
- Example: `mcp__github__create_issue`

### Performance Considerations

**Token Usage:**
- Markdown content competes with conversation
- Keep prompts concise
- Use progressive disclosure
- Link to detailed docs

**Tool Efficiency:**
- Use Glob before Read (find files first)
- Use Grep before Read (search content first)
- Batch operations when possible
- Minimize Bash calls

**Model Costs:**
- Haiku: Lowest cost, fastest
- Sonnet: Balanced
- Opus: Highest cost, most capable
- Use appropriate model for task

### Related Standards

- `@agent-os/standards/global/conventions.md` - File organization
- `@agent-os/standards/global/coding-style.md` - Markdown formatting
- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/agent-design.md` - Agent design
- `@agent-os/standards/claude-code/skill-design.md` - Skill creation

### References

- [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)
- [Claude Code Tools](https://docs.claude.com/en/docs/claude-code/tools)
- [CommonMark Specification](https://commonmark.org/)
- [YAML 1.2 Specification](https://yaml.org/)
