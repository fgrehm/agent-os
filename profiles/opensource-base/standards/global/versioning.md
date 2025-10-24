## Versioning best practices for open source

- **Semantic Versioning**: Follow [semver](https://semver.org/) strictly - MAJOR.MINOR.PATCH (e.g., 2.3.1)
- **Version Meaning**: MAJOR = breaking changes, MINOR = new features (backward compatible), PATCH = bug fixes
- **Start with 0.x.x**: Use 0.x.x for initial development; 1.0.0 is your first stable, semver-committed release
- **Breaking Changes**: Always bump MAJOR version for any backward-incompatible changes, even tiny ones
- **Deprecation First**: Deprecate features in MINOR version with warnings, remove in next MAJOR version
- **Clear Communication**: Document breaking changes prominently in CHANGELOG with migration guides
- **Pre-release Versions**: Use alpha (0.1.0-alpha.1), beta (1.0.0-beta.1), rc (1.0.0-rc.1) for pre-releases
- **Git Tags**: Create git tags for every release with `v` prefix (v1.2.3), push tags to remote
- **Package Version Sync**: Keep package.json (or equivalent) version in sync with git tags
- **Version Pinning**: For dependencies, use `^` for minor updates (^1.2.3), `~` for patch only (~1.2.3)
- **Changelog Correlation**: Every version should have corresponding CHANGELOG entry
- **No Breaking Changes in Patches**: PATCH versions must only fix bugs, never change behavior or add features
- **Conventional Commits**: Use Conventional Commits to enable automated versioning and changelog generation
- **LTS Versions**: For mature projects, consider LTS versions for enterprise users (e.g., v4.x with extended support)
