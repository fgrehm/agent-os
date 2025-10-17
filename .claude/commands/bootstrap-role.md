---
description: Bootstrap a new Agent OS role (implementer or verifier) based on project needs
---

# Bootstrap Role Command

You are helping create a new Agent OS role definition for a specific domain or responsibility area.

## Process Overview

This command will guide the user through:
1. **Discovery**: Understanding what type of role is needed and why
2. **Definition**: Defining clear responsibilities and boundaries
3. **Configuration**: Setting up tools, standards, and verification
4. **Creation**: Generating the YAML role definition

## PHASE 1: Role Discovery

Ask the user:

1. **What profile should this role belong to?**
   - List available profiles in `~/agent-os/profiles/`
   - Default to `default` if unsure

2. **Is this an implementer or verifier role?**
   - **Implementer**: Writes code for specific domain (e.g., "API engineer", "mobile developer")
   - **Verifier**: Reviews and validates implementer work (e.g., "backend verifier", "security reviewer")

3. **What gap does this role fill?**
   - What work is currently not covered by existing roles?
   - What specific expertise does this role bring?
   - Examples: "GraphQL specialist", "ML pipeline engineer", "accessibility expert", "DevOps engineer"

4. **Analyze existing roles** (optional):
   - Read the profile's `roles/implementers.yml` and `roles/verifiers.yml`
   - Identify areas not covered
   - Suggest complementary roles

## PHASE 2: Role Definition

### For Implementer Roles

**Responsibilities:**
Ask: "What specific tasks will this role handle?"

Examples by domain:
- **GraphQL Engineer**: Schema design, resolver implementation, federation setup
- **ML Engineer**: Model training, pipeline creation, feature engineering
- **DevOps Engineer**: Infrastructure provisioning, CI/CD setup, deployment automation
- **Accessibility Engineer**: ARIA implementation, keyboard navigation, screen reader testing

**Boundaries (Out of Scope):**
Ask: "What should this role NOT do to avoid overlap?"

Example: A "GraphQL Engineer" should NOT:
- Write database migrations (that's database-engineer)
- Design UI components (that's ui-designer)
- Write business logic (that's api-engineer)

**Standards to Follow:**
Based on the role domain, suggest:
- Always: `global/*`
- Backend roles: `backend/*`, `testing/*`
- Frontend roles: `frontend/*`, `testing/*`
- Infrastructure roles: `infrastructure/*`, `security/*`
- Data roles: `data/*`, `testing/*`

Ask: "Are there specific standard files this role should follow?"

**Verification:**
Ask: "Which verifier(s) should review this role's work?"
- List existing verifiers from `roles/verifiers.yml`
- Suggest creating new verifier if needed

### For Verifier Roles

**Review Focus:**
Ask: "What aspects of code should this verifier check?"

Examples:
- **Security Verifier**: Input validation, auth checks, data exposure, injection vulnerabilities
- **Performance Verifier**: Query efficiency, caching, bundle size, load time
- **Accessibility Verifier**: Semantic HTML, ARIA, keyboard nav, screen reader compatibility

**Standards Reference:**
Verifiers should reference the same standards as the implementers they review.

## PHASE 3: Tool Configuration

**Standard Tools** (auto-include):
- `Write, Read, Bash, WebFetch, Glob, Grep`

**Specialized Tools:**
Ask based on role type:

- **Frontend/UI roles**: Add `Playwright` for browser testing
- **API testing**: Add `WebFetch` (already included)
- **Data roles**: Consider `Jupyter`, custom Python tools
- **DevOps**: Already covered by Bash

**Model Selection:**
- Default: `inherit` (uses project's Claude Code model setting)
- For complex reasoning (verifiers, architecture): Suggest `opus`
- For straightforward tasks: Suggest `sonnet`

**Color Selection:**
Suggest colors based on domain:
- Backend: `blue`, `orange`
- Frontend: `purple`, `pink`
- Testing/QA: `green`, `cyan`
- DevOps/Infra: `red`, `yellow`
- Data/ML: `cyan`, `purple`

## PHASE 4: Role Creation

### Option A: Use Interactive Script

Run the existing script:
```bash
~/agent-os/scripts/create-role.sh
```

Guide the user through prompts with intelligent suggestions based on the discovery phase.

### Option B: Generate YAML Directly

Create the YAML structure:

**For Implementer:**
```yaml
  - id: [role-id]  # e.g., graphql-engineer
    description: [One-line description]
    your_role: You are a [role title]. Your role is to [brief description of primary responsibilities].
    tools: Write, Read, Bash, WebFetch, Glob, Grep  # Add specialized tools
    model: inherit  # or sonnet/opus
    color: [suggested-color]
    areas_of_responsibility:
      - [Responsibility 1]
      - [Responsibility 2]
      - [Responsibility 3]
    example_areas_outside_of_responsibility:
      - [Out of scope 1]
      - [Out of scope 2]
      - [Out of scope 3]
    standards:
      - global/*
      - [domain]/*
      - testing/*
    verified_by:
      - [verifier-id]
```

**For Verifier:**
```yaml
  - id: [role-id]  # e.g., security-verifier
    description: [One-line description of what they verify]
    your_role: You are a [role title]. Your role is to review and verify [what aspects].
    tools: Read, Bash, WebFetch, Glob, Grep  # Verifiers typically don't Write
    model: opus  # Verifiers benefit from stronger reasoning
    color: [suggested-color]
    areas_of_responsibility:
      - Review [aspect 1]
      - Verify [aspect 2]
      - Check for [aspect 3]
    standards:
      - global/*
      - [domain]/*
```

**Write to file:**
```bash
# Append to appropriate file
cat >> ~/agent-os/profiles/[profile]/roles/[implementers|verifiers].yml << 'EOF'
[generated YAML]
EOF
```

## PHASE 5: Validation

After creation:

1. **Validate YAML syntax**:
   ```bash
   python3 -c "import yaml; yaml.safe_load(open('~/agent-os/profiles/[profile]/roles/[file].yml'))"
   ```

2. **Check for role conflicts**:
   - Ensure responsibilities don't heavily overlap with existing roles
   - Verify standards exist
   - Confirm verifiers exist (for implementers)

3. **Display role summary**:
   ```
   ✅ Role '[role-id]' created successfully!

   📋 Role Details:
   - Type: [Implementer/Verifier]
   - Responsibilities: [Count] areas defined
   - Standards: [List]
   - Verifiers: [List if implementer]

   📁 Location: ~/agent-os/profiles/[profile]/roles/[file].yml
   ```

## PHASE 6: Integration Guidance

Provide next steps:

```
🚀 Next Steps:

1. Review the role definition in:
   ~/agent-os/profiles/[profile]/roles/[file].yml

2. Customize standards if needed:
   ~/agent-os/profiles/[profile]/standards/

3. Update existing projects with this role:
   ~/agent-os/scripts/project-update.sh

4. Test the role by creating a spec that requires its expertise

📚 Learn more: https://buildermethods.com/agent-os/roles
```

## Role Examples by Domain

Provide domain-specific suggestions:

**Backend:**
- `graphql-engineer`, `websocket-engineer`, `job-queue-engineer`, `cache-engineer`

**Frontend:**
- `state-management-engineer`, `animation-designer`, `accessibility-engineer`, `performance-engineer`

**Data:**
- `etl-engineer`, `ml-engineer`, `data-visualization-designer`, `analytics-engineer`

**Infrastructure:**
- `kubernetes-engineer`, `cicd-engineer`, `monitoring-engineer`, `security-engineer`

**Mobile:**
- `ios-developer`, `android-developer`, `react-native-developer`, `mobile-backend-engineer`

**Content/CMS:**
- `content-modeler`, `migration-engineer`, `plugin-developer`, `theme-designer`

## Notes

- Keep responsibilities focused and clear
- Define boundaries explicitly to avoid overlap
- Match verifiers appropriately to implementer domains
- Use concrete, actionable language
- Consider creating complementary verifier roles for new implementer specializations
