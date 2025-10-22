## Claude Code Skills design standards

### Overview

Claude Code Skills are model-invoked capabilities that extend Claude's functionality through organized folders containing instructions and resources. Unlike slash commands (user-invoked), Skills are **autonomously activated** by Claude based on request context and skill descriptions.

**Key Difference:**
- **Commands**: User explicitly invokes with `/command-name`
- **Skills**: Claude automatically decides to use based on description and context

### File Structure

```
.claude/skills/skill-name/
├── SKILL.md          # Required: Main skill definition
├── reference.md      # Optional: Detailed reference docs
├── examples.md       # Optional: Example usage
└── scripts/          # Optional: Supporting scripts
```

**Location Options:**
- Personal skills: `~/.claude/skills/`
- Project skills: `.claude/skills/`
- Plugin skills: Bundled with installed plugins

### SKILL.md Structure

**YAML Frontmatter** (Required):
```yaml
---
name: skill-name
description: What the skill does and when to use it
allowed-tools: Read, Write, Bash  # Optional: restrict tool access
---
```

**Content** (Markdown):
- Clear, step-by-step instructions
- Examples of use cases
- Edge cases and error handling
- Keep under 500 lines (use separate reference files for details)

### Description Best Practices

**Critical for Discovery**: The description determines when Claude activates the skill.

**Good descriptions** (specific, actionable, include "when to use"):
```yaml
description: Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

```yaml
description: Analyzes TypeScript codebases for type coverage and suggests improvements. Use when reviewing TypeScript code quality or addressing type errors.
```

**Bad descriptions** (vague, no trigger words):
```yaml
description: Helps with documents  # Too vague
```

```yaml
description: Processes data  # No context for when to use
```

**Description Writing Tips:**
- Write in third person: "Processes Excel files" not "I can help you..."
- Include both **what** it does and **when** to use it
- Use concrete trigger words that users might mention
- Be specific about capabilities and limitations

### Scope Definition

**The Conciseness Principle:**
> "Every token competes with conversation history and other context."

Challenge every piece of information:
- Does Claude really need this explanation?
- Can you assume existing knowledge?
- Is this detail necessary for task completion?

**Focus on One Capability:**
- **Good**: "Extract tables from PDFs"
- **Bad**: "Work with documents, spreadsheets, and presentations"

**Set Appropriate Freedom Levels:**
- **High freedom** (text-based): Multiple valid approaches exist
  - Example: "Summarize this article"
  - Give general guidance, allow variation
- **Medium freedom** (pseudocode): Some variation acceptable
  - Example: "Parse JSON and extract specific fields"
  - Provide structure, allow implementation details
- **Low freedom** (specific scripts): Operations are fragile
  - Example: "Run this exact git command sequence"
  - Be explicit about required steps

### Progressive Disclosure

**Keep SKILL.md concise** (< 500 lines):
- Core instructions and common use cases in SKILL.md
- Detailed reference in separate files
- Use links: `[Advanced usage](./reference.md)`

**Bad Structure** (too much in one file):
```
SKILL.md (2,500 lines)
├── Basic usage
├── Advanced usage
├── API reference
├── All examples
└── Troubleshooting guide
```

**Good Structure** (progressive disclosure):
```
SKILL.md (400 lines) - Core instructions
reference.md - Detailed API docs
examples.md - Comprehensive examples
troubleshooting.md - Edge cases
```

### Naming Conventions

**Skill Names:**
- Use gerund form (present participle): "Processing PDFs", "Analyzing spreadsheets"
- Avoid generic names: "Helper", "Utils", "Documents"
- Be specific: "Extracting invoice data" not "Working with files"

**File Names:**
- lowercase-with-dashes for skill directories
- PascalCase or lowercase for supporting files (be consistent)

### Tool Selection

**allowed-tools Field:**
- Restricts which tools Claude can use when skill is active
- Use for security or read-only operations
- Omit to allow all available tools

**Examples:**
```yaml
# Read-only analysis skill
allowed-tools: Read

# File processing skill
allowed-tools: Read, Write

# Full capability skill (omit field)
# allowed-tools: (not specified)
```

### Testing Strategy

**Test Across Models:**
- What works for Claude Opus may need more detail for Haiku
- Haiku: May need more explicit instructions
- Opus: Can work with higher-level guidance

**Validation Process:**
1. **Description test**: Does Claude activate skill at the right time?
2. **Functionality test**: Does the skill accomplish its purpose?
3. **Edge cases**: How does it handle unexpected inputs?
4. **Team testing**: Have colleagues validate clarity and activation

**Build Evaluations First:**
- Create test cases before extensive documentation
- Ensure skills solve actual problems, not anticipated ones
- Iterate based on real usage patterns

### Examples: Good vs Bad

**Good Skill Structure:**
```markdown
---
name: pdf-table-extractor
description: Extracts tables from PDF files and converts to CSV format. Use when working with PDF documents containing tabular data or when the user asks to extract tables from PDFs.
allowed-tools: Read, Write, Bash
---

# PDF Table Extraction

Extract structured tables from PDF documents.

## How It Works

1. Read PDF file with specified path
2. Detect table structures using layout analysis
3. Extract table data preserving structure
4. Output as CSV with headers

## Usage

When the user provides a PDF with tables, follow these steps:

[specific, actionable instructions]

## Edge Cases

- Scanned PDFs: [how to handle]
- Multi-page tables: [continuation logic]
- Complex layouts: [fallback approach]
```

**Bad Skill Structure:**
```markdown
---
name: document-helper
description: Helps with documents
---

# Document Helper

This skill helps you work with various document types.

It can do many things like reading files, processing content,
and generating outputs. Just use it whenever you need help
with documents.

[2,000 lines of nested examples and edge cases]
```

### Common Patterns

**Analysis Skills:**
```yaml
description: Analyzes Python code for PEP 8 compliance and suggests improvements. Use when reviewing Python code quality or addressing linting issues.
```

**Transformation Skills:**
```yaml
description: Converts Markdown documentation to HTML with syntax highlighting. Use when generating static documentation or publishing markdown content.
```

**Integration Skills:**
```yaml
description: Fetches and parses data from REST APIs with authentication handling. Use when integrating with external services or consuming API endpoints.
```

### Documentation Requirements

**In SKILL.md:**
- Purpose and use cases
- Step-by-step instructions
- Common patterns and examples
- Tool requirements
- Error handling approach

**In Supporting Files:**
- Detailed API references (reference.md)
- Comprehensive examples (examples.md)
- Advanced usage patterns
- Troubleshooting guides

### Version Control

**Track Changes:**
- Document version changes in commit messages
- Use semantic versioning for major changes
- Notify team of breaking changes

**Team Coordination:**
- Skills affect all team members using the project
- Test thoroughly before committing changes
- Document rationale for skill design decisions

### Related Standards

- `@agent-os/standards/claude-code/prompt-engineering.md` - Effective prompt patterns
- `@agent-os/standards/claude-code/command-design.md` - Slash command design
- `@agent-os/standards/global/frontmatter-conventions.md` - YAML frontmatter standards
- `@agent-os/standards/global/conventions.md` - File naming and organization

### References

- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Agent Skills Best Practices](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/best-practices)
