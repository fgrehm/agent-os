# Profile Recreation Prompts

These prompts can be used with `/bootstrap-profile` to recreate the profiles that were archived during the v2.1.0 migration.

---

## Navigator Profile

**Purpose:** Adds pause-for-review workflow enhancements throughout the spec-driven development process.

**Prompt for `/bootstrap-profile`:**

```
Create a "navigator" profile that inherits from "default" and adds pause-for-review workflow enhancements.

Purpose: Like a navigator guiding a pilot, this profile provides checkpoints throughout the spec-driven development workflow where the AI pauses for review and approval before proceeding.

Key Features:
- Pause after each task group during orchestration for review/commit
- Pause after completing each workflow phase (shape-spec, write-spec, create-tasks)
- Clear commit strategy guidance at each checkpoint
- Prevent massive uncommittable changesets

Commands to Override:
- orchestrate-tasks: Add pause/review checkpoints after each task group
- shape-spec, write-spec, create-tasks: Add completion checkpoints

Standards to Add (global/):
- agent-workflow.md: Document the pause-for-review pattern and commit strategies

Workflows to Add (implementation/):
- implementer-responsibilities.md: Include instructions for pausing and seeking approval
```

---

## Chezmoi Profile

**Purpose:** Specialized profile for dotfiles management using chezmoi.

**Prompt for `/bootstrap-profile`:**

```
Create a "chezmoi" profile for dotfiles management.

Tech Stack:
- chezmoi (dotfiles manager)
- Shell scripts (bash, zsh)
- Configuration files (YAML, JSON, TOML)
- Template language (chezmoi templates with Go text/template)

Standards to Create:

configuration/:
- chezmoi-structure.md: Directory layout, file organization
- config-files.md: Configuration file patterns
- dotfiles.md: Dotfile naming and organization
- essential-files.md: Critical files that must exist
- external-dependencies.md: Managing external tools and dependencies
- permissions.md: File permissions and security
- secrets-management.md: Handling secrets and sensitive data

scripts/:
- cross-platform.md: Writing portable shell scripts
- error-handling.md: Script error handling patterns
- run-scripts.md: chezmoi run_once and run_always scripts
- safety.md: Safe script practices
- shell-scripting.md: Shell script conventions
- template-validation.md: Validating templates before apply

templates/:
- chezmoi-templates.md: Template syntax and patterns
- conditional-logic.md: Platform and hostname conditionals
- data-management.md: Using .chezmoidata files
- file-selection.md: Template vs copy vs symlink decisions
- template-safety.md: Safe template practices

testing/:
- integration-tests.md: Testing dotfiles installation
- test-coverage.md: What to test in dotfiles
- test-writing.md: Testing approach for shell scripts

Agent Customization:
The implementer agent should be a "dotfiles and system configuration engineer" with expertise in shell scripting, configuration management, and cross-platform compatibility.
```

---

## Terraform Multi-Cloud Profile

**Purpose:** Infrastructure as code across AWS, Azure, and GCP using Terraform.

**Prompt for `/bootstrap-profile`:**

```
Create a "terraform-multicloud" profile for infrastructure as code.

Tech Stack:
- Terraform
- AWS, Azure, GCP (multi-cloud)
- Terratest (testing)
- CI/CD integration

Standards to Create:

global/:
- naming-conventions.md: Resource naming across clouds
- environment-strategy.md: Dev/staging/prod patterns
- security-compliance.md: Security best practices
- state-management.md: Remote state, locking, versioning
- communication-style.md: How to communicate infrastructure changes

infrastructure/:
- module-design.md: Terraform module structure and patterns
- provider-patterns.md: Multi-cloud provider configurations
- cicd-integration.md: CI/CD pipeline integration

testing/:
- terraform-testing.md: Terratest patterns, validation, plan testing

Agent Customization:
The implementer agent should be an "infrastructure engineer" with deep expertise in Terraform, multi-cloud architecture, and infrastructure best practices.

Additional Notes:
- Exclude frontend/ standards (not needed for infrastructure)
- Focus on cloud-agnostic patterns where possible
- Include cost optimization guidance
```

---

## Rails Fullstack Profile

**Purpose:** Ruby on Rails fullstack development.

**Prompt for `/bootstrap-profile`:**

```
Create a "rails-fullstack" profile for Ruby on Rails development.

Tech Stack:
- Ruby on Rails
- PostgreSQL
- Hotwire (Turbo, Stimulus)
- Tailwind CSS
- RSpec (testing)

Standards to Create:

backend/:
- api.md: Rails API conventions (REST, serializers)
- migrations.md: Database migration best practices
- models.md: ActiveRecord patterns, validations, associations
- queries.md: N+1 prevention, scopes, query optimization

frontend/:
- components.md: ViewComponent or Phlex patterns
- css.md: Tailwind conventions
- responsive.md: Responsive design patterns
- accessibility.md: Rails accessibility practices

global/:
- coding-style.md: Ruby/Rails style guide (Rubocop-based)
- commenting.md: Ruby documentation patterns
- conventions.md: Rails naming conventions
- error-handling.md: Exception handling in Rails
- tech-stack.md: Rails version, gem management
- validation.md: Model and form validation patterns

testing/:
- test-writing.md: RSpec patterns, factories, request specs

Agent Customization:
The implementer agent should be a "full stack Rails developer" with expertise in Ruby, Rails conventions, Hotwire, and modern Rails patterns.
```

---

## Claude Code Tools Profile

**Purpose:** Specialized for developing Agent OS itself and Claude Code integrations.

**Prompt for `/bootstrap-profile`:**

```
Create a "claude-code-tools" profile for developing Claude Code tools and Agent OS profiles.

Tech Stack:
- Markdown (command and agent definitions)
- YAML (configuration)
- Bash (installation scripts)
- Claude Code agent system

Standards to Create:

claude-code/:
- command-structure.md: How to write slash commands
- prompt-engineering.md: Writing effective agent prompts
- subagent-design.md: Designing specialized subagents

global/:
- agent-workflow.md: Agent OS workflow and patterns
- coding-style.md: Style for markdown, YAML, bash
- commenting.md: Documentation patterns
- conventions.md: File naming, directory structure
- error-handling.md: Error handling in scripts and agents
- tech-stack.md: Agent OS architecture
- validation.md: Validating profile configurations

Agent Customization:
The implementer agent should be a "Claude Code integration specialist" with expertise in prompt engineering, AI agent design, and Agent OS architecture.

Additional Notes:
- This profile is for meta-development (building the tools themselves)
- Focus on clear documentation and atemporal design
- Emphasize maintainability and user experience
```

---

## Usage Instructions

After the v2.1.0 upgrade is complete and tested:

1. Run `/bootstrap-profile` in this repository
2. Copy-paste one of the prompts above
3. Review and customize the generated profile
4. Test with `~/agent-os/scripts/project-install.sh --profile <name> --dry-run`
5. Refine standards based on actual usage patterns

These profiles can be recreated in any order based on priority. The navigator profile is likely the most useful to recreate first.
