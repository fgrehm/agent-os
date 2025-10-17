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

## PHASE 3: Profile Creation

Use the existing script with intelligent defaults:

```bash
~/agent-os/scripts/create-profile.sh
```

Guide the user through the prompts, providing informed suggestions based on analysis.

**OR** automate with bash commands:

```bash
# Example: Create profile structure directly
profile_name="rails-api"
base_dir="$HOME/agent-os"
mkdir -p "$base_dir/profiles/$profile_name"/{standards/{global,backend,testing},workflows/{implementation,planning,specification},roles,commands}
```

Create `profile-config.yml`:
```yaml
inherits_from: default  # or appropriate parent

# Override specific files:
exclude_inherited_files:
  - standards/frontend/*  # Example: API-only profile excludes frontend
  - workflows/implementation/ui-specific.md
```

## PHASE 4: Standards Customization

For each standards directory needed:

1. **Identify what to customize** from analysis
2. **Create focused standard files** (keep under 300 lines each)
3. **Use concrete examples** over vague guidance

Example structure:
```
standards/
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

**Prompt for each standard:**
"For [CATEGORY], what are the key patterns/conventions this project follows? (e.g., for API design: RESTful conventions, versioning strategy, error response format)"

## PHASE 5: Role Suggestions

Based on the project type, suggest implementer roles:

**For fullstack web apps:**
- `database-engineer`, `api-engineer`, `ui-designer`, `testing-engineer`

**For API-only projects:**
- `database-engineer`, `api-engineer`, `testing-engineer` (exclude frontend)

**For data science:**
- `data-engineer`, `ml-engineer`, `visualization-designer`, `notebook-maintainer`

**For mobile apps:**
- `mobile-developer`, `backend-engineer`, `ui-designer`, `testing-engineer`

**For infrastructure/DevOps:**
- `infrastructure-engineer`, `cicd-engineer`, `security-engineer`, `monitoring-engineer`

Offer to run `~/agent-os/scripts/create-role.sh` for each suggested role, or provide YAML templates to copy into `roles/implementers.yml` and `roles/verifiers.yml`.

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
