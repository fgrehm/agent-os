## Changelog best practices for open source

- **Keep a Changelog Format**: Follow the [Keep a Changelog](https://keepachangelog.com/) format for consistency
- **Semantic Versioning**: Tie changelog entries to semver releases (MAJOR.MINOR.PATCH)
- **Categorize Changes**: Group entries as Added, Changed, Deprecated, Removed, Fixed, Security
- **Unreleased Section**: Maintain an `[Unreleased]` section at top for ongoing work
- **User-Focused Language**: Write for users, not developers (focus on impact, not implementation)
- **Link to Issues/PRs**: Reference GitHub issues and PRs for context (#123, @contributor)
- **Highlight Breaking Changes**: Make breaking changes obvious with BREAKING CHANGE markers
- **Migration Guidance**: For breaking changes, include or link to migration instructions
- **Release Dates**: Include dates for each version (YYYY-MM-DD format)
- **Comparison Links**: Add git comparison links at bottom for easy diffing between versions
- **Update on Every Release**: Keep CHANGELOG current - update before tagging new versions
- **Start Simple**: Begin with GitHub Releases auto-notes, formalize CHANGELOG as project matures
