## Issue template best practices for open source

- **Essential Templates**: Create templates for bug reports, feature requests, and questions as a minimum
- **GitHub Template Location**: Place in `.github/ISSUE_TEMPLATE/` directory with descriptive filenames
- **YAML Frontmatter**: Use frontmatter to set name, description, title prefix, and labels automatically
- **Required Information**: Bug templates should request: steps to reproduce, expected vs actual behavior, environment details
- **Feature Request Structure**: Ask for: problem/motivation, proposed solution, use cases, alternatives considered
- **Security Template Redirect**: Create security template that redirects to private reporting (don't use for actual reports)
- **Config File**: Use `config.yml` to disable blank issues and link to discussions or docs for questions
- **Keep Concise**: Templates should be scannable (10-15 fields max), not intimidating walls of text
- **Examples in Templates**: Show example answers in placeholder text to guide contributors
- **Optional vs Required**: Mark critical fields clearly, but don't require everything
- **Easy First Issues**: Add template for contributors looking for starter tasks
- **Iterate Based on Usage**: Start minimal, add fields only when repeatedly asking for same information
- **Friendly Tone**: Templates should be welcoming, not bureaucratic
