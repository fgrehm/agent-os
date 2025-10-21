# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Agent OS is a framework for spec-driven agentic development. It transforms AI coding agents into productive developers by providing structured workflows, coding standards, and role-based task delegation.

### ⚠️ This is a Fork

This repository is a fork of `buildermethods/agent-os`. The upstream project does NOT include:
- `.claude/` directory and Claude Code integration
- `/bootstrap-profile` command
- Enhanced installation scripts (`--base-dir`, non-interactive mode)

**Fork-specific changes**: See [CHANGELOG-fork.md](./CHANGELOG-fork.md) for fork history
**Upstream changes**: See [CHANGELOG.md](./CHANGELOG.md) for upstream Agent OS releases

**Workflow for upstream contributions**:
1. Develop changes normally in your fork (using Claude Code, bootstrap commands, etc.)
2. When ready to contribute upstream:
   ```bash
   git checkout -b upstream-contribution upstream/main
   git cherry-pick <commits>  # or manually apply changes
   # Remove/adapt any fork-specific features (.claude/, bootstrap commands, --base-dir flag)
   git push origin upstream-contribution
   ```
3. Create PR from `upstream-contribution` branch to `buildermethods/agent-os`

This way you can work with all fork features during development, then create a clean upstream-compatible branch when contributing back.

**Creating cherry-pickable commits**:

To make upstream contributions easier, structure commits so upstream-compatible changes are separate from fork-specific changes:

- **Upstream-ready commits**: Core improvements, bug fixes, new features that work with base Agent OS
  - Example: `feat(scripts): add --base-dir flag to installation scripts`
  - Can be cherry-picked directly to upstream branch

- **Fork-specific commits**: Changes to `.claude/`, CHANGELOG-fork.md, fork documentation
  - Example: `feat(bootstrap): add /bootstrap-profile command`
  - Kept in fork, not included in upstream PR

**Example workflow**:
```bash
# Make core improvement
git add scripts/*.sh
git commit -m "feat(scripts): add --base-dir flag to installation scripts"

# Separately commit fork-specific changes
git add .claude/commands/bootstrap-profile.md
git commit -m "feat(bootstrap): add /bootstrap-profile command"

# Later, cherry-pick only upstream-ready commits
git checkout -b upstream-contribution upstream/main
git cherry-pick <upstream-ready-commit-hash>
```

This approach keeps git history clean and makes it trivial to extract upstream contributions without manual file editing.

The fork-specific additions are maintained in this repository to enhance Agent OS with Claude Code support and specialized profiles for different use cases.

## Documentation Resources

### Official Docs

For detailed explanations of Agent OS concepts and workflows, reference these docs:

- **[Overview](https://buildermethods.com/agent-os)** - Core philosophy and spec-driven development approach
- **[Modes](https://buildermethods.com/agent-os/modes)** - Configuration options and flexibility
- **[Adaptability](https://buildermethods.com/agent-os/adaptability)** - How Agent OS works with different AI tools
- **[Installation](https://buildermethods.com/agent-os/installation)** - Base and project installation steps
- **[Workflow](https://buildermethods.com/agent-os/workflow)** - Six-phase development cycle overview
- **[3-Layer Context](https://buildermethods.com/agent-os/3-layer-context)** - Standards, Product, and Specs layers explained
- **[Standards](https://buildermethods.com/agent-os/standards)** - Creating and organizing coding standards
- **[Skills](https://buildermethods.com/agent-os/skills)** - Claude Code Skills integration
- **[Profiles](https://buildermethods.com/agent-os/profiles)** - Profile inheritance and customization
- **[Workflows](https://buildermethods.com/agent-os/workflows)** - Reusable instruction blocks and injection
- **[Verification](https://buildermethods.com/agent-os/verification)** - Quality assurance process
- **[Visuals](https://buildermethods.com/agent-os/visuals)** - Design-driven development with mockups

### Command Documentation

- **[/plan-product](https://buildermethods.com/agent-os/plan-product)** - Create mission, roadmap, tech-stack
- **[/shape-spec](https://buildermethods.com/agent-os/shape-spec)** - Shape rough ideas into requirements
- **[/write-spec](https://buildermethods.com/agent-os/write-spec)** - Write detailed specification
- **[/create-tasks](https://buildermethods.com/agent-os/create-tasks)** - Break spec into task list
- **[/implement-tasks](https://buildermethods.com/agent-os/implement-tasks)** - Simple implementation
- **[/orchestrate-tasks](https://buildermethods.com/agent-os/orchestrate-tasks)** - Advanced multi-agent orchestration

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

Agent OS uses a **3-layer context system** (Standards → Product → Specs), **profile inheritance** for different project types, and **Claude Code Skills** for context-efficient standards application. See [documentation links](#documentation-resources) above for detailed explanations.

### Workflow

**Phase 1** (run once):
- **`/plan-product`**: Create mission, roadmap, tech-stack

**Phases 2-6** (per feature, use what you need):
- **`/shape-spec`**: Shape rough ideas into clear requirements (optional if requirements known)
- **`/write-spec`**: Write detailed specification
- **`/create-tasks`**: Break spec into grouped, prioritized tasks
- **`/implement-tasks`**: Simple implementation for smaller features
- **`/orchestrate-tasks`**: Advanced orchestration for complex features with specialized subagents

### Directory Structure

```
agent-os/
├── profiles/              # Profile configurations
│   └── default/          # Base profile for web apps
├── scripts/              # Installation and management scripts
│   ├── base-install.sh   # Installs Agent OS to ~/agent-os
│   ├── project-install.sh # Compiles profile into project
│   ├── project-update.sh  # Updates existing installation
│   └── create-profile.sh  # Creates new profiles
├── .claude/              # Fork-specific Claude Code integration
│   ├── commands/         # Bootstrap commands
│   └── settings.json     # Permissions and preferences
└── CLAUDE.md             # This file
```

### Profile Structure

Each profile contains:

```
profiles/[profile-name]/
├── profile-config.yml          # Inheritance and exclusion config
├── claude-code-skill-template.md  # Template for Skills descriptions
├── agents/                     # Agent definitions
│   └── implementer.md         # Generic implementer agent
├── commands/                   # Slash commands (can override inherited)
│   ├── plan-product/
│   ├── shape-spec/
│   ├── write-spec/
│   ├── create-tasks/
│   ├── implement-tasks/
│   ├── orchestrate-tasks/
│   └── improve-skills/
├── standards/                  # Coding standards and conventions
│   ├── global/                # Cross-cutting standards
│   ├── backend/               # Backend-specific standards
│   ├── frontend/              # Frontend-specific standards
│   └── testing/               # Testing standards
└── workflows/                  # Reusable instruction blocks
    ├── planning/              # Product planning workflows
    ├── specification/         # Spec creation workflows
    └── implementation/        # Implementation workflows
```

### Profile Inheritance

Profiles can inherit from other profiles using `profile-config.yml`:
- Child profiles override parent files with same path
- Can exclude specific inherited files via `exclude_inherited_files`
- Allows building specialized profiles while reusing common configurations

### How It Works

When `project-install.sh` runs:
1. Resolves profile inheritance chain and merges files (child overrides parent)
2. Processes injection tags: `{{workflows/...}}` (expands full content) and `{{standards/...}}` (creates reference lists)
3. Creates `agent-os/` folder in project
4. If Claude Code is enabled, installs commands to `.claude/commands/agent-os/`
5. If Skills mode enabled, converts standards to `.claude/skills/` (then run `/improve-skills`)

See [Profiles](https://buildermethods.com/agent-os/profiles), [Workflows](https://buildermethods.com/agent-os/workflows), and [Skills](https://buildermethods.com/agent-os/skills) docs for details.

## Bootstrap Commands

This fork includes Claude Code commands to help create new profiles intelligently:

### `/bootstrap-profile`

Creates a new Agent OS profile tailored to a specific project type or tech stack. This command:
- Analyzes project characteristics and tech stack (if provided)
- Suggests appropriate standards and workflows
- Guides through inheritance and customization decisions
- Generates complete profile structure

**When to use:** Creating profiles for Rails APIs, Python data science, mobile apps, infrastructure (Terraform), or any specialized project type.

### Best Practices for Bootstrap Commands

When writing or updating bootstrap commands (or any `.claude/commands/` files):

**Make commands atemporal and self-sufficient:**
- ❌ Don't reference specific versions (v2.0, v2.1.0, etc.)
- ❌ Don't include "What changed from X to Y" sections
- ✅ Document the current structure as if it's always been this way
- ✅ Make the command work standalone without external context

**Rationale:** Commands should describe "what to do now" not "how we got here". Version history belongs in CHANGELOG.md and git history, not in operational commands.

**Example:**
```markdown
# Bad
## Notes for v2.1.0
- Roles system removed in v2.1.0
- New 6-phase workflow introduced

# Good
## Additional Notes
- For specialized subagents, create them manually using Claude Code's subagent feature
- Profiles can inherit from other profiles for configuration reuse
```

This keeps commands maintainable and prevents them from becoming historical documentation that confuses users about current capabilities.

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

Or use the intelligent bootstrap command:
```bash
/bootstrap-profile
```

The bootstrap command analyzes your project and suggests appropriate standards, workflows, and configurations.

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
- `profiles/[name]/claude-code-skill-template.md`: Template for Skills descriptions
- `profiles/[name]/agents/implementer.md`: Generic implementer agent for the profile

### Commands

Commands can be provided in multiple formats:
- `commands/[name]/multi-agent/`: Delegates to Claude Code subagents
- `commands/[name]/single-agent/`: Sequential prompts for step-by-step execution
- `commands/[name]/[command-name].md`: Single-file command (simpler commands)

Profiles can override inherited commands by providing files with the same path.

## Important Conventions

### File Naming in Specs

When implementing features, agents create:
- `agent-os/specs/[date]-[feature]/planning/requirements.md`: Requirements and research
- `agent-os/specs/[date]-[feature]/spec.md`: Feature specification
- `agent-os/specs/[date]-[feature]/tasks.md`: Task breakdown with checkboxes
- `agent-os/specs/[date]-[feature]/orchestration.yml`: Task group assignments (for orchestrate-tasks)

### Standards Organization

Standards should be:
- **Modular**: One concern per file
- **Specific**: Concrete rules, not vague advice
- **Example-driven**: Show how to follow the standard
- **Minimal**: Start small, expand based on patterns you correct frequently
- **Discoverable** (for Skills): Clear descriptions with relevant keywords
