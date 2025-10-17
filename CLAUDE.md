# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Agent OS is a framework for spec-driven agentic development. It transforms AI coding agents into productive developers by providing structured workflows, coding standards, and role-based task delegation.

### ⚠️ This is a Fork

This repository is a fork of `buildermethods/agent-os`. The upstream project does NOT include:
- `.claude/` directory and Claude Code integration
- `navigator` profile (pause-for-review workflow)
- `chezmoi` profile (dotfiles management)

**Workflow for upstream contributions**:
1. Develop changes normally in your fork (using Claude Code, navigator profile, etc.)
2. When ready to contribute upstream:
   ```bash
   git checkout -b upstream-contribution upstream/main
   git cherry-pick <commits>  # or manually apply changes
   # Remove/adapt any fork-specific features (.claude/, navigator/chezmoi references)
   git push origin upstream-contribution
   ```
3. Create PR from `upstream-contribution` branch to `buildermethods/agent-os`

This way you can work with all fork features during development, then create a clean upstream-compatible branch when contributing back.

The fork-specific additions are maintained in this repository to enhance Agent OS with Claude Code support and specialized profiles for different use cases.

## Documentation Resources

### Official Docs

For detailed explanations of Agent OS concepts and workflows, reference these docs:

- **[Overview](https://buildermethods.com/agent-os)** - Core philosophy and spec-driven development approach
- **[Modes](https://buildermethods.com/agent-os/modes)** - Multi-agent vs single-agent mode comparison
- **[Adaptability](https://buildermethods.com/agent-os/adaptability)** - How Agent OS works with different AI tools
- **[Installation](https://buildermethods.com/agent-os/installation)** - Base and project installation steps
- **[Workflow](https://buildermethods.com/agent-os/workflow)** - Four-phase development cycle overview
- **[3-Layer Context](https://buildermethods.com/agent-os/3-layer-context)** - Standards, Product, and Specs layers explained
- **[Standards](https://buildermethods.com/agent-os/standards)** - Creating and organizing coding standards
- **[Profiles](https://buildermethods.com/agent-os/profiles)** - Profile inheritance and customization
- **[Roles](https://buildermethods.com/agent-os/roles)** - Implementers and verifiers system
- **[Workflows](https://buildermethods.com/agent-os/workflows)** - Reusable instruction blocks and injection
- **[Verification](https://buildermethods.com/agent-os/verification)** - Quality assurance process
- **[Visuals](https://buildermethods.com/agent-os/visuals)** - Design-driven development with mockups

### Command Documentation

- **[/plan-product](https://buildermethods.com/agent-os/plan-product)** - Create mission, roadmap, tech-stack
- **[/new-spec](https://buildermethods.com/agent-os/new-spec)** - Research and gather feature requirements
- **[/create-spec](https://buildermethods.com/agent-os/create-spec)** - Write detailed spec and task breakdown
- **[/implement-spec](https://buildermethods.com/agent-os/implement-spec)** - Execute implementation with agents

### Searching Upstream Community

To research community patterns, profile examples, and customization approaches:

**GitHub Search Patterns:**
- Issues: `https://github.com/search?type=issues&q=repo:buildermethods/agent-os+KEYWORD`
- PRs: `https://github.com/search?type=pullrequests&q=repo:buildermethods/agent-os+KEYWORD`
- Discussions: `https://github.com/search?type=discussions&q=repo:buildermethods/agent-os+KEYWORD`

**Useful Keywords:**
- `profile`, `role`, `standards`, `custom`, `bootstrap`, `example`, `mixin`
- Tech-specific: `rails`, `python`, `nextjs`, `wordpress`, etc.

**Direct Links:**
- [All Discussions](https://github.com/buildermethods/agent-os/discussions)
- [Merged PRs](https://github.com/buildermethods/agent-os/pulls?q=is%3Apr+is%3Amerged)
- [Open Issues](https://github.com/buildermethods/agent-os/issues)

## Quick Reference

### Core Concepts

Agent OS uses a **3-layer context system** (Standards → Product → Specs), **profile inheritance** for different project types, and supports both **multi-agent** (parallel subagents) and **single-agent** (sequential prompts) modes. See [documentation links](#documentation-resources) above for detailed explanations.

### Workflow

1. **`/plan-product`** (once): Create mission, roadmap, tech-stack
2. **`/new-spec`** (per feature): Research and gather requirements
3. **`/create-spec`** (per feature): Write spec and task list
4. **`/implement-spec`** (per feature): Implement and verify

### Directory Structure

```
agent-os/
├── profiles/              # Profile configurations
│   ├── default/          # Base profile for web apps
│   ├── navigator/        # Adds pause-for-review workflow
│   └── chezmoi/          # Specialized for dotfiles management
├── scripts/              # Installation and management scripts
│   ├── base-install.sh   # Installs Agent OS to ~/agent-os
│   ├── project-install.sh # Compiles profile into project
│   ├── project-update.sh  # Updates existing installation
│   ├── create-profile.sh  # Creates new profiles
│   └── create-role.sh     # Creates new roles
└── CLAUDE.md             # This file
```

### Profile Structure

Each profile contains:

```
profiles/[profile-name]/
├── profile-config.yml     # Inheritance and exclusion config
├── agents/                # Agent definitions and templates
│   └── templates/         # Reusable agent templates
├── commands/              # Slash commands (/plan-product, /new-spec, etc.)
│   ├── [command]/
│   │   ├── multi-agent/  # Multi-agent orchestration
│   │   └── single-agent/ # Sequential single-agent prompts
├── roles/                 # Role definitions
│   ├── implementers.yml  # Specialized implementer agents
│   └── verifiers.yml     # Verification agents
├── standards/             # Coding standards and conventions
│   ├── global/           # Cross-cutting standards
│   ├── backend/          # Backend-specific standards
│   ├── frontend/         # Frontend-specific standards
│   └── testing/          # Testing standards
└── workflows/             # Reusable instruction blocks
    ├── planning/         # Product planning workflows
    ├── specification/    # Spec creation workflows
    └── implementation/   # Implementation workflows
```

### Profile Inheritance Chain

```
default (base web app profile)
  ↓
navigator (adds pause-for-review workflow)
  ↓
chezmoi (specialized for dotfiles)
```

### How It Works

When `project-install.sh` runs:
1. Resolves profile inheritance chain and merges files (child overrides parent)
2. Processes injection tags: `{{workflows/...}}` (expands full content) and `{{standards/...}}` (creates reference lists)
3. Creates `agent-os/` folder in project and `.claude/agents/` in multi-agent mode

See [Profiles](https://buildermethods.com/agent-os/profiles), [Workflows](https://buildermethods.com/agent-os/workflows), and [Roles](https://buildermethods.com/agent-os/roles) docs for details.

## Bootstrap Commands

This fork includes Claude Code commands to help create new profiles and roles intelligently:

### `/bootstrap-profile`

Creates a new Agent OS profile tailored to a specific project type or tech stack. This command:
- Analyzes project characteristics and tech stack (if provided)
- Suggests appropriate standards, roles, and workflows
- Guides through inheritance and customization decisions
- Generates complete profile structure

**When to use:** Creating profiles for Rails APIs, Python data science, mobile apps, or any specialized project type.

### `/bootstrap-role`

Creates a new role (implementer or verifier) based on project needs. This command:
- Identifies gaps in current role coverage
- Defines clear responsibilities and boundaries
- Configures tools, standards, and verification
- Generates YAML role definition

**When to use:** Adding specialized roles like "GraphQL engineer", "ML pipeline engineer", "accessibility expert", or "security verifier".

These commands wrap the bash scripts (`create-profile.sh`, `create-role.sh`) with intelligent analysis and suggestions.

## Common Development Tasks

### Testing Installation Scripts

Run scripts in dry-run mode or in Docker containers to avoid modifying the host system:

```bash
# Dry run to see what would happen
scripts/project-install.sh --dry-run

# Test in Docker
docker run -it --rm -v $(pwd):/agent-os ubuntu:latest bash
cd /agent-os
bash scripts/base-install.sh
```

### Creating a New Profile

```bash
scripts/create-profile.sh
```

Follow prompts to specify:
- Profile name
- Parent profile to inherit from
- Which inherited files to exclude

### Creating a New Role

```bash
scripts/create-role.sh
```

Define the role's expertise, responsibilities, standards, and verifier assignments.

### Validating Shell Scripts

Use shellcheck on all shell scripts:

```bash
shellcheck scripts/*.sh
```

### Testing Profile Inheritance

Check how profiles resolve:

```bash
# View inheritance chain
grep -r "inherits_from:" profiles/*/profile-config.yml

# See what a profile excludes
grep -A 20 "exclude_inherited_files:" profiles/*/profile-config.yml
```

## Key Files to Understand

### Profile Configuration

- `profiles/[name]/profile-config.yml`: Controls inheritance and file exclusions
- `profiles/[name]/roles/implementers.yml`: Defines implementer roles with their responsibilities and standards
- `profiles/[name]/roles/verifiers.yml`: Defines verifier roles

### Commands

Commands are organized by mode:
- `commands/[name]/multi-agent/`: Orchestrates subagents
- `commands/[name]/single-agent/`: Sequential prompt files

### Agent Templates

- `agents/templates/implementer.md`: Template for implementer agents
- `agents/templates/verifier.md`: Template for verifier agents

These templates get compiled with role-specific data from `roles/*.yml`.

## Important Conventions

### File Naming in Specs

When implementing features, agents create:
- `agent-os/specs/[date]-[feature]/spec.md`: Feature specification
- `agent-os/specs/[date]-[feature]/tasks.md`: Task breakdown with checkboxes
- `agent-os/specs/[date]-[feature]/implementation/[N]-[name].md`: Implementation reports
- `agent-os/specs/[date]-[feature]/verification/[role]-verification.md`: Verification reports

### Standards Organization

Standards should be:
- **Modular**: One concern per file
- **Specific**: Concrete rules, not vague advice
- **Example-driven**: Show how to follow the standard
- **Minimal**: Start small, expand based on patterns you correct frequently

### Role Responsibilities

Keep implementer responsibilities focused:
- Clear areas of responsibility
- Clear areas OUTSIDE of responsibility (prevents overlap)
- Appropriate standards for the domain
- Matched with appropriate verifiers
