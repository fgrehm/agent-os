## Coding Style Standards for Claude Code Development

### Overview

Claude Code commands, agents, and skills are written in Markdown with YAML frontmatter. Consistent formatting improves readability, maintainability, and collaboration.

### Markdown Formatting

**Headers:**

```markdown
## Top-level Section (H2)

### Subsection (H3)

#### Detail Level (H4)
```

**Guidelines:**
- Use H2 (`##`) for main sections
- Use H3 (`###`) for subsections
- Use H4 (`####`) for detail levels
- Don't skip heading levels (H2 → H4)
- One H1 (`#`) per file maximum (usually title)
- Blank line before and after headers

**Lists:**

**Unordered lists:**
```markdown
- First item
- Second item
  - Nested item
  - Another nested item
- Third item
```

**Ordered lists:**
```markdown
1. First step
2. Second step
3. Third step
```

**Guidelines:**
- Use `-` for unordered lists (not `*` or `+`)
- Use `1.` for all ordered list items (auto-numbering)
- Indent nested lists with 2 spaces
- Blank line before and after lists
- Use ordered lists for sequential steps
- Use unordered lists for non-sequential items

**Code Blocks:**

**Fenced code blocks:**
````markdown
```typescript
function example() {
  return true
}
```
````

**Inline code:**
```markdown
Use the `Read` tool to examine files.
```

**Guidelines:**
- Always specify language for syntax highlighting
- Use inline code for tool names, file paths, variables
- Use fenced blocks for multi-line code
- Indent code blocks for proper rendering

**Emphasis:**

```markdown
*Italic text* or _italic text_
**Bold text** or __bold text__
***Bold and italic*** or ___bold and italic___
```

**Guidelines:**
- Use `**bold**` for emphasis (not `__bold__`)
- Use `*italic*` for light emphasis (not `_italic_`)
- Use sparingly - structure > emphasis

**Links:**

```markdown
[Link text](https://example.com)
[Relative link](./other-file.md)
[Anchor link](#section-name)
```

**Guidelines:**
- Use descriptive link text (not "click here")
- Use relative paths for internal links
- Use absolute URLs for external links

**Blockquotes:**

```markdown
> Important principle or quote
```

**Guidelines:**
- Use for principles, quotes, or callouts
- Keep concise (1-2 sentences)
- Don't nest blockquotes

### YAML Frontmatter Style

**General Rules:**

```yaml
---
field_name: value
another_field: another value
list_field: Item1, Item2, Item3
---
```

**Guidelines:**
- Use lowercase for field names
- Use snake_case for multi-word fields
- Use consistent spacing: `key: value` (space after colon)
- Order fields consistently (see templates below)
- Keep values concise
- Use comma-separated for lists (not YAML arrays)

**Command Frontmatter:**

```yaml
---
description: Brief description of what the command does
argument-hint: [optional-arg]
allowed-tools: Read, Write, Edit
---
```

**Order:**
1. `description` (required)
2. `argument-hint` (optional)
3. `allowed-tools` (optional)

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

**Order:**
1. `name` (required)
2. `description` (required)
3. `tools` (required)
4. `color` (optional)
5. `model` (optional)

**Skill Frontmatter:**

```yaml
---
name: skill-name
description: What skill does and when Claude should use it
allowed-tools: Read, Write
---
```

**Order:**
1. `name` (required)
2. `description` (required)
3. `allowed-tools` (optional)

### File Organization

**Section Order:**

```markdown
---
[YAML frontmatter]
---

# Title

[1-2 sentence overview]

## Overview

[Detailed description of purpose and use]

## [Main Sections]

[Core content organized by concern]

## Examples

[Concrete examples]

## Related Standards

[Links to related standards]

## References

[External documentation links]
```

**Guidelines:**
- Frontmatter always first
- Title immediately after frontmatter
- Brief overview before detailed sections
- Examples near the end
- References at the very end
- Group related content under clear headers

### Naming Conventions

**File Names:**

```
command-name.md           # Commands (kebab-case)
agent-name.md            # Agents (kebab-case)
skill-name/SKILL.md      # Skills (directory + SKILL.md)
standard-topic.md        # Standards (kebab-case)
```

**Guidelines:**
- Use kebab-case (lowercase with hyphens)
- Be descriptive but concise
- Match agent/command/skill name
- Use .md extension
- SKILL.md is always capitalized for skills

**Directory Names:**

```
.claude/commands/
.claude/agents/
.claude/skills/skill-name/
profiles/profile-name/
standards/category-name/
```

**Guidelines:**
- Use kebab-case
- Plural for collections (commands, agents, skills)
- Singular for specific items (skill-name, profile-name)
- Group related files in directories

**Variable Names in Prompts:**

```markdown
$FILE_PATH            # All caps with underscores
$COMPONENT_NAME
[placeholder]         # Lowercase in brackets
[ANOTHER_PLACEHOLDER] # Uppercase in brackets
```

**Guidelines:**
- Use `$VAR_NAME` for template variables
- Use `[placeholder]` for user-replaceable content
- Be descriptive
- Document what each variable represents

### Whitespace and Formatting

**Blank Lines:**

```markdown
## Section Header

Content starts here.

New paragraph with blank line before.

- List starts here
- Second item

Content after list has blank line before.
```

**Guidelines:**
- One blank line between sections
- One blank line before and after lists
- One blank line before and after code blocks
- One blank line between paragraphs
- No trailing whitespace

**Indentation:**

```markdown
1. First item
   - Nested item (3 spaces)
   - Another nested (3 spaces)

2. Second item
   ```typescript
   // Code block indented 3 spaces
   const x = 1
   ```
```

**Guidelines:**
- Use 2 spaces for nested lists
- Use 3 spaces for nested code blocks in lists
- Never use tabs
- Be consistent within file

**Line Length:**

```markdown
This is a paragraph that wraps naturally without hard line breaks
because Markdown handles wrapping. Only use hard breaks for semantic
paragraph separation.

Code blocks can have longer lines:
```typescript
const reallyLongVariableName = someFunction(arg1, arg2, arg3, arg4)
```
```

**Guidelines:**
- No hard line length limit for prose
- Natural wrapping is fine
- Break only at semantic boundaries
- Code blocks: follow language conventions

### Documentation Comments

**Inline Explanations:**

```markdown
## Process

1. Read source file to understand exports
2. Detect test framework from package.json
   <!-- Most projects use Jest or Vitest -->
3. Create test file with boilerplate
4. Add test cases
```

**Guidelines:**
- Use `<!-- comment -->` for inline notes
- Keep comments concise
- Explain "why" not "what"
- Remove before committing (or keep if valuable)

**TODO Comments:**

```markdown
<!-- TODO: Add examples for Vitest -->
<!-- FIXME: Handle edge case when package.json missing -->
<!-- NOTE: This assumes Jest syntax -->
```

**Guidelines:**
- Use `TODO:` for future improvements
- Use `FIXME:` for known issues
- Use `NOTE:` for important context
- Include issue number if applicable

### Example Formatting

**Good Example Structure:**

```markdown
## Examples

### Basic Usage

/command-name simple-file.ts

Creates: simple-file.test.ts

### With Options

/command-name --watch complex-file.ts

Creates test file and runs in watch mode.

### Edge Cases

/command-name empty-file.ts

Detects empty file and creates minimal test scaffold.
```

**Guidelines:**
- Use H3 for example categories
- Show command/input first
- Show result/output second
- Include edge cases
- Keep examples concise

### Consistency Patterns

**Tool References:**

```markdown
Use the Read tool to examine files.
Use the Write tool to create files.
Use the Edit tool to modify files.
```

**Not:**
```markdown
Use `read` to examine files.
use Read tool for files.
Use the read tool to examine files.
```

**File Paths:**

```markdown
Path to file: `src/components/Button.tsx`
In the `.claude/commands/` directory
```

**Not:**
```markdown
Path to file: src/components/Button.tsx
In the .claude/commands/ directory
```

**Status Indicators:**

```markdown
✓ Success
⚠ Warning
✗ Error
→ Result
```

**Checklists:**

```markdown
- [ ] Incomplete task
- [x] Completed task
```

### Error Messages

**Format:**

```markdown
## Error Handling

If file not found:
```
ERROR: Source file not found
Path: src/missing.ts
Suggestion: Check file path and try again
```

If invalid format:
```
ERROR: Invalid file format
Expected: .ts or .tsx
Received: .jpg
```
```

**Guidelines:**
- Use ERROR: prefix
- State what went wrong
- Include relevant context
- Suggest resolution
- Use code blocks for multi-line errors

### Standards References

**Injection Tags:**

```markdown
See complete coding standards:
{{standards/global/coding-style.md}}

For testing patterns:
{{standards/testing/test-writing.md}}
```

**Guidelines:**
- Use `{{path}}` syntax
- Path relative to standards directory
- Document what the reference provides
- Don't duplicate content

**External Links:**

```markdown
See: [Claude Code Documentation](https://docs.claude.com/en/docs/claude-code)
Reference: [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
```

**Guidelines:**
- Use descriptive link text
- Include full URLs
- Verify links work
- Keep external references minimal

### Anti-Patterns

**Avoid:**

```markdown
❌ Inconsistent heading levels
# H1
### H3 (skipped H2)

❌ Mixed list markers
- Item one
* Item two
+ Item three

❌ No language in code blocks
```
code here
```

❌ Trailing whitespace
Content here      [spaces]

❌ Inconsistent frontmatter
---
Description: Mixed casing
allowed_tools: inconsistent-format
---

❌ No blank lines
## Header
Content immediately
- List immediately

❌ Hard line wraps
This is a sentence that is broken
at arbitrary points for no semantic
reason.
```

### Linting and Validation

**Markdown Linters:**

Use markdownlint or similar:
```bash
markdownlint .claude/commands/**/*.md
markdownlint .claude/agents/**/*.md
```

**Common Rules:**
- MD001: Heading levels increment by one
- MD003: Heading style (consistent)
- MD004: List style (consistent)
- MD009: No trailing whitespace
- MD012: No multiple blank lines
- MD031: Blank lines around fenced code blocks

**YAML Validation:**

```bash
yamllint .claude/commands/**/*.md
```

**Pre-commit Hooks:**

```bash
# .git/hooks/pre-commit
markdownlint .claude/**/*.md
yamllint .claude/**/*.md
```

### Related Standards

- `@agent-os/standards/global/frontmatter-conventions.md` - YAML frontmatter
- `@agent-os/standards/global/conventions.md` - File naming and organization
- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/agent-design.md` - Agent structure

### References

- [Markdown Guide](https://www.markdownguide.org/)
- [CommonMark Spec](https://commonmark.org/)
- [YAML Specification](https://yaml.org/spec/)
