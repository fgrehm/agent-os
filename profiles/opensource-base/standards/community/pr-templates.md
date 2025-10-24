## Pull request template best practices for open source

- **Single Template Location**: Place at `.github/pull_request_template.md` for automatic use
- **Multiple Templates**: Or create `.github/PULL_REQUEST_TEMPLATE/` directory with specific templates (feature.md, bugfix.md, docs.md)
- **Essential Sections**: Description, type of change, related issues, testing performed, checklist
- **Link to Issues**: Auto-reference related issues with "Closes #123" or "Fixes #456" syntax
- **Change Type Checkboxes**: Bug fix, new feature, breaking change, documentation, refactoring
- **Testing Evidence**: Require description of how changes were tested (unit tests, manual testing, before/after screenshots)
- **Documentation Updates**: Checklist item for README, API docs, CHANGELOG updates when applicable
- **Breaking Changes Section**: Dedicated section for migration guidance if changes break existing usage
- **Code Quality Checklist**: Self-review, linting, tests passing, no new warnings
- **Contributor Guidance**: Include helpful comments explaining each section's purpose
- **Keep Optional**: Most fields optional except description and testing - trust contributors to provide what's relevant
- **Visual Changes**: Request screenshots/GIFs for UI changes
- **Start Minimal**: Single template with basic fields, add specialized templates only if needed
