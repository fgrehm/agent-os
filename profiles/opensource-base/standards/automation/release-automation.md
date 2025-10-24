## Release automation best practices for open source

- **Semantic Versioning**: Follow semver strictly (MAJOR.MINOR.PATCH) for predictable version numbers
- **Automated Version Bumping**: Use semantic-release or release-please to determine version from commit messages
- **Conventional Commits**: Adopt Conventional Commits format to enable automated versioning (feat:, fix:, BREAKING CHANGE:)
- **Automated Changelog**: Generate CHANGELOG.md automatically from commit messages
- **Git Tagging**: Automatically create git tags for releases (v1.2.3 format)
- **GitHub Releases**: Auto-create GitHub releases with notes generated from commits and PRs
- **Registry Publishing**: Automate publishing to npm, PyPI, crates.io, etc. on tag creation
- **Pre-release Channels**: Support alpha, beta, RC releases with npm dist-tags or equivalent
- **Release Notes**: Generate user-friendly release notes highlighting features, fixes, and breaking changes
- **Manual Fallback**: Maintain documented manual release process for when automation fails
- **Release Checklist**: For manual releases, use checklist (tests pass, CHANGELOG updated, version bumped, tagged)
- **Multi-Platform Publishing**: Coordinate releases across multiple package registries if applicable
- **Start Simple**: Manual releases are fine for low-frequency projects; automate when releasing monthly or more
