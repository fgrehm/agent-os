# State Management

## Remote State Backend

Always use remote state with locking. Never commit `.tfstate` files to git.

### AWS S3 Backend (Recommended)

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "myorg-terraform-state"
    key            = "projects/myapp/${var.environment}/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"

    # Optional: Use AWS KMS for encryption
    kms_key_id = "arn:aws:kms:us-east-1:123456789:key/..."
  }
}
```

**Setup state bucket:**
```bash
# Create S3 bucket for state
aws s3api create-bucket \
  --bucket myorg-terraform-state \
  --region us-east-1

# Enable versioning
aws s3api put-bucket-versioning \
  --bucket myorg-terraform-state \
  --versioning-configuration Status=Enabled

# Create DynamoDB table for locks
aws dynamodb create-table \
  --table-name terraform-state-locks \
  --attribute-definitions AttributeName=LockID,AttributeType=S \
  --key-schema AttributeName=LockID,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST
```

## Workspace Strategy

Use workspaces for environment separation:

```bash
# Create workspaces
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod

# Switch between environments
terraform workspace select prod
terraform plan
```

**Reference workspace in code:**
```hcl
locals {
  environment = terraform.workspace

  # Workspace-specific config
  instance_sizes = {
    dev     = "t3.small"
    staging = "t3.medium"
    prod    = "t3.large"
  }

  instance_type = local.instance_sizes[local.environment]
}
```

## State File Organization

### Single Environment per State

Keep environments in separate state files to prevent accidental cross-environment changes:

```
project/
├── environments/
│   ├── dev/
│   │   ├── backend.tf
│   │   ├── main.tf
│   │   └── terraform.tfstate (remote)
│   ├── staging/
│   │   ├── backend.tf
│   │   ├── main.tf
│   │   └── terraform.tfstate (remote)
│   └── prod/
│       ├── backend.tf
│       ├── main.tf
│       └── terraform.tfstate (remote)
└── modules/
    └── ...
```

### Shared State References

Use `terraform_remote_state` to reference outputs from other state files:

```hcl
data "terraform_remote_state" "networking" {
  backend = "s3"
  config = {
    bucket = "myorg-terraform-state"
    key    = "shared/networking/terraform.tfstate"
    region = "us-east-1"
  }
}

# Reference outputs
resource "aws_instance" "app" {
  subnet_id = data.terraform_remote_state.networking.outputs.subnet_id
  # ...
}
```

## State Locking

Always enable state locking to prevent concurrent modifications:

```hcl
# Automatically handled by S3 + DynamoDB backend
# Manual lock check:
terraform force-unlock <lock-id>  # Only if needed
```

## State Migration

When changing backends or restructuring:

```bash
# 1. Backup current state
terraform state pull > backup-$(date +%Y%m%d).tfstate

# 2. Update backend.tf with new configuration

# 3. Migrate state
terraform init -migrate-state

# 4. Verify
terraform plan  # Should show no changes
```

## State Security

- **Encryption**: Always enable encryption at rest
- **Access Control**: Use IAM policies to restrict state bucket access
- **Versioning**: Enable bucket versioning for rollback capability
- **Sensitive Data**: Mark sensitive outputs explicitly

```hcl
output "database_password" {
  value     = random_password.db.result
  sensitive = true  # Prevents showing in logs
}
```

## .gitignore

Always exclude state files:

```gitignore
# .gitignore
**/.terraform/*
*.tfstate
*.tfstate.*
.terraform.lock.hcl  # Optional: some teams commit this
```

## State Inspection

Safe commands for examining state:

```bash
# List resources
terraform state list

# Show resource details
terraform state show aws_instance.app

# Pull state for inspection (read-only)
terraform state pull > state-backup.json
```
