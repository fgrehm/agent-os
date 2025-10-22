# Environment Strategy

## Environment Separation

Maintain clear boundaries between dev, staging, and prod environments to prevent accidental changes.

## Directory Structure

**Recommended: Terraform Workspaces**

```
terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── backend.tf          # Single backend with workspace-aware key
├── dev.tfvars
├── staging.tfvars
├── prod.tfvars
└── modules/
    ├── aws-infrastructure/
    ├── cloudflare-dns/
    └── heroku-apps/
```

**Create and use workspaces:**
```bash
# Create workspaces
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod

# Deploy to specific environment
terraform workspace select prod
terraform apply -var-file=prod.tfvars
```

**Benefits:**
- Single codebase, DRY principle
- Easy to switch between environments
- Workspace name available as `terraform.workspace`
- State files automatically isolated per workspace

**Alternative: Environment Directories**

Use separate directories only if you need completely different infrastructure per environment (rare for multi-cloud apps).

## Environment Configuration

### tfvars Files

**dev.tfvars:**
```hcl
environment = "dev"
aws_region  = "us-east-1"

# Smaller resources
instance_type = "t3.small"
min_size      = 1
max_size      = 2

# Development settings
enable_monitoring = false
enable_backups    = false
heroku_dyno_type  = "hobby"
```

**prod.tfvars:**
```hcl
environment = "prod"
aws_region  = "us-east-1"

# Production-grade resources
instance_type = "t3.large"
min_size      = 3
max_size      = 10

# Full production features
enable_monitoring = true
enable_backups    = true
backup_retention  = 30
heroku_dyno_type  = "standard-2x"
```

### Workspace-Aware Backend

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "myorg-terraform-state"
    key            = "env://${terraform.workspace}/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"
  }
}
```

State files are automatically separated: `env://dev/terraform.tfstate`, `env://prod/terraform.tfstate`

### Environment-Aware Logic

Use `terraform.workspace` directly:

```hcl
locals {
  environment = terraform.workspace  # "dev", "staging", or "prod"

  # Environment-specific sizing
  instance_config = {
    dev     = { type = "t3.small", min = 1, max = 2 }
    staging = { type = "t3.medium", min = 2, max = 4 }
    prod    = { type = "t3.large", min = 3, max = 10 }
  }

  instance = local.instance_config[local.environment]

  # Force monitoring in prod
  enable_monitoring = local.environment == "prod" ? true : var.enable_monitoring
}

# Use in resources
resource "aws_s3_bucket" "app" {
  bucket = "${terraform.workspace}-myapp-data"

  tags = {
    Environment = terraform.workspace
    ManagedBy   = "terraform"
  }
}
```

## Provider Configuration per Environment

### AWS Account Separation

Use workspace-aware account switching:

```hcl
# main.tf
locals {
  aws_account_ids = {
    dev     = "111111111111"
    staging = "222222222222"
    prod    = "999999999999"
  }

  role_arn = "arn:aws:iam::${local.aws_account_ids[terraform.workspace]}:role/TerraformRole"
}

provider "aws" {
  region = var.aws_region

  assume_role {
    role_arn     = local.role_arn
    session_name = "terraform-${terraform.workspace}"
  }
}
```

Separate AWS accounts provide the best isolation. Workspace determines which account to use.

### Cloudflare Zones

```hcl
locals {
  cloudflare_zones = {
    dev     = "dev.myapp.com"
    staging = "staging.myapp.com"
    prod    = "myapp.com"  # Apex domain
  }
}

resource "cloudflare_zone" "main" {
  zone = local.cloudflare_zones[terraform.workspace]
}
```

### Heroku Pipelines

```hcl
locals {
  heroku_stages = {
    dev     = "development"
    staging = "staging"
    prod    = "production"
  }
}

resource "heroku_pipeline" "app" {
  name = "${var.project_name}-pipeline"
}

resource "heroku_app" "main" {
  name   = "${var.project_name}-${terraform.workspace}"
  region = "us"
}

resource "heroku_pipeline_coupling" "main" {
  app      = heroku_app.main.name
  pipeline = heroku_pipeline.app.id
  stage    = local.heroku_stages[terraform.workspace]
}
```

Workspace name determines pipeline stage: dev → staging → production.

## Promotion Strategy

### Development → Staging → Production

**Development:**
- Frequent deployments
- Test features
- Can destroy/recreate

**Staging:**
- Mirror of production
- Integration/load testing
- Same config as prod (smaller scale)

**Production:**
- Change control required
- Scheduled deployments
- Rollback plans

### Promotion Workflow

```bash
# 1. Test in dev
terraform workspace select dev
terraform apply -var-file=dev.tfvars

# 2. Promote to staging
terraform workspace select staging
terraform plan -var-file=staging.tfvars  # Review changes
terraform apply -var-file=staging.tfvars

# 3. Verify staging
# Run smoke tests, integration tests

# 4. Deploy to prod (scheduled)
terraform workspace select prod
terraform plan -var-file=prod.tfvars > prod-plan.txt
# Team reviews plan

terraform apply -var-file=prod.tfvars
```

**Check current workspace:**
```bash
terraform workspace show
terraform workspace list
```

## Shared Resources

Use a separate Terraform project for shared resources (Route53 zones, KMS keys, etc.):

```
projects/
├── shared-infrastructure/
│   ├── main.tf
│   ├── backend.tf
│   └── outputs.tf
└── application/
    ├── main.tf            # Uses workspaces
    ├── dev.tfvars
    ├── staging.tfvars
    └── prod.tfvars
```

**Reference shared outputs:**
```hcl
# In application/main.tf
data "terraform_remote_state" "shared" {
  backend = "s3"
  config = {
    bucket = "myorg-terraform-state"
    key    = "shared/terraform.tfstate"
    region = "us-east-1"
  }
}

resource "aws_route53_record" "app" {
  zone_id = data.terraform_remote_state.shared.outputs.public_zone_id
  name    = "${terraform.workspace}.myapp.com"
  # ...
}
```

## Environment Tagging

Use `terraform.workspace` for automatic environment tagging:

```hcl
locals {
  common_tags = {
    Environment = terraform.workspace
    Project     = var.project_name
    ManagedBy   = "terraform"
  }
}

resource "aws_instance" "app" {
  tags = merge(local.common_tags, {
    Name = "${terraform.workspace}-app-server"
  })
}
```

## Destroy Protection

Protect production from accidents:

```hcl
resource "aws_db_instance" "main" {
  # Prevent terraform destroy in prod workspace
  deletion_protection = terraform.workspace == "prod" ? true : false

  lifecycle {
    prevent_destroy = terraform.workspace == "prod" ? true : false
  }
}
```

**Safety tip:** Implement workspace validation to prevent typos:

```hcl
locals {
  valid_workspaces = ["dev", "staging", "prod"]

  # Fail fast if workspace is invalid
  validate_workspace = contains(local.valid_workspaces, terraform.workspace) ? true : tobool("ERROR: Invalid workspace '${terraform.workspace}'. Must be one of: ${join(", ", local.valid_workspaces)}")
}
```

## Environment Checklist

Before creating new environments:

- [ ] Workspace created: `terraform workspace new <name>`
- [ ] Environment-specific tfvars created: `<name>.tfvars`
- [ ] Workspace-aware backend configured with `env://` key
- [ ] AWS role ARN added to `aws_account_ids` map
- [ ] Cloudflare zone added to `cloudflare_zones` map
- [ ] Heroku stage added to `heroku_stages` map
- [ ] CI/CD workflows updated with new workspace
- [ ] Environment protection rules configured (GitHub/GitLab)
- [ ] Destroy protection uses `terraform.workspace == "prod"`
- [ ] Tags use `terraform.workspace` for environment
- [ ] Workspace validation includes new environment name
