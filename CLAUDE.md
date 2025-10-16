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

## Architecture

### Core Concepts

**3-Layer Context System:**
1. **Standards** (Layer 1): Define HOW to build - coding conventions, patterns, best practices
2. **Product** (Layer 2): Define WHAT and WHY - mission, roadmap, tech stack
3. **Specs** (Layer 3): Define WHAT NEXT - specific feature requirements and tasks

**Profile System:**
- Profiles are complete configuration packages containing standards, roles, workflows, agents, and commands
- Profiles support inheritance via `profile-config.yml` (e.g., `chezmoi` inherits from `navigator` which inherits from `default`)
- Each profile can override parent files or add new ones
- Profiles enable different tech stacks, project types, and team conventions

**Dual Mode Support:**
- **Multi-agent mode**: Commands delegate to specialized subagents that work in parallel
- **Single-agent mode**: Commands compile into sequential prompts executed one at a time

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

### How Compilation Works

When `project-install.sh` runs, it:
1. Resolves the profile inheritance chain (e.g., `chezmoi` → `navigator` → `default`)
2. Merges files from all profiles (child overrides parent)
3. Processes injection tags like `{{standards/global/*}}` and `{{workflows/implementation/implement-task}}`
4. Compiles standards into role definitions
5. Creates `agent-os/` folder in project (with commands, standards, roles, workflows)
6. Creates `.claude/agents/` folder in multi-agent mode with role-specific agent files

### Injection System

**Workflow injection** (full content):
```markdown
{{workflows/implementation/implement-task}}
```
Expands to full content of the workflow file.

**Standards injection** (reference list):
```yaml
standards:
  - global/*
  - backend/*
```
Compiles into reference list like: `@agent-os/standards/global/coding-style.md`, `@agent-os/standards/backend/api.md`, etc.

### Roles System

**Implementers** are specialists:
- `database-engineer`: Migrations, models, schemas
- `api-engineer`: Endpoints, controllers, business logic
- `ui-designer`: Components, views, styling
- `testing-engineer`: Test coverage

**Verifiers** review implementations:
- `backend-verifier`: Backend code quality
- `frontend-verifier`: Frontend code quality
- `integration-verifier`: Full system integration

Each role in `roles/implementers.yml` or `roles/verifiers.yml` defines:
- Areas of responsibility
- Standards to follow
- Which verifier reviews their work

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

## Profile Inheritance Chain

```
default (base web app profile)
  ↓
navigator (adds pause-for-review workflow)
  ↓
chezmoi (specialized for dotfiles)
```

- **default**: Complete profile for web applications with backend/frontend standards
- **navigator**: Inherits default, overrides commands/agents to add checkpoint pauses
- **chezmoi**: Inherits navigator, excludes backend/frontend standards, adds dotfiles-specific standards

## Spec-Driven Development Workflow

Projects using Agent OS follow this workflow:

1. **`/plan-product`** (once): Create mission, roadmap, tech-stack
2. **`/new-spec`** (per feature): Research and gather requirements
3. **`/create-spec`** (per feature): Write spec and task list
4. **`/implement-spec`** (per feature): Implement and verify

Each command delegates to appropriate agents based on the active profile's role definitions.

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
