---
description: Bootstrap a new Agent OS profile by analyzing project characteristics and tech stack
---

# Bootstrap Profile Command

You are helping create a new Agent OS profile tailored to a specific project type or tech stack.

## Process Overview

This command will guide the user through:
1. **Discovery**: Understanding the target project type and requirements
2. **Analysis**: Identifying appropriate standards, roles, and workflows
3. **Creation**: Generating the profile structure with customized configurations

## PHASE 1: Gather Requirements

Ask the user:

1. **What type of project is this profile for?**
   - Provide examples: "Rails API", "NextJS fullstack", "Python data science", "WordPress", "Rust CLI", etc.
   - If they have a specific project directory, offer to analyze it

2. **Should this profile inherit from an existing profile?**
   - List available profiles in `~/agent-os/profiles/`
   - Explain: Inheritance allows overriding specific files while reusing most configuration
   - Default suggestion: Inherit from `default` unless it's fundamentally different

3. **What makes this profile unique?**
   - What standards differ from the parent profile?
   - What roles are needed? (e.g., "mobile developer", "data engineer", "DevOps engineer")
   - Any special workflows or commands?

## PHASE 2: Project Analysis (Optional)

If the user provides a project directory path, analyze:

**Tech Stack Detection:**
- Check for `package.json`, `requirements.txt`, `Gemfile`, `go.mod`, `Cargo.toml`, etc.
- Identify frameworks: React/Next/Vue, Rails/Django/FastAPI, etc.
- Note databases: PostgreSQL, MongoDB, SQLite mentions
- Detect tools: Docker, CI configs, test frameworks

**Code Pattern Scanning:**
- Look for architectural patterns (MVC, microservices, monorepo)
- Identify common file naming conventions
- Note styling approaches (CSS modules, Tailwind, styled-components)
- Check test file patterns

**Standards Inference:**
- File organization patterns
- Import/require statement conventions
- Common code structure

Summarize findings and suggest which standards folders to create/customize.

## PHASE 3: Create Profile Structure

Create the profile directory structure directly:

```bash
profile_name="[name-from-phase-1]"
base_dir="$HOME/agent-os"

# Create directory structure
mkdir -p "$base_dir/profiles/$profile_name"/{standards/{global,backend,frontend,testing},workflows/{implementation,planning,specification},roles,commands}
```

**Create `profile-config.yml`:**

Based on Phase 1 decisions, determine inheritance:
- Web apps → inherit from `default`
- Dotfiles → inherit from `chezmoi` (if available)
- Specialized → appropriate parent or `inherits_from: false`

Write the configuration file:

```yaml
inherits_from: [parent-profile or false]

# Profile configuration for [profile-name]
# [Brief description of purpose]

# Optionally exclude inherited files if needed:
# exclude_inherited_files:
#   - standards/frontend/*  # Example for API-only profiles
#   - workflows/implementation/ui-specific.md
```

**Directory structure created:**
```
~/agent-os/profiles/[profile-name]/
├── profile-config.yml
├── standards/
│   ├── global/
│   ├── backend/
│   ├── frontend/
│   └── testing/
├── workflows/
│   ├── implementation/
│   ├── planning/
│   └── specification/
├── roles/
└── commands/
```

## PHASE 4: Create Standards Files

Based on Phase 2 analysis, create focused standard files in the generated directories:

Example structure:
```
~/agent-os/profiles/[profile-name]/standards/
├── global/
│   ├── naming-conventions.md
│   ├── error-handling.md
│   └── tech-stack.md
├── backend/
│   ├── api-design.md
│   ├── database-patterns.md
│   └── auth-patterns.md
└── testing/
    ├── unit-tests.md
    └── integration-tests.md
```

**For each standard:**
1. Identify patterns from analysis
2. Create focused files (keep under 300 lines each)
3. Use concrete examples over vague guidance

**Prompt user:**
"For [CATEGORY], what are the key patterns/conventions this project follows? (e.g., for API design: RESTful conventions, versioning strategy, error response format)"

## PHASE 5: Generate Role Definitions

Based on project analysis, create role definitions by delegating to the `role-generator` agent for each needed role.

### Determine Required Roles

**Based on analysis, identify roles needed:**

**For most projects:**
- `database-engineer` - Database operations
- `api-engineer` - Backend/API logic
- `testing-engineer` - Test coverage
- Backend verifier

**Add if frontend detected:**
- `ui-designer` - Frontend components
- Frontend verifier

**Add for specializations:**
- GraphQL → `graphql-engineer`
- WebSockets → `realtime-engineer`
- Docker/K8s → `infrastructure-engineer`
- ML/data → `ml-engineer`, `data-engineer`
- Mobile → `mobile-developer`
- Background jobs → `job-queue-engineer`

### Delegate to Role Generator Agent

For each role, use the Task tool to invoke the `role-generator` agent:

```
Use the role-generator agent to create a role for this profile.

Profile: [profile-name]
Tech Stack: [detected stack - e.g., "Rails API with PostgreSQL, Sidekiq, RSpec"]

Role: database-engineer
Type: Implementer
Domain: Database operations for [framework]
Responsibilities:
  - [Framework]-specific database work (e.g., "Rails migrations", "Prisma schema")
  - [Specific patterns identified in analysis]

Generate complete YAML with framework-specific language.
```

**Repeat for each role** (api-engineer, ui-designer, testing-engineer, verifiers, specialized roles).

The role-generator agent will:
1. Create properly structured YAML
2. Use framework-specific terminology
3. Write to appropriate file (implementers.yml or verifiers.yml)
4. Validate structure

### Present Results

After all roles are generated:

```
🤖 Generated [N] role definitions for your [tech-stack] profile:

Implementers:
  ✓ database-engineer ([framework]-specific database work)
  ✓ api-engineer ([framework]-specific API work)
  ✓ ui-designer ([framework]-specific frontend) [if applicable]
  ✓ testing-engineer ([test-framework] focused)

Verifiers:
  ✓ backend-verifier
  ✓ frontend-verifier [if applicable]

📁 Location: ~/agent-os/profiles/[profile-name]/roles/

Would you like to add more specialized roles?
```

## PHASE 6: Documentation

Create `profiles/[name]/README.md`:

```markdown
# [Profile Name] Profile

## Purpose
[Brief description of what projects this profile is for]

## Inherits From
`[parent-profile]`

## Customizations
- **Standards**: [List overridden/added standards]
- **Roles**: [List custom roles]
- **Workflows**: [List custom workflows if any]

## Usage
\`\`\`bash
~/agent-os/scripts/project-install.sh --profile [name]
\`\`\`

## Example Projects
- [Link or description of example project using this profile]
```

## Output Summary

Display to the user:

```
✅ Profile '[name]' created successfully!

📁 Location: ~/agent-os/profiles/[name]

📋 Next Steps:
1. Review and customize standards in profiles/[name]/standards/
2. Add roles with: ~/agent-os/scripts/create-role.sh
3. Test in a project: ~/agent-os/scripts/project-install.sh --profile [name]

📚 Docs: https://buildermethods.com/agent-os/profiles
```

## Notes

- Keep standards modular and focused (one concern per file)
- Use concrete examples over abstract principles
- Start minimal, expand based on patterns you correct frequently
- Consider creating mixins for common add-ons (auth, Docker, etc.)
