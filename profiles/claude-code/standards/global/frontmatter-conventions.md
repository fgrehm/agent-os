## YAML frontmatter conventions for Claude Code

### Overview

Claude Code uses YAML frontmatter to define metadata for commands, agents, and skills. Consistent frontmatter structure improves discoverability, maintainability, and team collaboration.

### General Principles

**All frontmatter blocks:**
- Start and end with `---` delimiters
- Use lowercase for field names (snake_case for multi-word)
- Keep values concise and descriptive
- Order fields consistently (see templates below)

**Required vs Optional:**
- Include all required fields
- Omit optional fields unless needed (cleaner, less noise)
- Document rationale for non-standard fields

### Commands Frontmatter

**Location**: `.claude/commands/command-name.md`

**Template:**
```yaml
---
description: Brief description of what the command does
argument-hint: [optional argument hint]  # Optional
allowed-tools: Tool1, Tool2              # Optional
---
```

**Fields:**

**description** (Required):
- One-line summary of command purpose
- Written as imperative: "Add a link" not "Adds links"
- Keep under 80 characters
- Appears in command palette

**argument-hint** (Optional):
- Shows expected argument format in command palette
- Examples: `[URL]`, `[file-path]`, `[search-term]`
- Use square brackets for optional, angle brackets for required: `<required> [optional]`

**allowed-tools** (Optional):
- Comma-separated list of tools command can use
- Restricts available tools for security
- Common tools: Read, Write, Edit, Bash, Task, WebFetch
- Omit to allow all available tools

**Examples:**

```yaml
---
description: Add a web link with automatic extraction and summarization
argument-hint: [URL]
allowed-tools: Task, Read, Write, Edit
---
```

```yaml
---
description: Create a new spec for a feature
---
```

### Agents Frontmatter

**Location**: `.claude/agents/agent-name.md`

**Template:**
```yaml
---
name: agent-name
description: What the agent does and its role
tools: Tool1, Tool2, Tool3
color: blue                              # Optional
model: haiku                             # Optional
---
```

**Fields:**

**name** (Required):
- kebab-case naming: `link-processor`, `spec-writer`
- Descriptive of agent's role
- Used to invoke agent with Task tool

**description** (Required):
- Clear statement of agent's purpose and responsibilities
- Written in third person: "Processes links" not "I process links"
- 1-2 sentences max
- Used for subagent discovery

**tools** (Required):
- Comma-separated list of tools agent can use
- Be restrictive: only include necessary tools
- Common tools: Read, Write, Edit, Bash, WebFetch, Task
- Task tool if agent spawns subagents

**color** (Optional):
- Visual identifier in Claude Code UI
- Options: red, blue, green, yellow, purple, orange, pink, gray
- Use consistently across related agents
- Default: blue

**model** (Optional):
- Which Claude model to use: haiku, sonnet, opus
- `inherit`: Use same model as parent (default)
- haiku: Fast, cost-effective for simple tasks
- opus: Complex reasoning, critical tasks
- Default: inherit from parent context

**Examples:**

```yaml
---
name: link-processor
description: Process a single web link with extraction and summarization
tools: Bash, Read, Write, WebFetch
color: blue
model: haiku
---
```

```yaml
---
name: spec-verifier
description: Verify that a specification is complete and meets quality standards
tools: Read, Task
color: purple
model: opus
---
```

### Skills Frontmatter

**Location**: `.claude/skills/skill-name/SKILL.md`

**Template:**
```yaml
---
name: skill-name
description: What skill does and when Claude should use it
allowed-tools: Tool1, Tool2              # Optional
---
```

**Fields:**

**name** (Required):
- kebab-case naming: `pdf-extractor`, `api-client`
- Descriptive of capability
- Used in skill selection logs

**description** (Required):
- **Critical for skill discovery** - determines when Claude activates skill
- Must include BOTH what it does AND when to use it
- Include trigger words users might mention
- Be specific and actionable
- Written in third person
- Keep under 200 characters for optimal context usage

**allowed-tools** (Optional):
- Comma-separated list of allowed tools
- Restricts tools for security or read-only operations
- Common patterns:
  - Analysis: `Read`
  - Processing: `Read, Write`
  - Automation: `Read, Write, Bash`
- Omit to allow all available tools

**Examples:**

```yaml
---
name: pdf-table-extractor
description: Extract tables from PDF files and convert to CSV format. Use when working with PDF documents containing tabular data or when the user asks to extract tables from PDFs.
allowed-tools: Read, Write, Bash
---
```

```yaml
---
name: code-analyzer
description: Analyzes Python code for PEP 8 compliance and suggests improvements. Use when reviewing Python code quality or addressing linting issues.
allowed-tools: Read
---
```

### Field Value Conventions

**Tool Names:**
- Exact casing as provided by Claude Code
- Common tools: Read, Write, Edit, Bash, Task, WebFetch, Playwright
- MCP tools: `mcp__server-name__tool-name`

**Color Names:**
- Lowercase: `blue`, `red`, `green`, etc.
- Use semantic meaning:
  - red: Critical/important operations
  - blue: General purpose
  - green: Success/validation
  - yellow: Warning/review
  - purple: Specialized/analysis
  - gray: Utility/helper

**Model Names:**
- Lowercase: `haiku`, `sonnet`, `opus`, `inherit`
- Selection guide:
  - haiku: Simple, repetitive, fast tasks
  - sonnet: Balanced capability and cost
  - opus: Complex reasoning, critical accuracy
  - inherit: Use parent's model (default)

### Description Writing

**Commands** (action-oriented):
- "Add a link to collection"
- "Create a new specification"
- "Analyze code for issues"

**Agents** (role-oriented):
- "Process a single web link with extraction"
- "Verify specification completeness"
- "Analyze codebase structure"

**Skills** (capability + trigger):
- "Extract tables from PDFs. Use when working with PDF documents."
- "Analyze TypeScript types. Use when reviewing code quality."
- "Parse API responses. Use when integrating with REST APIs."

### Common Patterns

**Read-Only Analysis:**
```yaml
# Command
allowed-tools: Read

# Agent
tools: Read

# Skill
allowed-tools: Read
```

**File Processing:**
```yaml
# Command
allowed-tools: Read, Write, Edit

# Agent
tools: Read, Write, Edit

# Skill
allowed-tools: Read, Write
```

**Multi-Agent Orchestration:**
```yaml
# Command spawning subagents
allowed-tools: Task, Read, Write

# Agent spawning subagents
tools: Task, Read, Write
```

**External Integration:**
```yaml
# Web scraping/API calls
allowed-tools: WebFetch, Read, Write

# With data processing
tools: WebFetch, Bash, Read, Write
```

### Validation

**Required Field Check:**
- Commands: description ✓
- Agents: name, description, tools ✓
- Skills: name, description ✓

**Value Validation:**
- Tool names match available tools
- Color names are valid options
- Model names are valid options
- Descriptions are concise and clear

**Testing:**
- Commands appear correctly in palette
- Agents can be invoked with Task tool
- Skills activate at appropriate times
- Tool restrictions work as expected

### Documentation in Content

**After frontmatter**, include:
- Purpose and use cases
- Workflow or steps
- Examples
- Edge cases
- Error handling

**Keep frontmatter minimal:**
- Only metadata goes in frontmatter
- Descriptive content goes in markdown body
- Don't duplicate information

### Evolution and Maintenance

**When to Update:**
- Tool access needs change
- Description needs refinement
- Agent model selection optimization
- Skill activation issues

**Version Control:**
- Document significant changes in commits
- Notify team of breaking changes
- Test after modifications

**Consistency:**
- Use same formatting across project
- Follow team conventions for optional fields
- Document deviations from standards

### Examples: Before and After

**Command - Before** (too verbose):
```yaml
---
description: This command will help you add a new web link to your collection by automatically extracting the content and generating a nice summary
argument-hint: Please provide the URL you want to add
allowed-tools: Task, Read, Write, Edit, Bash, WebFetch
---
```

**Command - After** (concise):
```yaml
---
description: Add web link with automatic extraction and summarization
argument-hint: [URL]
allowed-tools: Task, Read, Write
---
```

**Agent - Before** (vague):
```yaml
---
name: helper
description: Helps with various tasks
tools: Read, Write, Edit, Bash, Task, WebFetch
---
```

**Agent - After** (specific):
```yaml
---
name: link-processor
description: Process web link with content extraction and summarization
tools: Bash, Read, Write, WebFetch
color: blue
model: haiku
---
```

**Skill - Before** (missing trigger):
```yaml
---
name: document-processor
description: Processes documents
---
```

**Skill - After** (includes trigger):
```yaml
---
name: pdf-table-extractor
description: Extract tables from PDF files and convert to CSV format. Use when working with PDF documents containing tabular data.
allowed-tools: Read, Write, Bash
---
```

### Related Standards

- `@agent-os/standards/claude-code/command-design.md` - Command structure and workflow
- `@agent-os/standards/claude-code/agent-design.md` - Agent roles and patterns
- `@agent-os/standards/claude-code/skill-design.md` - Skill creation and discovery
- `@agent-os/standards/global/conventions.md` - File naming and organization
