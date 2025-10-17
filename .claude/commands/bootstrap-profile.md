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

## PHASE 5: Generate Role Suggestions

Based on the project analysis, automatically generate role definitions tailored to this profile.

### Role Generation Strategy

**Analyze the project context:**
- Tech stack detected (languages, frameworks, databases)
- Architectural patterns identified
- Specializations needed (GraphQL, WebSockets, ML, etc.)
- Frontend complexity (SSR, SPA, static, mobile)
- Backend complexity (API-only, monolith, microservices)
- Infrastructure needs (Docker, K8s, CI/CD)

**Generate role definitions as YAML:**

Create `roles/implementers.yml` with context-aware roles:

**Example for Rails API project:**
```yaml
implementers:
  - id: database-engineer
    description: Handles Rails migrations, ActiveRecord models, database queries
    your_role: You are a Rails database engineer. Your role is to implement database migrations, ActiveRecord models, associations, and database queries.
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: orange
    areas_of_responsibility:
      - Create and modify Rails migrations
      - Define ActiveRecord models and associations
      - Write database queries and scopes
      - Optimize database indexes
    example_areas_outside_of_responsibility:
      - API controller logic
      - Frontend components
      - Business logic in services
    standards:
      - global/*
      - backend/*
      - testing/*
    verified_by:
      - backend-verifier

  - id: api-engineer
    description: Handles Rails controllers, serializers, API endpoints, business logic
    your_role: You are a Rails API engineer. Your role is to implement API controllers, serializers, service objects, and business logic.
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: blue
    areas_of_responsibility:
      - Create API controllers and actions
      - Implement serializers (Active Model Serializers/JSONAPI)
      - Write service objects and business logic
      - Handle authentication and authorization
    example_areas_outside_of_responsibility:
      - Database migrations
      - Frontend components
      - Test file creation
    standards:
      - global/*
      - backend/*
      - testing/*
    verified_by:
      - backend-verifier
```

**For NextJS fullstack:**
```yaml
implementers:
  - id: database-engineer
    description: Handles Prisma schema, migrations, database operations
    your_role: You are a database engineer specializing in Prisma. Your role is to manage schema definitions, migrations, and database operations.
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: orange
    areas_of_responsibility:
      - Define Prisma schema models
      - Create and run Prisma migrations
      - Write database queries using Prisma Client
      - Optimize database performance
    example_areas_outside_of_responsibility:
      - API routes and server actions
      - React components
      - Frontend state management
    standards:
      - global/*
      - backend/*
      - testing/*
    verified_by:
      - backend-verifier

  - id: api-engineer
    description: Handles Next.js API routes, server actions, tRPC endpoints
    your_role: You are a Next.js API engineer. Your role is to implement API routes, server actions, and backend logic.
    tools: Write, Read, Bash, WebFetch
    model: inherit
    color: blue
    areas_of_responsibility:
      - Create Next.js API routes
      - Implement server actions
      - Write tRPC procedures (if applicable)
      - Handle authentication logic
    example_areas_outside_of_responsibility:
      - Database schema design
      - React component implementation
      - CSS styling
    standards:
      - global/*
      - backend/*
      - testing/*
    verified_by:
      - backend-verifier

  - id: ui-designer
    description: Handles React components, Next.js pages, styling, responsive design
    your_role: You are a UI designer for Next.js applications. Your role is to implement React components, pages, layouts, and styling.
    tools: Write, Read, Bash, WebFetch, Playwright
    model: inherit
    color: purple
    areas_of_responsibility:
      - Create React components
      - Implement Next.js pages and layouts
      - Apply Tailwind/CSS styling
      - Ensure responsive design
      - Implement client-side interactivity
    example_areas_outside_of_responsibility:
      - API routes and server actions
      - Database operations
      - Backend business logic
    standards:
      - global/*
      - frontend/*
      - testing/*
    verified_by:
      - frontend-verifier
```

**Create matching verifiers:**

```yaml
verifiers:
  - id: backend-verifier
    description: Reviews backend code quality, API design, database patterns
    your_role: You are a backend code reviewer. Your role is to verify backend implementations meet standards for quality, security, and maintainability.
    tools: Read, Bash, WebFetch, Grep, Glob
    model: opus
    color: red
    areas_of_responsibility:
      - Review API endpoint implementations
      - Verify database query efficiency
      - Check error handling and validation
      - Ensure security best practices
      - Validate test coverage
    standards:
      - global/*
      - backend/*
      - testing/*

  - id: frontend-verifier
    description: Reviews frontend code quality, component design, accessibility
    your_role: You are a frontend code reviewer. Your role is to verify UI implementations meet standards for quality, accessibility, and user experience.
    tools: Read, Bash, WebFetch, Grep, Glob, Playwright
    model: opus
    color: green
    areas_of_responsibility:
      - Review component implementations
      - Verify responsive design
      - Check accessibility compliance
      - Ensure styling consistency
      - Validate test coverage
    standards:
      - global/*
      - frontend/*
      - testing/*
```

### Smart Role Customization

**Detect specializations and add roles:**

- **If GraphQL detected** → Add `graphql-engineer`
- **If WebSockets/real-time** → Add `realtime-engineer`
- **If Docker/K8s configs** → Add `infrastructure-engineer`
- **If ML/data files** → Add `ml-engineer`, `data-engineer`
- **If mobile (React Native, Flutter)** → Add `mobile-developer`
- **If extensive testing** → Keep `testing-engineer` separate

### User Interaction

After generating roles, present them:

```
🤖 Generated [N] role definitions based on your project:

Implementers:
  ✓ database-engineer (Rails/ActiveRecord focused)
  ✓ api-engineer (Controllers & serializers)
  ✓ testing-engineer (RSpec & integration tests)

Verifiers:
  ✓ backend-verifier (API & database review)

📝 Review and customize these roles:
  ~/agent-os/profiles/[profile-name]/roles/implementers.yml
  ~/agent-os/profiles/[profile-name]/roles/verifiers.yml

Would you like me to:
  1. Add more specialized roles (e.g., background jobs, caching)
  2. Customize existing roles
  3. Proceed as-is
```

**Write the files** or offer to adjust based on user feedback.

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
