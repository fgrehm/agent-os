# Fork Features

This is [@fgrehm](https://github.com/fgrehm)'s fork of [buildermethods/agent-os](https://github.com/buildermethods/agent-os) with additional features:

- **Claude Code integration** - `.claude/settings.json` and project configuration
- **Navigator profile** - Pause-for-review workflow with checkpoints after each phase
- **Chezmoi profile** - Specialized roles and standards for dotfiles management
- **Bootstrap commands** - Intelligent `/bootstrap-profile` and `/bootstrap-role` commands with project analysis
- **Role generator agent** - Specialized subagent for creating framework-specific role definitions

See [CLAUDE.md](CLAUDE.md) for detailed documentation on fork-specific features and upstream contribution workflow.

## Intelligent Profile & Role Creation

This fork includes smart bootstrap commands that analyze your project and generate tailored configurations:

### `/bootstrap-profile` - Create new profiles with project analysis

- Detects tech stack (Rails, NextJS, Python, etc.)
- Identifies architectural patterns
- Auto-generates role definitions
- Suggests appropriate standards

### `/bootstrap-role` - Create specialized roles interactively

- Identifies gaps in current role coverage
- Defines clear boundaries
- Configures tools and standards
- Generates production-ready YAML

### `role-generator` agent - Specialized subagent

- Generates framework-specific role definitions
- Uses proper terminology (ActiveRecord, Prisma, etc.)
- Writes properly structured YAML files
- Validates and reports success

These tools make it easy to create custom profiles for different tech stacks (Rails APIs, Python data science, mobile apps, etc.) without manually writing YAML. The commands handle directory creation, file structure, and framework-specific customization intelligently.

## Additional Profiles

### Navigator Profile

Adds a pause-for-review workflow that stops after each phase for user review before proceeding. Perfect for:
- Learning how Agent OS works
- Reviewing generated content before proceeding
- Maintaining tighter control over the development process

### Chezmoi Profile

Specialized profile for managing dotfiles with chezmoi:
- Excludes backend/frontend standards (not applicable)
- Includes dotfiles-specific standards and conventions
- Roles tailored for configuration management
- Inherits from navigator for review checkpoints

## Upstream Contributions

This fork aims to contribute useful features back to the main project. See [TODO.md](TODO.md) for planned upstream contributions, including non-interactive flags for `create-profile.sh` and `create-role.sh` scripts.
