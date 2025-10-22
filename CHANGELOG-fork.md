# Fork Changelog

This file tracks changes specific to this fork of Agent OS. For upstream Agent OS changes, see [CHANGELOG.md](./CHANGELOG.md).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

### Fixed

- **exclude_inherited_files bug for Claude Code Skills**: Fixed subshell variable scope bug in `get_profile_files()` function
  - **Issue**: When a profile used `exclude_inherited_files` to exclude certain standards (e.g., chezmoi profile excludes `standards/backend/*` and `standards/frontend/*`), the exclusion worked correctly for copying standards to `agent-os/standards/` but FAILED for creating Claude Code Skills in `.claude/skills/`. Backend and frontend skills were incorrectly created even though those standards were excluded.
  - **Root cause**: Line 427 in `scripts/common-functions.sh` used a pipe (`echo "$excluded_patterns" | while read pattern; do`) which created a subshell. When `excluded="true"` was set inside the subshell, that change didn't persist to the parent shell, so the exclusion check always failed.
  - **Fix**: Replaced pipe with process substitution (`while read pattern; do ... done <<< "$excluded_patterns"`) to keep the while loop in the current shell scope, allowing the `excluded` variable to persist correctly.
  - **Impact**: Profiles with exclusions now correctly exclude those standards from Skills generation. For example, chezmoi profile now creates only 30 relevant skills (configuration, documentation, global, scripts, templates, testing) instead of 30+ backend/frontend skills that don't apply.
  - Matches upstream issue: https://github.com/buildermethods/agent-os/discussions/240

### Added

- **Claude-code profile for v2.1.0**: New standalone profile for Claude Code extension development
  - Standalone profile focused on creating commands, agents, and skills for Claude Code
  - 10 standards files covering Claude Code development best practices
  - Single implementer agent specialized as Claude Code specialist
  - Standards organized into two categories:
    - **claude-code/**: command-design.md, agent-design.md, skill-design.md, prompt-engineering.md
    - **global/**: coding-style.md, conventions.md, frontmatter-conventions.md, tech-stack.md, documentation.md, validation.md
  - **New comprehensive standards**:
    - `skill-design.md`: Model-invoked capabilities, description best practices, conciseness principle, progressive disclosure, testing across models
    - `frontmatter-conventions.md`: YAML metadata standards for commands, agents, skills with templates and validation patterns
  - Based on Claude Code official documentation and real-world usage patterns from digest-ai project
  - Covers recently released Skills support with proper activation triggers and discovery patterns

- **Terraform-multicloud profile for v2.1.0**: Recreated standalone infrastructure profile
  - Standalone profile focused solely on infrastructure as code
  - 10 standards files covering Terraform, multi-cloud, testing, and CI/CD
  - Single implementer agent specialized for infrastructure engineering
  - Standards cover: naming conventions, environment strategy, state management, module design, provider patterns, security compliance, CI/CD integration, Terratest testing, and **resource imports**
  - New resource-imports.md standard provides comprehensive guidance on importing existing infrastructure while ensuring compliance with naming conventions, tagging, and security standards
  - Suitable for AWS, Azure, GCP, Cloudflare, and other cloud providers

- **Chezmoi profile for v2.1.0**: Recreated chezmoi profile with enhanced best practices
  - Inherits from default profile, excludes backend/frontend standards
  - 29 standards files covering chezmoi-specific development patterns
  - Single implementer agent specialized for dotfiles and system configuration
  - **Enhanced best practices**:
    - `.chezmoiscripts/` directory guidance for cleaner home directory structure
    - Docker-first testing strategy for safe development workflow
    - Multi-environment support patterns (bare-metal, VM, container)
    - Development safety rules to prevent accidental host system modification
    - Comprehensive integration testing documentation
  - All standards ported from active chezmakase project with refinements from real-world usage

---

## [2.1.0-fork] - 2025-10-21

### Upgraded to Upstream v2.1.0

This release syncs with upstream Agent OS v2.1.0. This was a major migration involving removal of fork-specific features that conflicted with upstream architectural changes (roles system removal, new 6-phase workflow, Claude Code Skills support).

### Removed

- **Roles system**: All `roles/implementers.yml` and `roles/verifiers.yml` files removed to align with upstream v2.1.0
- **Custom profiles**: navigator, chezmoi, terraform-multicloud, rails-fullstack, claude-code-tools profiles archived for recreation
  - Archived in `archive-v2.0.5-profiles` branch for reference
- **Agent templates**: Removed `agents/templates/` directories in favor of single `agents/implementer.md` per profile
- **`/bootstrap-role` command**: Removed as roles system is deprecated
- **`scripts/create-role.sh`**: Removed along with roles system
- **`role-generator` agent**: No longer needed

### Changed

- **`/bootstrap-profile` command**: Updated to generate current Agent OS structure
  - Supports Claude Code Skills
  - Creates single `agents/implementer.md` instead of role-based templates
  - Generates new command layout (shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks)
  - Made atemporal (no version references)
- **CLAUDE.md documentation**: Fully updated to reflect current Agent OS structure
  - Updated to 6-phase workflow
  - Added Claude Code Skills documentation
  - Removed all roles system references
  - Updated command references
  - Made all content atemporal

### Added

- **PROFILE_RECREATION_PROMPTS.md**: Detailed prompts for recreating archived profiles using `/bootstrap-profile`
  - Navigator profile (pause-for-review workflow)
  - Chezmoi profile (dotfiles management)
  - Terraform-multicloud profile (infrastructure as code)
  - Rails-fullstack profile (Ruby on Rails development)
  - Claude-code-tools profile (Agent OS meta-development)
- **Best Practices for Bootstrap Commands**: New CLAUDE.md section documenting principles for writing maintainable commands
  - Commands should be atemporal (no version history)
  - Commands should be self-sufficient
  - Version history belongs in CHANGELOG, not operational commands

### Preserved Fork Features

These fork-specific features were maintained through the upgrade:

- **`.claude/` directory**: Claude Code integration and settings
- **`/bootstrap-profile` command**: Intelligent profile creation (adapted for v2.1.0)
- **Non-interactive mode**: Installation scripts retain `--base-dir` flag and non-interactive modes
- **Profile inheritance**: Documented and supported (unchanged from upstream)

### Migration Path

Custom profiles can be recreated using `/bootstrap-profile` with the prompts provided in PROFILE_RECREATION_PROMPTS.md. The original v2.0.5 profiles are preserved in the `archive-v2.0.5-profiles` branch.

### Upstream Changes Included

This release includes all upstream v2.1.0 changes:
- Claude Code Skills support
- Configurable subagent delegation
- New configuration system (replaced multi-agent/single-agent modes)
- 6-phase workflow (plan-product, shape-spec, write-spec, create-tasks, implement-tasks, orchestrate-tasks)
- Simplified agent structure
- Improved project upgrade script

See [CHANGELOG.md](./CHANGELOG.md) for complete upstream v2.1.0 details.

---

## [2.0.5-fork] - 2025-10-17

### Added

- **Custom profiles**: Created 5 specialized profiles
  - `navigator`: Pause-for-review workflow enhancements
  - `chezmoi`: Dotfiles management with chezmoi
  - `terraform-multicloud`: Infrastructure as code across AWS/Azure/GCP
  - `rails-fullstack`: Ruby on Rails development
  - `claude-code-tools`: Agent OS meta-development
- **Bootstrap commands**: Intelligent profile and role creation
  - `/bootstrap-profile`: Analyzes projects and creates tailored profiles
  - `/bootstrap-role`: Creates roles with proper structure and validation
- **`.claude/` directory**: Claude Code integration
  - Settings and permissions
  - Bootstrap commands
  - Role generator agent
- **Enhanced installation scripts**:
  - `--base-dir` flag for flexible installation paths
  - Non-interactive mode with command-line flags
  - Better error handling and validation

### Changed

- **CLAUDE.md**: Added comprehensive fork documentation
  - Upstream contribution workflow
  - Cherry-pickable commits guidance
  - Bootstrap commands documentation
  - Profile and role creation guidance

This release was based on upstream Agent OS v2.0.5.
