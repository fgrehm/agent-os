---
description: Bootstrap a new Agent OS profile by analyzing project characteristics and tech stack
---

# Bootstrap Profile Command

You are helping create a new Agent OS profile tailored to a specific project type or tech stack.

## IMPORTANT: Profile Location

**Profiles are created in the CURRENT DIRECTORY**, not in `~/agent-os/profiles/`.

This command assumes you are running it from the Agent OS repository itself (or a fork). The profile will be created at `./profiles/[profile-name]/` relative to your current working directory.

If the user is not in an Agent OS repository, ask them to `cd` to the appropriate location first.

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
   - List available profiles in `./profiles/` (current directory)
   - Explain: Inheritance allows overriding specific files while reusing most configuration
   - Default suggestion: Inherit from `default` unless it's fundamentally different

3. **What makes this profile unique?**
   - What standards differ from the parent profile?
   - What specialized workflows are needed?
   - Will this use Claude Code Skills for standards?

4. **How should this profile handle Agent OS commands?**
   - If inheriting from default: Commands come automatically (can override specific ones)
   - If standalone: Copy commands from default, or skip them?
   - The default profile provides: /plan-product, /shape-spec, /write-spec, /create-tasks, /implement-tasks, /orchestrate-tasks, /improve-skills
   - Note: You can always add commands later

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

Create the profile directory structure directly in the current directory:

```bash
profile_name="[name-from-phase-1]"

# Create directory structure in current directory
mkdir -p "profiles/$profile_name"/{standards/{global,backend,frontend,testing},workflows/{implementation,planning,specification},agents,commands}
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
./profiles/[profile-name]/
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

Use the standard template format with placeholders:

```markdown
---
name: {{standard_name_humanized_capitalized}}
description: Your approach to handling {{standard_name_humanized}}. Use this skill when working on files where {{standard_name_humanized}} comes into play.
---

# {{standard_name_humanized_capitalized}}

This Skill provides Claude Code with specific guidance on how to adhere to [profile-specific context, e.g., "OSS best practices"] as they relate to {{standard_name_humanized}}.

## Instructions

For details, refer to the information provided in this file:
[{{standard_name_humanized}}](../../../{{standard_file_path}})
```

**Note:** The placeholders like `{{standard_name_humanized_capitalized}}` are replaced by the installation scripts when generating skills.

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

**CRITICAL: Keep standards LEAN (20-50 lines each, bullet points only)**

Standards are **reminders for Claude**, not documentation for humans. They get injected into agent context with `{{standards/*}}`, so verbosity = wasted tokens.

### Format: Bullet Points Only

Follow the default profile's style. Example from `profiles/default/standards/global/error-handling.md`:

```markdown
## Error handling best practices

- **User-Friendly Messages**: Provide clear, actionable error messages to users
- **Fail Fast and Explicitly**: Validate input and check preconditions early
- **Specific Exception Types**: Use specific exception/error types rather than generic ones
- **Centralized Error Handling**: Handle errors at appropriate boundaries
- **Graceful Degradation**: Design systems to degrade gracefully when non-critical services fail
- **Retry Strategies**: Implement exponential backoff for transient failures
- **Clean Up Resources**: Always clean up resources in finally blocks
```

That's it! 10 lines, just principles. NO extensive examples, NO "When to Apply" sections, NO tutorials.

### What to Include

For each standard:
- **Title**: Clear category (e.g., "README best practices for open source")
- **Bullet points**: 8-15 items covering key principles
- **Bold keywords**: Start each bullet with bold concept name
- **Brief explanation**: One sentence after the colon
- **NO sections**: No "When to Apply", "Examples", "Common Pitfalls" subheadings
- **NO templates**: No full code examples (brief inline examples OK)
- **Context-specific**: Focus on what makes this profile unique (e.g., OSS-specific concerns)

### Standard File Structure

```
./profiles/[profile-name]/standards/
├── global/              # Cross-cutting concerns
│   ├── licensing.md     # ~15 lines
│   ├── versioning.md    # ~15 lines
│   └── tech-stack.md    # ~20 lines
├── backend/             # Backend-specific (if applicable)
│   ├── api-design.md    # ~15 lines
│   └── database.md      # ~15 lines
└── repository/          # Project health (for OSS)
    ├── readme.md        # ~12 lines
    └── contributing.md  # ~15 lines
```

### Creating Each Standard

1. **Start minimal**: 3-5 core standards, not 13
2. **Identify principles**: What must Claude check/remember for this concern?
3. **Write bullets**: Each bullet = one principle/reminder
4. **Keep concise**: If over 25 lines, you're being too detailed
5. **Reference default**: Look at `profiles/default/standards/` for style examples

### Example: Good vs Bad

**❌ Bad (tutorial style, 200+ lines):**
```markdown
# README Standards

## When to Apply
- Creating new projects
- Updating documentation
...

## Complete Template

```markdown
# Project Name

Detailed template with all sections...
```

## Detailed Examples
...
```

**✅ Good (reminder style, 12 lines):**
```markdown
## README best practices for open source projects

- **Clear Project Description**: Lead with one-sentence description
- **Installation Instructions**: Provide clear, copy-paste commands
- **Quick Start Example**: Show basic usage (< 5 minutes to success)
- **License**: State clearly and link to LICENSE file
- **Status Badges**: Include build status, version, license at top
- **Documentation Links**: Point to full docs, API reference
- **Welcoming Tone**: Write for newcomers evaluating the project
```

### Prompting the User

For each category, ask:
"What are the 5-10 most important principles for [CATEGORY] in this profile?"

NOT: "Describe all the details of how to do [CATEGORY]"

Focus on WHAT matters, not HOW to do it (Claude already knows how).

### Skills-Specific Guidance

If user wants to use Claude Code Skills (`standards_as_claude_code_skills: true`):

1. **Write discoverable descriptions**: Start each standard with a clear summary
2. **Use specific keywords**: Include terms Claude should recognize (e.g., "Rails", "API", "database")
3. **Plan to run `/improve-skills`**: After installation, this command optimizes skill descriptions
4. **Focus on "when to apply"**: Help Claude understand context for using each skill

## PHASE 5.5: Create Workflow Files

Workflows provide reusable instruction blocks for common tasks. Consider creating workflows for:

### Implementation Workflows (workflows/implementation/)

Already created: `implement-tasks.md` from Phase 4.

Consider adding profile-specific implementation patterns:
- Code review checklists
- Testing requirements
- Documentation standards

### Planning Workflows (workflows/planning/)

For project planning and strategy:
- `plan-project.md` - High-level project planning
- `roadmap-planning.md` - Feature roadmap development
- `architecture-decisions.md` - ADR (Architecture Decision Records) process

**Example for OSS profile:**
- `plan-oss-project.md` - Planning with community focus, sustainability strategy

### Specification Workflows (workflows/specification/)

For writing feature specifications:
- `write-spec.md` - Detailed spec template
- `rfc-process.md` - Request for Comments workflow
- `api-design.md` - API specification standards

**Example for OSS profile:**
- `write-oss-spec.md` - Spec writing with RFC process, community review

**Note:** Workflows are injected using `{{workflows/path/file}}` syntax in commands and agents. If your profile inherits from another, you get those workflows automatically unless excluded.

## PHASE 6: Commands Strategy

Agent OS provides slash commands for the development workflow. Decide how to handle them for this profile.

### Option 1: Inherit Commands (Recommended for Most Profiles)

If your profile inherits from `default`, you automatically get these commands:
- `/plan-product` - Create mission, roadmap, tech-stack
- `/shape-spec` - Shape rough ideas into requirements
- `/write-spec` - Write detailed specification
- `/create-tasks` - Break spec into task list
- `/implement-tasks` - Simple implementation workflow
- `/orchestrate-tasks` - Advanced multi-agent orchestration
- `/improve-skills` - Optimize Claude Code Skills

**If inheriting:** You get these for free! Override only if you need customization.

### Option 2: Copy Default Commands

If your profile is standalone (`inherits_from: false`), you can copy commands:

```bash
# Copy all commands from default profile
cp -r profiles/default/commands/* "profiles/$profile_name/commands/"

# Or copy selectively
cp -r profiles/default/commands/implement-tasks "profiles/$profile_name/commands/"
cp -r profiles/default/commands/improve-skills "profiles/$profile_name/commands/"
```

**When to copy:**
- Profile doesn't inherit from default
- You want full Agent OS workflow commands
- You'll customize them for your domain

### Option 3: Custom Commands Only

Create only profile-specific commands:

```bash
# Example: OSS-specific command
mkdir -p "profiles/$profile_name/commands/create-oss-docs"
```

**When to go custom:**
- Your profile serves a specialized workflow (e.g., pure OSS project management)
- Default commands don't fit your domain
- You want minimal command set

### Option 4: Hybrid Approach

Copy some, customize others:

```bash
# Copy standard commands
cp -r profiles/default/commands/implement-tasks "profiles/$profile_name/commands/"

# Create custom command
mkdir -p "profiles/$profile_name/commands/setup-oss-repo"
```

### Commands to Consider

**Essential for most profiles:**
- `implement-tasks` - Basic implementation workflow
- `improve-skills` - If using Skills mode

**Useful for feature development:**
- `write-spec` - Feature specification
- `create-tasks` - Task breakdown

**For product planning:**
- `plan-product` - Strategic planning
- `shape-spec` - Idea refinement

**For complex projects:**
- `orchestrate-tasks` - Multi-agent coordination

### Prompting the User

Ask the user:

```
Do you want to include Agent OS workflow commands in this profile?

1. Inherit from default profile (get all commands automatically)
2. Copy commands from default (if standalone profile)
3. Skip commands for now (focus on standards only)

Note: You can always add commands later by copying from profiles/default/commands/
```

If copying, show what's available:
```bash
ls -1 profiles/default/commands/
```

And confirm which to copy.

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

📁 Location: ./profiles/[name]

📋 Structure Created:
  ✓ profile-config.yml (inheritance configured)
  ✓ claude-code-skill-template.md
  ✓ agents/implementer.md
  ✓ standards/ (ready for customization)
  ✓ workflows/ (inherits from parent or standalone)
  ✓ commands/ (ready for overrides)

📋 Next Steps:
1. Review and customize standards in profiles/[name]/standards/
2. Update agents/implementer.md with profile-specific expertise
3. Test in a project:
   ~/agent-os/scripts/project-install.sh --profile [name]
4. If using Skills, run /improve-skills in your project

💡 Tips:
- Keep standards modular (one concern per file)
- Keep standards lean: 20-50 lines of bullet points, no tutorials
- Start minimal, expand based on patterns you correct frequently
- Focus on reminders for Claude, not documentation for humans

📚 Refer to Agent OS documentation and existing profiles for examples
```

## Additional Notes

**Specialized Subagents:**
If you need specialized subagents for task orchestration, create them manually using Claude Code's subagent feature. Reference your profile's standards in their instructions for consistency.

**Profile Inheritance:**
Profiles can inherit from other profiles and override specific files. The inheritance system allows you to build specialized profiles while reusing common configurations.
