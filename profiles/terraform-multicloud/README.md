# Terraform Multi-Cloud Profile

## Purpose

This profile is designed for Terraform projects managing infrastructure across **AWS**, **Cloudflare**, and **Heroku**. It provides comprehensive standards, roles, and best practices for multi-cloud infrastructure-as-code.

## Key Features

- **Multi-Provider Support**: Integrated patterns for AWS, Cloudflare, and Heroku
- **Workspace-Based Environments**: Terraform workspaces for dev/staging/prod separation
- **State Management**: Remote state backends, locking, and automatic workspace isolation
- **Security First**: Secret management, IAM least privilege, encryption, compliance
- **CI/CD Ready**: GitHub Actions workflows with automated testing and deployment
- **Testing**: Static analysis, unit tests, integration tests, and drift detection
- **Modular Design**: Reusable, composable infrastructure modules

## Inherits From

This profile is **independent** (`inherits_from: false`) and does not inherit from other profiles. It's purpose-built for infrastructure management.

## Profile Structure

```
terraform-multicloud/
├── profile-config.yml
├── standards/
│   ├── global/
│   │   ├── communication-style.md      # Technical language, no jargon
│   │   ├── naming-conventions.md       # Resource naming patterns
│   │   ├── state-management.md         # Remote state, workspaces, locking
│   │   ├── security-compliance.md      # Secrets, IAM, encryption, audit
│   │   └── environment-strategy.md     # Dev/staging/prod separation
│   ├── infrastructure/
│   │   ├── module-design.md            # Module structure and composition
│   │   ├── provider-patterns.md        # AWS/Cloudflare/Heroku configuration
│   │   └── cicd-integration.md         # GitHub Actions, drift detection
│   └── testing/
│       └── terraform-testing.md        # Testing strategies and tools
├── roles/
│   ├── implementers.yml                # 6 specialized roles
│   └── verifiers.yml                   # 3 verification roles
└── README.md                           # This file
```

## Implementer Roles

### infrastructure-engineer
Provisions and manages cloud resources across AWS, Cloudflare, and Heroku using Terraform modules.

**Responsibilities:**
- Design and implement Terraform modules
- Configure multi-cloud integrations
- Create reusable infrastructure components

### state-engineer
Manages state files, remote backends, and environment separation.

**Responsibilities:**
- Configure S3 backends with DynamoDB locking
- Set up workspace strategies
- Handle state migrations

### security-engineer
Ensures security, compliance, and proper secret management.

**Responsibilities:**
- Implement secret management (AWS Secrets Manager)
- Configure IAM policies with least privilege
- Enable encryption and audit logging

### devops-engineer
Automates Terraform workflows in CI/CD pipelines.

**Responsibilities:**
- Create GitHub Actions workflows
- Set up drift detection and notifications
- Integrate security scanning tools

### testing-engineer
Tests infrastructure code at multiple levels.

**Responsibilities:**
- Configure static analysis (tfsec, checkov, TFLint)
- Write unit tests (Terratest)
- Create integration and smoke tests

### environment-engineer
Manages multi-environment configuration and promotion using Terraform workspaces.

**Responsibilities:**
- Configure Terraform workspaces for environment separation
- Create workspace-specific tfvars files
- Implement promotion workflows (dev → staging → prod)

## Verifier Roles

### infrastructure-verifier
Reviews infrastructure code quality and best practices.

**Verifies:** infrastructure-engineer, state-engineer, environment-engineer, devops-engineer

### security-verifier
Ensures security requirements and compliance.

**Verifies:** security-engineer, infrastructure-engineer

### testing-verifier
Reviews test coverage and quality.

**Verifies:** testing-engineer, infrastructure-engineer

## Standards Overview

### Global Standards

- **Communication Style**: Technical, direct language - no business jargon or buzzwords
- **Naming Conventions**: Consistent resource naming across all providers
- **State Management**: Remote state, locking, workspaces, migrations
- **Security & Compliance**: Secrets, IAM, encryption, audit logging, compliance tags
- **Environment Strategy**: Dev/staging/prod separation, promotion workflows

### Infrastructure Standards

- **Module Design**: Structure, composition, versioning, testing
- **Provider Patterns**: AWS/Cloudflare/Heroku configuration, authentication, multi-region
- **CI/CD Integration**: GitHub Actions, drift detection, cost estimation

### Testing Standards

- **Terraform Testing**: Static analysis, unit tests, integration tests, policy testing

## Usage

### Installation

Install this profile into your Terraform project:

```bash
~/agent-os/scripts/project-install.sh --profile terraform-multicloud
```

This creates an `agent-os/` directory in your project with:
- All standards documentation
- Role-based agent configurations
- Workflow templates

### Typical Project Structure

After installation, organize your Terraform project like this:

```
my-terraform-project/
├── agent-os/                  # Created by installer
│   ├── standards/             # Profile standards
│   ├── roles/                 # Role definitions
│   └── ...
├── terraform/
│   ├── main.tf                # Main configuration
│   ├── variables.tf           # Input variables
│   ├── outputs.tf             # Output values
│   ├── versions.tf            # Provider versions
│   ├── backend.tf             # State backend (workspace-aware)
│   ├── dev.tfvars             # Dev workspace config
│   ├── staging.tfvars         # Staging workspace config
│   ├── prod.tfvars            # Prod workspace config
│   └── modules/
│       ├── aws-vpc-network/
│       ├── cloudflare-dns-zone/
│       └── heroku-app-pipeline/
├── test/                      # Terratest tests
└── .github/workflows/         # GitHub Actions pipelines
```

**Deploy to environments:**
```bash
cd terraform
terraform workspace select dev
terraform apply -var-file=dev.tfvars
```

### Updating Profile

To update an existing installation with profile changes:

```bash
~/agent-os/scripts/project-update.sh
```

## Workflow

This profile uses the standard Agent OS workflow:

### 1. Plan Product (Once)
```bash
/plan-product
```
Creates mission statement, roadmap, and tech stack documentation.

### 2. New Spec (Per Feature)
```bash
/new-spec
```
Research and gather requirements for infrastructure changes (e.g., "Add VPC with multi-AZ setup").

### 3. Create Spec (Per Feature)
```bash
/create-spec
```
Write detailed specification and task breakdown for implementation.

### 4. Implement Spec (Per Feature)
```bash
/implement-spec
```
Specialized agents (infrastructure-engineer, security-engineer, etc.) implement the spec according to standards, then verifiers ensure quality.

**Agents work with standards automatically** - no need to reference them manually. The profile standards guide all implementations.

## Key Principles

### 1. Technical Communication
- Use direct, technical language - no business jargon or buzzwords
- Discuss impact in concrete terms: cost, availability, security metrics
- Write like an engineer, not a consultant
- See `standards/global/communication-style.md` for examples

### 2. Workspace-Based Environments
- Use Terraform workspaces for environment separation
- Single codebase with workspace-specific tfvars
- Automatic state isolation per workspace

### 3. Security First
- No secrets in code
- IAM least privilege
- Encryption everywhere applicable
- Audit logging enabled

### 4. Modular Design
- Single responsibility per module
- Reusable and composable
- Well-documented with examples

### 5. State Safety
- Always use remote state with locking
- Never commit `.tfstate` files to git
- Workspace-aware backend with `env://` keys

### 6. Automation
- CI/CD for all changes
- Automated testing and validation
- Drift detection and notifications

## Tools and Integrations

This profile includes guidance for:

- **Terraform**: v1.5+ with workspaces
- **AWS Provider**: v5.0+
- **Cloudflare Provider**: v4.0+
- **Heroku Provider**: v5.0+
- **Static Analysis**: tfsec, checkov, TFLint
- **Testing**: Terratest, native terraform test
- **CI/CD**: GitHub Actions
- **Cost**: Infracost
- **Policy**: OPA/conftest

## Example Projects

This profile works well for:

- **Multi-cloud web applications**: Heroku app with AWS RDS and Cloudflare DNS
- **Hybrid infrastructure**: Heroku for apps, AWS for data storage and processing
- **Global deployments**: Cloudflare CDN with AWS regional backends
- **Infrastructure platforms**: Reusable module libraries for org-wide use

## Getting Help

- **Agent OS Docs**: https://buildermethods.com/agent-os
- **Profile Location**: `~/agent-os/profiles/terraform-multicloud/`
- **Standards**: Check `standards/` directory for detailed guidance
- **Examples**: See module examples in each standard document

## Customization

To customize this profile for your needs:

1. **Add Provider**: Add new provider patterns to `standards/infrastructure/provider-patterns.md`
2. **Add Role**: Use `~/agent-os/scripts/create-role.sh` to add specialized roles
3. **Add Standard**: Create new `.md` files in appropriate `standards/` subdirectory
4. **Override Files**: Project installation copies files; edit in `agent-os/` directory

## Contributing

This profile is part of a fork of Agent OS. To contribute:

1. Make changes in `~/agent-os/profiles/terraform-multicloud/`
2. Test in a project using `project-install.sh --profile terraform-multicloud`
3. Submit improvements via pull request

---

**Profile Version**: 1.0.0
**Last Updated**: 2025-10-17
**Maintainer**: Agent OS Community
