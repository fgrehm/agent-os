# Naming Conventions

## Resource Naming Pattern

Use consistent, descriptive names following the pattern:
```
{environment}-{service}-{resource-type}-{description}
```

**Examples:**
```hcl
# Good
resource "aws_s3_bucket" "prod_app_data_uploads" {
  bucket = "prod-myapp-data-uploads"
}

resource "cloudflare_record" "staging_app_web" {
  name = "staging-app"
}

# Avoid
resource "aws_s3_bucket" "bucket1" {
  bucket = "my-bucket"
}
```

## Variable Naming

- Use `snake_case` for all variables and locals
- Prefix boolean variables with `enable_` or `is_`
- Use plural for lists, singular for strings/numbers

```hcl
# Good
variable "enable_monitoring" {
  type    = bool
  default = true
}

variable "availability_zones" {
  type = list(string)
}

variable "instance_type" {
  type = string
}

# Avoid
variable "monitoring" { }
variable "az" { }
variable "instanceType" { }
```

## Module Naming

- Module directories use `kebab-case`
- Module names describe their purpose clearly

```
modules/
├── aws-vpc-network/
├── cloudflare-dns-zone/
├── heroku-app-pipeline/
└── shared-monitoring/
```

## File Naming

- Use descriptive names for resource files
- Group related resources together
- Separate providers into their own files

```
terraform/
├── main.tf           # Primary entry point
├── variables.tf      # Input variables
├── outputs.tf        # Output values
├── versions.tf       # Provider versions
├── backend.tf        # State backend config
├── aws-compute.tf    # AWS EC2/ECS resources
├── aws-storage.tf    # AWS S3/RDS resources
├── cloudflare-dns.tf # Cloudflare DNS records
└── heroku-apps.tf    # Heroku app configs
```

## Tag/Label Conventions

All cloud resources must include standard tags:

```hcl
locals {
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
    ManagedBy   = "terraform"
    Repository  = var.repo_url
    Owner       = var.team_owner
  }
}

resource "aws_instance" "app" {
  # ... other config ...

  tags = merge(local.common_tags, {
    Name = "${var.environment}-app-server"
    Role = "application"
  })
}
```

## Environment Naming

Use consistent environment identifiers:
- `dev` - Development
- `staging` - Staging/QA
- `prod` - Production

Never abbreviate differently (e.g., `development`, `stg`, `production`).
