## Markdown documentation standards

- **Line Length**: Strictly enforce 120 characters per line maximum in all markdown files
- **Line Wrapping**: Break long lines at natural points (periods, commas, conjunctions) for readability
- **File Endings**: All markdown files must end with exactly one newline character
- **Fenced Code Blocks**: Always specify language for syntax highlighting (bash, yaml, json, etc.)
- **Heading Hierarchy**: Use proper heading levels (h1 for title, h2 for sections, h3 for subsections)
- **Unique Headings**: Avoid duplicate headings within the same document (except in spec files)
- **Link References**: Use descriptive link text; verify all internal links are valid
- **Table Formatting**: Ensure proper table structure with alignment markers
- **Lists**: Use consistent markers (-, *, +) and proper indentation for nested lists
- **Emphasis**: Use proper headings instead of bold text for section titles
- **Code Inline**: Use backticks for inline code, commands, file paths, and variable names
- **Markdownlint Compliance**: All markdown files must pass markdownlint-cli2 validation
