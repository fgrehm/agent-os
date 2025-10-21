---
description: Bootstrap a new Agent OS profile by analyzing project characteristics and tech stack
---

# Bootstrap Profile Command

You are helping create a new Agent OS profile tailored to a specific project type or tech stack.

## Process Overview

This command will guide the user through:
1. **Discovery**: Understanding the target project type and requirements
2. **Analysis**: Identifying appropriate standards and workflows
3. **Creation**: Generating the profile structure with customized configurations

## PHASE 1: Gather Requirements

Ask the user:

1. **What type of project is this profile for?**
   - Provide examples: "Rails API", "NextJS fullstack", "Python data science", "WordPress", "Rust CLI", "Terraform infrastructure", etc.
   - If they have a specific project directory, offer to analyze it

2. **Should this profile inherit from an existing profile?**
   - List available profiles in `~/agent-os/profiles/`
   - Explain: Inheritance allows overriding specific files while reusing most configuration
   - Default suggestion: Inherit from `default` unless it's fundamentally different

3. **What makes this profile unique?**
   - What standards differ from the parent profile?
   - What specialized workflows are needed?
   - Any custom commands to override?
   - Will this use Claude Code Skills for standards?

## PHASE 2: Project Analysis (Optional)

If the user provides a project directory path, analyze:

**Tech Stack Detection:**
- Check for `package.json`, `requirements.txt`, `Gemfile`, `go.mod`, `Cargo.toml`, etc.
- Identify frameworks: React/Next/Vue, Rails/Django/FastAPI, etc.
- Note databases: PostgreSQL, MongoDB, SQLite mentions
- Detect tools: Docker, CI configs, test frameworks
- Infrastructure: Terraform, CloudFormation, Kubernetes configs

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
mkdir -p "$base_dir/profiles/$profile_name"/{standards/{global,backend,frontend,testing},workflows/{implementation,planning,specification},agents,commands}
```

**Create `profile-config.yml`:**

Based on Phase 1 decisions, determine inheritance:
- Web apps → inherit from `default`
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
#   - commands/shape-spec/*  # If you want to provide custom command
```

**Directory structure created:**
```
~/agent-os/profiles/[profile-name]/
├── profile-config.yml
├── agents/
│   └── implementer.md          # Generic implementer for this profile
├── claude-code-skill-template.md  # Template for Skills descriptions
├── standards/
│   ├── global/
│   ├── backend/
│   ├── frontend/
│   └── testing/
├── workflows/
│   ├── implementation/
│   ├── planning/
│   └── specification/
└── commands/                   # Override specific commands if needed
    ├── shape-spec/
    ├── write-spec/
    ├── create-tasks/
    ├── implement-tasks/
    └── orchestrate-tasks/
```

## PHASE 4: Create Core Files

### 4.1 Create `claude-code-skill-template.md`

Copy from default profile or create:

```markdown
# [Skill Title]

Brief description of what this standard covers.

## When to Apply

Describe when Claude should use this skill.

## Key Patterns

[Concrete examples and patterns specific to your tech stack]

## Related Standards

- Related standard 1
- Related standard 2
```

### 4.2 Create `agents/implementer.md`

Create a generic implementer agent for this profile:

```markdown
---
name: implementer
description: Use proactively to implement features for [profile-type] projects following [framework] best practices
tools: Write, Read, Bash, WebFetch, Playwright
color: red
model: inherit
---

You are a [specialized description - e.g., "full stack Rails developer", "infrastructure engineer", "data scientist"] with deep expertise in [key areas]. Your role is to implement features by closely following specifications and adhering to [framework/language] conventions.

{{workflows/implementation/implement-tasks}}

{{UNLESS standards_as_claude_code_skills}}
## User Standards & Preferences Compliance

IMPORTANT: Ensure your implementation IS ALIGNED and DOES NOT CONFLICT with the user's preferred tech stack, coding conventions, and patterns as detailed in:

{{standards/*}}
{{ENDUNLESS standards_as_claude_code_skills}}
```

## PHASE 5: Create Standards Files

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
4. If using Skills: Write clear descriptions for discoverability
5. Use the skill template structure for consistency

**Prompt user:**
"For [CATEGORY], what are the key patterns/conventions this project follows? (e.g., for API design: RESTful conventions, versioning strategy, error response format)"

### Skills-Specific Guidance

If user wants to use Claude Code Skills (`standards_as_claude_code_skills: true`):

1. **Write discoverable descriptions**: Start each standard with a clear summary
2. **Use specific keywords**: Include terms Claude should recognize (e.g., "Rails", "API", "database")
3. **Plan to run `/improve-skills`**: After installation, this command optimizes skill descriptions
4. **Focus on "when to apply"**: Help Claude understand context for using each skill

## PHASE 6: Optional Custom Commands

If the profile needs custom workflow variations, create command overrides:

**Example: Custom orchestrate-tasks for infrastructure projects:**

```bash
mkdir -p "$base_dir/profiles/$profile_name/commands/orchestrate-tasks"
```

Create customized command that references profile-specific workflows.

## PHASE 7: Documentation

Create `profiles/[name]/README.md`:

```markdown
# [Profile Name] Profile

## Purpose
[Brief description of what projects this profile is for]

## Tech Stack
- [Primary language/framework]
- [Key tools/libraries]
- [Infrastructure/deployment tools]

## Inherits From
`[parent-profile]`

## Customizations
- **Standards**: [List overridden/added standards]
- **Agents**: [Custom implementer description]
- **Commands**: [List command overrides if any]

## Configuration

Supports both traditional standards injection and Claude Code Skills mode.

### Installation

\`\`\`bash
~/agent-os/scripts/project-install.sh --profile [name]
\`\`\`

### With Claude Code Skills

\`\`\`bash
# config.yml should have:
# standards_as_claude_code_skills: true

~/agent-os/scripts/project-install.sh --profile [name]

# Then optimize skills:
cd your-project
/improve-skills
\`\`\`

## Example Projects
- [Link or description of example project using this profile]
```

## Output Summary

Display to the user:

```
✅ Profile '[name]' created successfully!

📁 Location: ~/agent-os/profiles/[name]

📋 Structure Created:
  ✓ profile-config.yml (inheritance configured)
  ✓ claude-code-skill-template.md
  ✓ agents/implementer.md
  ✓ standards/ (ready for customization)
  ✓ workflows/ (inherits from parent)
  ✓ commands/ (ready for overrides)

📋 Next Steps:
1. Review and customize standards in profiles/[name]/standards/
2. Update agents/implementer.md with profile-specific expertise
3. Test in a project:
   ~/agent-os/scripts/project-install.sh --profile [name]
4. If using Skills, run /improve-skills in your project

💡 Tips:
- Keep standards modular (one concern per file)
- Use concrete examples over abstract principles
- Start minimal, expand based on patterns you see frequently
- Consider creating specialized workflow overrides in commands/

📚 Docs: https://buildermethods.com/agent-os/profiles
```

## Additional Notes

**Specialized Subagents:**
If you need specialized subagents for task orchestration, create them manually using Claude Code's subagent feature. Reference your profile's standards in their instructions for consistency.

**Profile Inheritance:**
Profiles can inherit from other profiles and override specific files. The inheritance system allows you to build specialized profiles while reusing common configurations.
