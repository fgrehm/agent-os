# Rails Fullstack Profile

## Purpose

This profile is designed for Ruby on Rails applications with modern TypeScript/Stimulus frontends, PostgreSQL databases, and service-oriented architectures.

**Ideal for:**
- Rails 8.0+ applications with monadic service patterns
- Full-stack applications with Rails backend + TypeScript/Stimulus frontend
- Projects using PostgreSQL with UUIDs and encrypted PII
- Teams using multi-agent workflows with specialized implementers and verifiers

## Tech Stack

**Backend:**
- Ruby on Rails 8.0+
- Service-oriented architecture with service_base gem (monadic patterns)
- PostgreSQL with UUID primary keys
- Encrypted sensitive data fields
- Soft deletion with deleted_at timestamps
- PaperTrail for audit trails

**Frontend:**
- TypeScript + Stimulus + Turbo
- Bootstrap 5.3+ for UI framework
- Slim templates
- ViewComponent for reusable components
- Vite Rails for asset bundling

**Testing:**
- RSpec for Ruby/Rails tests
- Vitest for TypeScript/JavaScript tests
- FactoryBot for test data

**Background Processing:**
- Solid Queue for background jobs

## Inherits From

`navigator`

This profile inherits the pause-for-review workflow from the navigator profile, enabling systematic code review checkpoints during implementation.

## Customizations

### Standards

- **Global Standards**: Coding style, commenting, conventions, error handling, validation
- **Backend Standards**: API design, migrations, models, database queries
- **Frontend Standards**: Accessibility, components, CSS, responsive design
- **Testing Standards**: Test writing conventions

All standards use references to centralized documentation to avoid duplication.

### Roles

**Implementers:**
1. **database-engineer** - Handles migrations, models, schemas, database queries
2. **api-engineer** - Handles API endpoints, controllers, business logic
3. **ui-designer** - Handles UI components, views, layouts, styling
4. **testing-engineer** - Handles comprehensive test coverage

**Verifiers:**
1. **backend-verifier** - Verifies database and API implementations
2. **frontend-verifier** - Verifies UI components, styling, responsive design, accessibility

### Workflows

Inherits all workflows from the navigator profile, including:
- Planning workflows
- Specification workflows
- Implementation workflows with review checkpoints

## Usage

### Install in a Project

```bash
~/agent-os/scripts/project-install.sh --profile rails-fullstack
```

### Update Existing Installation

```bash
~/agent-os/scripts/project-update.sh --profile rails-fullstack
```

## Example Projects

This profile was extracted from a production Rails application featuring:
- Rails 8.0 with service_base monadic patterns
- TypeScript/Stimulus frontend with Vite
- PostgreSQL with encrypted PII and UUID keys
- Multi-factor authentication system
- Third-party integrations (Plaid, Customer.io, LexisNexis, Twilio)
- Comprehensive RSpec + Vitest test suites

## Customization Tips

### Adding Standards

Create focused standard files in the appropriate directory:
- `standards/global/` - Cross-cutting concerns
- `standards/backend/` - Rails/Ruby specific
- `standards/frontend/` - TypeScript/Stimulus specific
- `standards/testing/` - Testing conventions

Keep standards modular (one concern per file) and use concrete examples.

### Adding Roles

Use the role creation script:

```bash
~/agent-os/scripts/create-role.sh
```

Or use the `/bootstrap-role` command for intelligent role generation.

### Adapting for API-Only Projects

If your Rails app is API-only (no frontend), add to `profile-config.yml`:

```yaml
exclude_inherited_files:
  - standards/frontend/*
  - roles/ui-designer
  - roles/frontend-verifier
```

### Adapting for GraphQL

Add a specialized role:

```yaml
# In roles/implementers.yml
- id: graphql-engineer
  description: Handles GraphQL schemas, resolvers, mutations, queries
  your_role: You are a GraphQL engineer...
  areas_of_responsibility:
    - Create GraphQL types and schemas
    - Implement GraphQL resolvers
    - Create GraphQL mutations and queries
  standards:
    - global/*
    - backend/*
  verified_by:
    - backend-verifier
```

## Documentation

For comprehensive Agent OS concepts and workflows:
- [Agent OS Overview](https://buildermethods.com/agent-os)
- [Profiles Guide](https://buildermethods.com/agent-os/profiles)
- [Roles System](https://buildermethods.com/agent-os/roles)
- [Workflow Commands](https://buildermethods.com/agent-os/workflows)
