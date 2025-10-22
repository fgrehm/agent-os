# Fork Changelog

This file tracks changes specific to this fork of Agent OS. For upstream Agent OS changes, see [CHANGELOG.md](./CHANGELOG.md).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

### Added

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
