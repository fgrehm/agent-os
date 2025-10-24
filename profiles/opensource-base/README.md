# Open Source Base Profile

A comprehensive Agent OS profile for open source project management, focused on repository health, community engagement, automation, and security best practices.

## Purpose

This profile provides standards and workflows for building welcoming, maintainable open source projects. It's tech-stack agnostic and covers the universal concerns of OSS projects: documentation, community building, CI/CD, and security.

## Who Should Use This Profile?

- **New OSS projects**: Getting started with solid foundations
- **Existing projects**: Improving repository health and community practices
- **Maintainers**: Establishing consistent standards across projects
- **Organizations**: Standardizing OSS practices across multiple repos

This profile is ideal as a standalone for pure OSS projects or as a parent profile to inherit from for tech-specific profiles.

## Adaptability & Flexibility

**Not every project needs everything!** This profile provides comprehensive standards, but you should adapt them based on your project's:
- **Maturity**: Side project vs. established community project
- **Backing**: Personal vs. company-backed vs. foundation-supported
- **Scale**: Solo maintainer vs. large community
- **Goals**: Experimentation vs. production-critical infrastructure

See **[Project Maturity Guide](standards/global/project-maturity-guide.md)** for guidance on what to prioritize at each stage. Start minimal and add structure as your project grows.

## What's Included

### Repository Documentation Standards

#### README Best Practices (standards/repository/readme.md)

- Structure and essential sections
- Installation and quick start guides
- Example-driven documentation
- Badge usage and project status

#### Contributing Guidelines (standards/repository/contributing.md)

- Welcome messages and Code of Conduct
- Development setup instructions
- Coding standards and commit guidelines
- PR process and testing requirements

#### Changelog Standards (standards/repository/changelog.md)

- Keep a Changelog format
- Semantic versioning integration
- Breaking change communication
- Migration guides

### Community Workflow Standards

#### Code of Conduct (standards/community/code-of-conduct.md)

- Contributor Covenant implementation
- Enforcement guidelines
- Scope definition and reporting channels
- Inclusive language practices

#### Issue Templates (standards/community/issue-templates.md)

- Bug report templates
- Feature request templates
- Question templates
- Security issue handling

#### PR Templates (standards/community/pr-templates.md)

- Standard PR description structure
- Type-specific templates (bug fix, feature, docs)
- Review checklists
- Testing and documentation requirements

### CI/CD Automation Standards

#### GitHub Actions (standards/automation/github-actions.md)

- Test, lint, and build workflows
- Matrix testing strategies
- Caching and optimization
- Security best practices

#### Release Automation (standards/automation/release-automation.md)

- Semantic release configuration
- Release-please workflows
- Pre-release strategies
- Multi-platform publishing

### Security & Compliance Standards

#### Security Policy (standards/security/security-policy.md)

- SECURITY.md template
- Vulnerability reporting process
- Security advisory publishing
- Supported versions and disclosure timeline

#### Dependency Management (standards/security/dependency-management.md)

- Dependency audit processes
- Dependabot/Renovate configuration
- Bundle size optimization
- License compliance checking

### Global Standards

#### Licensing (standards/global/licensing.md)

- License selection guide
- MIT, Apache, GPL comparison
- License compatibility matrix
- CLA/DCO implementation

#### Versioning (standards/global/versioning.md)

- Semantic versioning (semver)
- Breaking change communication
- Deprecation strategies
- Pre-release versioning

## Profile Configuration

**Inherits From:** None (standalone profile)

This profile is designed as a foundational, tech-stack-agnostic profile. It can be:

- Used standalone for OSS projects
- Inherited by tech-specific profiles (Rails, Python, etc.)
- Mixed with other profiles via Agent OS inheritance

## Standards Organization

```
profiles/opensource-base/
├── standards/
│   ├── global/              # Cross-cutting standards
│   │   ├── licensing.md
│   │   └── versioning.md
│   ├── repository/          # Documentation standards
│   │   ├── readme.md
│   │   ├── contributing.md
│   │   └── changelog.md
│   ├── community/           # Community engagement
│   │   ├── code-of-conduct.md
│   │   ├── issue-templates.md
│   │   └── pr-templates.md
│   ├── automation/          # CI/CD practices
│   │   ├── github-actions.md
│   │   └── release-automation.md
│   └── security/            # Security practices
│       ├── security-policy.md
│       └── dependency-management.md
└── workflows/
    └── implementation/
        └── implement-tasks.md
```

## Installation

### Basic Installation

```bash
~/agent-os/scripts/project-install.sh --profile opensource-base
```

This creates an `agent-os/` directory in your project with compiled standards.

### With Claude Code Skills

For a more context-efficient experience, use Skills mode:

```bash
# In your project's config.yml:
# standards_as_claude_code_skills: true

~/agent-os/scripts/project-install.sh --profile opensource-base

# Then optimize skills:
cd your-project
/improve-skills
```

Skills mode converts standards into Claude Code skills that are invoked on-demand rather than injected into every prompt.

### As a Parent Profile

To extend this profile for a tech-specific project:

**profiles/rails-oss/profile-config.yml:**

```yaml
inherits_from: opensource-base
# Rails-oss inherits all OSS standards
# Add tech-specific standards in this profile
```

## Agent Configuration

This profile includes a generic implementer agent (`agents/implementer.md`) specialized for OSS projects. The agent:

- Understands OSS best practices
- Ensures documentation stays current
- Follows community guidelines
- Implements security-conscious code
- Maintains licensing compliance

## Usage Examples

### Starting a New OSS Project

```bash
# 1. Install Agent OS base
~/agent-os/scripts/base-install.sh

# 2. Create your project
mkdir my-oss-project && cd my-oss-project
git init

# 3. Install opensource-base profile
~/agent-os/scripts/project-install.sh --profile opensource-base

# 4. Your project now has:
# - agent-os/standards/ with all OSS standards
# - .claude/commands/agent-os/ with Agent OS commands
```

### Improving an Existing Project

```bash
cd existing-project

# Install profile
~/agent-os/scripts/project-install.sh --profile opensource-base

# Review standards in agent-os/standards/
# Implement missing components (README, CONTRIBUTING, etc.)
```

### Creating a Specialized Profile

```bash
cd ~/agent-os

# Create new profile inheriting from opensource-base
./scripts/create-profile.sh

# Or use bootstrap command:
/bootstrap-profile
```

## Standards Not Included

This profile intentionally doesn't include:

- **Tech-stack specific**: Language, framework, or tooling conventions
- **Code architecture**: MVC, microservices, etc.
- **API design**: REST, GraphQL conventions
- **Testing strategies**: Unit/integration test patterns (tech-specific)
- **Deployment**: Hosting, infrastructure decisions

These belong in tech-specific profiles that inherit from this base.

## Customization

### Override Specific Standards

Create a child profile and override files:

```
profiles/my-custom-oss/
├── profile-config.yml        # inherits_from: opensource-base
├── standards/
│   └── repository/
│       └── readme.md         # Override with custom README standards
```

### Exclude Standards

If you don't need certain standards:

**profile-config.yml:**

```yaml
inherits_from: opensource-base

exclude_inherited_files:
  - standards/automation/* # If not using CI/CD
  - standards/security/dependency-management.md # If not applicable
```

### Add Tech-Specific Standards

```
profiles/python-oss/
├── profile-config.yml        # inherits_from: opensource-base
├── standards/
│   ├── backend/
│   │   ├── python-style.md   # Python-specific standards
│   │   └── testing.md
│   └── frontend/
│       └── pypi-publishing.md
```

## Best Practices for Using This Profile

1. **Start with documentation**: Implement README, CONTRIBUTING, LICENSE first
2. **Set up automation early**: CI/CD, dependency updates, security scanning
3. **Be welcoming**: Focus on contributor experience from day one
4. **Iterate on standards**: Start minimal, expand as patterns emerge
5. **Use Skills mode**: For large projects, reduces context overhead
6. **Keep current**: Update standards as project evolves
7. **Community first**: Prioritize clear communication and welcoming tone

## Common Workflows

### Adding a New Feature

```bash
# 1. Create feature branch
git checkout -b feature/awesome-feature

# 2. Implement following standards
# Reference: agent-os/standards/*

# 3. Update documentation
# - README if user-facing
# - CHANGELOG under [Unreleased]

# 4. Create PR using template
# .github/PULL_REQUEST_TEMPLATE/feature.md

# 5. Pass CI checks
# - Tests
# - Linting
# - Security audit

# 6. Merge and release
# Automated release will publish
```

### Releasing a New Version

```bash
# With semantic-release (recommended):
# Just merge to main with conventional commits
git commit -m "feat: add awesome feature"
git push origin main

# Automated release workflow:
# 1. Analyzes commits
# 2. Determines version
# 3. Updates CHANGELOG
# 4. Creates GitHub release
# 5. Publishes to npm/PyPI/etc.
```

### Handling a Security Report

```bash
# 1. Acknowledge report (within 48 hours)
# 2. Create private security advisory
# 3. Develop fix in private fork
# 4. Coordinate disclosure with reporter
# 5. Release patched version
# 6. Publish security advisory
# 7. Notify community
```

## Example Projects Using This Profile

- _Add links to example projects here_

## Contributing to This Profile

Found a bug or want to improve the standards?

1. Check existing issues in this fork's repository
2. Open a discussion for major changes
3. Submit PRs for improvements
4. Share feedback in discussions

## Philosophy

This profile embodies these OSS principles:

- **Welcoming**: Lower barriers to contribution
- **Transparent**: Clear processes and communication
- **Secure**: Proactive security practices
- **Sustainable**: Automation reduces maintainer burden
- **Community-driven**: Users and contributors come first
- **Quality-focused**: Standards that improve project health

## Resources

### Agent OS Documentation

- [Agent OS Overview](https://buildermethods.com/agent-os)
- [Profiles Guide](https://buildermethods.com/agent-os/profiles)
- [Workflows Guide](https://buildermethods.com/agent-os/workflows)
- [Skills Mode](https://buildermethods.com/agent-os/skills)

### External OSS Resources

- [Open Source Guides](https://opensource.guide/)
- [Contributor Covenant](https://www.contributor-covenant.org/)
- [Semantic Versioning](https://semver.org/)
- [Keep a Changelog](https://keepachangelog.com/)
- [Choose a License](https://choosealicense.com/)

## Support

Questions or issues?

- Open an issue in this fork's repository
- Start a discussion for questions and ideas
- Refer to upstream Agent OS for general Agent OS questions

## License

This profile is part of Agent OS and follows the same license as the parent project.

## Changelog

See [Agent OS CHANGELOG](../../CHANGELOG.md) for version history.
