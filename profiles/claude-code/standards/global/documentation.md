## Documentation Standards for Claude Code

### Overview

Good documentation makes commands, agents, and skills discoverable, understandable, and maintainable. This standard covers inline documentation, README patterns, and documentation best practices for Claude Code projects.

### Documentation Layers

**Three Levels of Documentation:**

1. **Frontmatter**: Metadata for discovery (required)
2. **Inline Documentation**: In-file guidance (required)
3. **Supporting Documentation**: Detailed references (optional)

**Example:**

```markdown
---
name: test-writer
description: Generates test files for source files using project conventions
tools: Read, Write, Glob
---

# Test Writer Agent (Inline Documentation)

You are a testing specialist focused on comprehensive test coverage.

## Your Role

Generate test files following project testing patterns...

## Process

1. Read source file
2. Detect test framework
3. Create test file
4. Add test cases

For detailed testing patterns, see: reference.md (Supporting Documentation)
```

### Frontmatter Documentation

**Purpose:**
- Discovery: How Claude finds commands/agents/skills
- Invocation: How to use them
- Configuration: Tool access, model selection

**Commands:**

```yaml
---
description: Add test file for a given source file
argument-hint: <source-file>
allowed-tools: Read, Write, Glob
---
```

**Guidelines:**
- `description`: One-line summary, imperative mood
- `argument-hint`: Show expected format with brackets
- `allowed-tools`: List actual tool names used
- See `@agent-os/standards/global/frontmatter-conventions.md`

**Agents:**

```yaml
---
name: test-writer
description: Generates comprehensive test files for source files
tools: Read, Write, Glob
color: green
model: sonnet
---
```

**Guidelines:**
- `name`: Match filename, describe role
- `description`: What it does and when to use
- `tools`: Minimum necessary set
- `color`: Semantic (green for testing)
- `model`: Appropriate for complexity

**Skills:**

```yaml
---
name: pdf-processor
description: Extract text and tables from PDF files. Use when working with PDFs.
allowed-tools: Read, Write, Bash
---
```

**Guidelines:**
- `description`: MUST include "when to use"
- Include trigger keywords
- Be specific about capabilities
- Critical for skill discovery

### Inline Documentation Structure

**Standard Pattern:**

```markdown
---
[frontmatter]
---

# Title

[1-2 sentence overview]

## Overview

[Detailed description of purpose and use]

## Your Role (for agents)
## What This Does (for commands)
## When to Use (for skills)

[Core responsibility]

## Process / Steps

[Step-by-step instructions]

## Guidelines

[Rules and best practices]

## Examples

[Concrete examples]

## Edge Cases

[Error handling, special situations]

## Related Standards

[Cross-references]

## References

[External links]
```

**Section Purposes:**

**Overview:**
- What this command/agent/skill does
- When to use it
- Prerequisites

**Process/Steps:**
- Concrete, actionable steps
- Numbered for sequential processes
- Bulleted for non-sequential

**Guidelines:**
- Specific rules
- Constraints
- Best practices
- What to avoid

**Examples:**
- Show don't tell
- Cover common cases
- Include edge cases
- Demonstrate expected output

**Edge Cases:**
- Error handling
- Invalid input
- Missing dependencies
- Fallback behavior

### Command Documentation

**Simple Command Example:**

```markdown
---
description: Format code according to project style guide
argument-hint: [file-path]
allowed-tools: Read, Write, Edit
---

# Format Code

Automatically format code files following project conventions.

## Overview

This command:
- Detects file type (JS, TS, Python, etc.)
- Applies appropriate formatter
- Preserves file metadata
- Reports formatting changes

## Usage

/format-code src/utils.ts
/format-code src/  # Formats entire directory

## What This Does

1. Detect file type from extension
2. Read current content
3. Apply formatter (Prettier, Black, etc.)
4. Write formatted content
5. Report changes

## Supported File Types

- JavaScript/TypeScript: Prettier
- Python: Black
- Rust: rustfmt
- Go: gofmt

## Prerequisites

- Formatter must be installed
- File must be in supported language
- Must have write permission

## Examples

### Format Single File

/format-code src/utils.ts

Output:
```
Formatted src/utils.ts
- 3 lines changed
- Added semicolons
- Fixed indentation
```

### Format Directory

/format-code src/

Output:
```
Formatted 12 files
- src/utils.ts
- src/parser.ts
...
```

## Error Handling

**File not found:**
```
ERROR: File not found: src/missing.ts
Suggestion: Check file path and try again
```

**Unsupported file type:**
```
ERROR: Unsupported file type: .jpg
Supported: .js, .ts, .py, .rs, .go
```

**Formatter not installed:**
```
ERROR: Prettier not found
Install: npm install -D prettier
```

## Related Commands

- /lint-code - Check for issues without formatting
- /check-style - Verify style compliance

## References

- [Prettier Documentation](https://prettier.io/)
```

**Key Elements:**
- Clear usage examples
- Supported scenarios
- Error messages
- Prerequisites
- Related commands

### Agent Documentation

**Agent Example:**

```markdown
---
name: test-writer
description: Generates comprehensive test files for source files using project conventions
tools: Read, Write, Glob
color: green
model: sonnet
---

# Test Writer Agent

You are a testing specialist focused on creating maintainable, comprehensive tests.

## Your Role

Generate test files for given source files that:
- Follow project testing conventions
- Cover main functionality
- Include edge cases
- Use appropriate test framework

## Process

1. **Analyze Source**
   - Read source file
   - Identify exported functions/classes
   - Note parameter types and return values

2. **Detect Framework**
   - Check package.json for test dependencies
   - Look for existing test patterns
   - Default to Jest if unclear

3. **Create Test File**
   - Follow naming convention: [filename].test.[ext]
   - Set up imports
   - Create describe/test structure
   - Add test cases

4. **Generate Tests**
   - Happy path tests
   - Edge cases (null, undefined, empty)
   - Error cases
   - Type validation (if TypeScript)

## Guidelines

**Test Structure:**
- One describe block per function/class
- Descriptive test names: "should X when Y"
- Arrange-Act-Assert pattern
- Mock external dependencies

**Coverage Goals:**
- Cover main functionality
- Test edge cases
- Don't over-test trivial code
- Focus on behavior, not implementation

**Naming:**
- Test files: [filename].test.[ext]
- Test names: should [expected behavior] when [condition]
- Clear, descriptive names over brevity

## Examples

### TypeScript Function

Source: src/utils/formatDate.ts
```typescript
export function formatDate(date: Date): string {
  return date.toISOString().split('T')[0]
}
```

Generated Test: src/utils/formatDate.test.ts
```typescript
import { formatDate } from './formatDate'

describe('formatDate', () => {
  it('should format valid date as ISO string', () => {
    const date = new Date('2024-01-15')
    expect(formatDate(date)).toBe('2024-01-15')
  })

  it('should handle dates with time component', () => {
    const date = new Date('2024-01-15T14:30:00Z')
    expect(formatDate(date)).toBe('2024-01-15')
  })

  it('should throw error for invalid date', () => {
    const date = new Date('invalid')
    expect(() => formatDate(date)).toThrow()
  })
})
```

### Python Class

Source: lib/parser.py
```python
class Parser:
    def parse(self, text: str) -> dict:
        return {"content": text, "length": len(text)}
```

Generated Test: tests/test_parser.py
```python
import pytest
from lib.parser import Parser

class TestParser:
    def test_parse_valid_text(self):
        parser = Parser()
        result = parser.parse("hello")
        assert result == {"content": "hello", "length": 5}

    def test_parse_empty_string(self):
        parser = Parser()
        result = parser.parse("")
        assert result == {"content": "", "length": 0}

    def test_parse_unicode(self):
        parser = Parser()
        result = parser.parse("héllo")
        assert result["length"] == 5
```

## Edge Cases

**Source file is empty:**
- Create minimal test scaffold
- Add TODO comment for user
- Don't generate actual test cases

**No clear exports:**
- Report to parent: "No exported functions found"
- Suggest checking source file
- Don't create empty test file

**Framework unclear:**
- Use most common (Jest for JS/TS, pytest for Python)
- Add comment explaining assumption
- Report to parent for confirmation

**Complex mocking needed:**
- Set up basic mock structure
- Add TODO comments for complex scenarios
- Focus on testable units

## Return Format

```markdown
### Summary
Created test file: [path]
Framework: [detected framework]
Test cases: [number]

### Tests Created
- [function/class]: [number] tests
  - Happy path
  - Edge cases
  - Error handling

### Issues
[Any problems encountered]

### Recommendations
- [Suggestions for improvement]
- [Areas needing manual attention]
```

## Related Standards

{{standards/testing/test-writing.md}}

## References

- [Jest Documentation](https://jestjs.io/)
- [pytest Documentation](https://docs.pytest.org/)
```

**Key Elements:**
- Clear role definition
- Step-by-step process
- Concrete examples
- Error handling
- Return format specification

### Skill Documentation

**Skill Example:**

```markdown
---
name: pdf-processor
description: Extract text and tables from PDF files. Use when working with PDFs or when user mentions PDF processing.
allowed-tools: Read, Write, Bash
---

# PDF Processing Skill

Extract text, tables, and metadata from PDF files.

## When to Use

Activate this skill when the user:
- Mentions "PDF" or "pdf" in request
- Asks to extract data from documents
- Needs to process forms or tables
- Wants to analyze PDF content

## What You Can Do

- Extract plain text from PDFs
- Extract tables to CSV format
- Read PDF metadata (title, author, etc.)
- Split multi-page PDFs
- Merge multiple PDFs

## How It Works

### Text Extraction

1. Use Read tool to access PDF file
2. Extract text preserving structure
3. Return formatted text content

Example:
```bash
# Using pdftotext (from poppler-utils)
pdftotext document.pdf output.txt
```

### Table Extraction

1. Detect table structures
2. Extract to CSV format
3. Preserve headers and data types

Example:
```bash
# Using tabula-py
tabula document.pdf --output tables.csv
```

### Metadata Reading

1. Read PDF properties
2. Extract title, author, creation date
3. Return structured metadata

Example:
```bash
# Using pdfinfo
pdfinfo document.pdf
```

## Prerequisites

**Required Tools:**
- poppler-utils (pdftotext, pdfinfo)
- tabula-py (for table extraction)

**Installation:**
```bash
# Ubuntu/Debian
apt-get install poppler-utils

# macOS
brew install poppler

# Python package
pip install tabula-py
```

## Examples

### Extract Text

User: "Extract text from report.pdf"

Process:
1. Check if report.pdf exists
2. Run: pdftotext report.pdf
3. Return extracted text

### Extract Tables

User: "Get tables from financial-report.pdf as CSV"

Process:
1. Detect tables in PDF
2. Run: tabula financial-report.pdf --output tables.csv
3. Return CSV file path

## Edge Cases

**Scanned PDFs (images):**
- Inform user OCR is needed
- Suggest using tesseract
- Provide alternative approach

**Password-protected PDFs:**
- Detect encryption
- Ask user for password
- Use --password flag

**Large PDFs:**
- Warn about processing time
- Process page by page if possible
- Provide progress updates

**Malformed PDFs:**
- Attempt repair with ghostscript
- Report specific errors
- Suggest manual review

## Error Handling

**File not found:**
```
ERROR: PDF file not found: report.pdf
Check file path and try again.
```

**Tool not installed:**
```
ERROR: pdftotext not found
Install: apt-get install poppler-utils
```

**Invalid PDF:**
```
ERROR: Invalid PDF format
File may be corrupted or not a valid PDF.
```

## Performance Tips

- Process large PDFs page by page
- Use `--first-page` and `--last-page` for ranges
- Consider file size before full extraction
- Cache results when processing multiple times

## Related Skills

- ocr-processor: For scanned documents
- document-analyzer: For document classification

## References

- [Poppler Documentation](https://poppler.freedesktop.org/)
- [Tabula](https://tabula.technology/)

---

For detailed API reference, see: [reference.md](./reference.md)
```

**Key Elements:**
- Clear activation triggers
- Concrete capabilities
- Installation instructions
- Comprehensive examples
- Edge case handling

### Supporting Documentation

**When to Create Supporting Docs:**

```
skill-name/
├── SKILL.md          # Core instructions (< 500 lines)
├── reference.md      # Detailed API/options
├── examples.md       # Comprehensive examples
└── troubleshooting.md # Edge cases and fixes
```

**SKILL.md:**
- Core functionality
- Common use cases
- Basic examples
- When to use

**reference.md:**
- Detailed API documentation
- All configuration options
- Advanced features
- Complete parameter reference

**examples.md:**
- Comprehensive example library
- Different scenarios
- Real-world use cases
- Copy-paste ready examples

**troubleshooting.md:**
- Common errors and solutions
- Edge cases
- Debugging strategies
- Known limitations

### README Patterns

**Project README:**

```markdown
# Project Name

Brief description of what this project does.

## Claude Code Extensions

This project includes custom commands, agents, and skills:

### Commands

- `/format-code` - Format code according to style guide
- `/add-test` - Add test file for source file
- `/review-pr` - Review pull request

### Agents

- `code-reviewer` - Analyzes code for issues
- `test-writer` - Generates test files
- `doc-generator` - Creates documentation

### Skills

- `pdf-processor` - Extract text/tables from PDFs
- `api-client` - Interact with REST APIs

## Getting Started

1. Install dependencies: `npm install`
2. Try a command: `/format-code src/utils.ts`
3. Invoke an agent: Use Task tool with `code-reviewer`

## Documentation

- [Command Guide](docs/commands.md)
- [Agent Reference](docs/agents.md)
- [Skill Documentation](docs/skills.md)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.
```

**Skill README (reference.md):**

```markdown
# PDF Processor - Detailed Reference

## API Overview

Complete reference for PDF processing capabilities.

## Text Extraction

### Basic Extraction

Command: `pdftotext input.pdf output.txt`

Options:
- `-layout`: Maintain original layout
- `-raw`: Raw text without layout
- `-enc UTF-8`: Specify encoding

### Page Ranges

Extract specific pages:
```bash
pdftotext -f 1 -l 5 input.pdf output.txt
```

## Table Extraction

### Using Tabula

[Detailed tabula options and examples...]

## Advanced Usage

[Complex scenarios, custom configurations...]

## API Reference

[Complete parameter listing...]
```

### Documentation Anti-Patterns

**Avoid:**

```markdown
❌ Vague descriptions
description: Helps with things

❌ No examples
## Usage
Just use the command appropriately.

❌ Assumed knowledge
Use the standard approach everyone knows.

❌ Missing error handling
## Process
1. Read file
2. Process it
3. Done

❌ No prerequisites
Works out of the box! (but actually requires 5 dependencies)

❌ Everything in one file
[5,000 lines of documentation in SKILL.md]
```

**Use:**

```markdown
✓ Specific descriptions
description: Extract tables from PDF files and convert to CSV format

✓ Concrete examples
## Usage
/command-name src/file.ts
Creates: src/file.test.ts

✓ Explicit instructions
1. Read source file with Read tool
2. Detect test framework from package.json
3. Create test file using Write tool

✓ Error handling
If file not found:
- Report error clearly
- Suggest valid paths
- Do not proceed

✓ Prerequisites listed
Required:
- poppler-utils installed
- PDF file must exist
- Write permission for output

✓ Progressive disclosure
Core docs in SKILL.md (< 500 lines)
Details in reference.md
```

### Documentation Maintenance

**Keep Documentation Current:**

1. **Update with changes**: When modifying commands/agents/skills, update docs
2. **Version references**: Note when behavior changes
3. **Test examples**: Verify examples still work
4. **Remove obsolete**: Delete outdated information

**Documentation Review Checklist:**

```markdown
- [ ] Frontmatter is complete and accurate
- [ ] Description is clear and specific
- [ ] Examples are concrete and tested
- [ ] Error cases are documented
- [ ] Prerequisites are listed
- [ ] Tool restrictions are correct
- [ ] External links work
- [ ] Cross-references are valid
- [ ] No outdated information
- [ ] Formatting is consistent
```

### Related Standards

- `@agent-os/standards/global/frontmatter-conventions.md` - Metadata standards
- `@agent-os/standards/global/coding-style.md` - Markdown formatting
- `@agent-os/standards/claude-code/command-design.md` - Command structure
- `@agent-os/standards/claude-code/agent-design.md` - Agent patterns
- `@agent-os/standards/claude-code/skill-design.md` - Skill creation

### References

- [Write the Docs](https://www.writethedocs.org/)
- [Claude Code Documentation Best Practices](https://docs.claude.com/en/docs/claude-code/best-practices)
