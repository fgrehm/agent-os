# Markdown Standards for Claude Code Documentation

This standard defines markdown conventions for Claude Code commands, subagents, and documentation.

## File Naming

Use kebab-case for all markdown files:

✅ Good:
- `command-structure.md`
- `prompt-engineering.md`
- `multi-agent-orchestration.md`

❌ Bad:
- `CommandStructure.md` (PascalCase)
- `prompt_engineering.md` (snake_case)
- `Prompt Engineering.md` (spaces)

## Frontmatter

All command files MUST include frontmatter:

```markdown
---
description: Brief description for Claude Code UI
---
```

Optional frontmatter fields for documentation:

```markdown
---
title: Document Title
tags: [tag1, tag2]
updated: 2025-01-17
---
```

## Heading Hierarchy

Use proper heading levels (don't skip levels):

✅ Good:
```markdown
# Main Title

## Section

### Subsection

#### Detail
```

❌ Bad:
```markdown
# Main Title

### Skipped Level (should be ##)
```

## Code Blocks

Always specify the language:

✅ Good:
```markdown
\`\`\`ruby
class User < ApplicationRecord
end
\`\`\`
```

❌ Bad:
```markdown
\`\`\`
class User < ApplicationRecord
end
\`\`\`
```

Common language identifiers:
- `ruby`, `python`, `javascript`, `typescript`
- `bash`, `shell`, `sh`
- `yaml`, `json`, `markdown`, `md`
- `sql`, `erb`, `html`, `css`

## Lists

### Unordered Lists

Use `-` for consistency:

✅ Good:
```markdown
- First item
- Second item
  - Nested item
  - Another nested item
```

❌ Bad:
```markdown
* First item
+ Second item
  * Mixed markers
```

### Ordered Lists

Use `1.` for all items (auto-numbering):

✅ Good:
```markdown
1. First step
1. Second step
1. Third step
```

This way inserting steps doesn't require renumbering.

### Task Lists

Use GitHub-style checkboxes:

```markdown
- [ ] Incomplete task
- [x] Complete task
```

## Inline Code

Use backticks for:
- File names: `spec.md`
- Code elements: `User.find(id)`
- Commands: `rails server`
- Variable names: `user_id`

Don't use for:
- Emphasis (use *italics* or **bold**)
- General terms (use plain text)

## Links

### Internal Links

Link to other docs using relative paths:

```markdown
See [Command Structure](../claude-code/command-structure.md)
```

### External Links

Include descriptive text:

✅ Good:
```markdown
[Agent OS Documentation](https://buildermethods.com/agent-os)
```

❌ Bad:
```markdown
[Click here](https://buildermethods.com/agent-os)
```

## Tables

Use tables sparingly. Keep them simple:

```markdown
| Tool | Purpose |
|------|---------|
| Read | Read files |
| Write | Create/update files |
| Bash | Run commands |
```

For complex data, use lists or structured text instead.

## Examples

Structure examples clearly:

### Good Example Format

```markdown
## Example: Creating a Command

**Good command structure:**

\`\`\`markdown
---
description: Create a new feature spec
---

# Create Feature Spec

Follow these steps:

1. Research requirements
2. Write specification
3. Create task list
\`\`\`

**Why this works:**
- Clear frontmatter
- Structured steps
- Actionable instructions
```

### Bad Example Format

```markdown
## Example

Here's an example:

\`\`\`
stuff
\`\`\`
```

Always explain WHY an example is good or bad.

## Admonitions

Use visual markers for important notes:

```markdown
⚠️ **Warning:** This will delete files

✅ **Good:** Use descriptive names

❌ **Bad:** Use vague names

💡 **Tip:** You can use keyboard shortcuts

📝 **Note:** This is optional
```

## Injection Tags

Document injection tags clearly:

```markdown
## Standards Reference

Include relevant standards:

\`\`\`markdown
{{standards/global/*}}
{{standards/backend/*}}
\`\`\`

This expands to a list of all matching standards.
```

## File Structure

Organize documentation with clear sections:

```markdown
# [Title]

[Brief introduction]

## Overview

[High-level explanation]

## Usage

[How to use]

## Examples

[Concrete examples]

## Common Mistakes

[What to avoid]

## See Also

[Related documentation]
```

## Line Length

- **Hard limit**: None (let editors wrap)
- **Soft guideline**: Aim for 80-100 chars for readability
- **Exception**: Don't break URLs or code

## Emphasis

Use emphasis sparingly:

- **Bold** for important terms on first use
- *Italics* for emphasis in sentences
- `Code` for technical terms

Don't: **BOLD ENTIRE SENTENCES** or use ***both*** unless necessary.

## Command Documentation

Structure command documentation consistently:

```markdown
# Command Name

Description of what the command does.

## When to Use

Explain use cases for this command.

## How It Works

Explain the workflow:

1. First step
1. Second step
1. Third step

## Example Usage

Show concrete example:

\`\`\`
User: /command-name
Agent: [does work]
User: [provides input]
Agent: [completes]
\`\`\`

## Options

If the command has variants or options, document them.

## See Also

Related commands or documentation.
```

## Standards Documentation

Structure standards consistently:

```markdown
# Standard Name

Brief description of what this standard covers.

## Principles

Core principles to follow.

## Good Patterns

✅ **Pattern Name**

\`\`\`language
[good example]
\`\`\`

Why this is good.

## Bad Patterns

❌ **Anti-pattern Name**

\`\`\`language
[bad example]
\`\`\`

Why this is bad and how to fix it.

## Common Mistakes

List frequent errors and solutions.

## Examples

Comprehensive real-world examples.
```

## Versioning and Updates

Include update info when relevant:

```markdown
---
updated: 2025-01-17
---

# Document Title

[content]

## Changelog

- **2025-01-17**: Added section on X
- **2025-01-10**: Initial version
```

## Accessibility

Write accessible documentation:

- Use descriptive link text
- Provide alt text context when needed
- Use semantic heading structure
- Don't rely solely on color/emoji for meaning

## Common Mistakes

### ❌ No Code Language

```markdown
\`\`\`
code here
\`\`\`
```

Readers and syntax highlighters need the language.

### ❌ Inconsistent List Markers

```markdown
- Item
* Other item
+ Another
```

Pick one marker (`-`) and stick with it.

### ❌ Vague Examples

```markdown
## Example

\`\`\`
do the thing
\`\`\`
```

Examples should be complete and realistic.

### ❌ No Context

```markdown
# Function

Here's how to use it:

\`\`\`ruby
def foo(bar)
  bar * 2
end
\`\`\`
```

Explain WHAT it does, WHEN to use it, WHY it's structured this way.

## Documentation Review Checklist

Before finalizing documentation:

- [ ] Frontmatter present and correct
- [ ] Headings follow proper hierarchy
- [ ] Code blocks have language specified
- [ ] Examples include explanations
- [ ] Links work and use relative paths where appropriate
- [ ] Lists use consistent markers
- [ ] Important notes use visual markers
- [ ] No typos or grammar issues
- [ ] Clear, concise language
- [ ] Actionable guidance

## Tools

Use these tools to check markdown quality:

```bash
# Check for broken links
markdown-link-check *.md

# Lint markdown
markdownlint *.md

# Preview locally
grip -b README.md
```
