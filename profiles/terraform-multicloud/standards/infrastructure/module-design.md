# Module Design

## Standard Module Layout

```
modules/aws-vpc-network/
├── README.md           # Module documentation with usage examples
├── main.tf             # Primary resources
├── variables.tf        # Input variables with validation
├── outputs.tf          # Output values for composition
├── versions.tf         # Provider version constraints
├── locals.tf           # Local values (optional)
└── examples/complete/  # Working example for testing
    ├── main.tf
    └── outputs.tf
```

**Every module must have:**
- Descriptive README with usage example
- Variables with descriptions and types
- Validation for critical inputs
- Outputs for values other modules need
- At least one example configuration

## Input Variables

**Always include:** description, type, validation for critical inputs, sensible defaults

```hcl
variable "environment" {
  description = "Environment name (dev, staging, prod)"
  type        = string

  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string

  validation {
    condition     = can(cidrhost(var.vpc_cidr, 0))
    error_message = "Must be valid CIDR."
  }
}

variable "enable_nat_gateway" {
  type    = bool
  default = true  # Sensible default
}
```

## Output Values

Expose values that other modules will reference:

```hcl
output "vpc_id" {
  description = "VPC identifier"
  value       = aws_vpc.main.id
}

output "private_subnet_ids" {
  description = "Private subnet IDs for compute resources"
  value       = aws_subnet.private[*].id
}

output "nat_gateway_ips" {
  description = "NAT Gateway IPs for allowlisting"
  value       = aws_eip.nat[*].public_ip
}
```

**Guidelines:**
- Always include descriptions
- Output resource IDs, ARNs, endpoints
- Use `[*]` for lists, not manual indexing

## Module Composition

Layer modules by referencing outputs:

```hcl
# Network foundation
module "vpc" {
  source      = "../../modules/aws-vpc-network"
  environment = var.environment
  vpc_cidr    = "10.0.0.0/16"
}

# Compute uses VPC outputs
module "app_cluster" {
  source     = "../../modules/aws-ecs-cluster"
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnet_ids
}

# Multi-cloud integration
module "heroku_app" {
  source   = "../../modules/heroku-app-pipeline"
  app_name = var.app_name

  config_vars = {
    DATABASE_URL = module.app_cluster.rds_endpoint
  }
}

module "dns" {
  source   = "../../modules/cloudflare-dns-zone"
  domain   = var.domain
  cname    = module.heroku_app.web_url
}
```

## Module Versioning

Pin to specific versions for stability:

```hcl
module "vpc" {
  source = "git::https://github.com/myorg/terraform-modules.git//aws-vpc-network?ref=v1.2.3"
}
```

Use semantic versioning: `v1.0.0` (major.minor.patch)

## Data-Only Modules

Query existing resources without creating new ones:

```hcl
# modules/aws-data-sources/main.tf
data "aws_vpc" "main" {
  filter {
    name   = "tag:Environment"
    values = [var.environment]
  }
}

output "vpc_id" {
  value = data.aws_vpc.main.id
}
```

Useful for referencing resources managed elsewhere.

## Multi-Provider Modules

Integrate AWS, Cloudflare, and Heroku in single modules:

```hcl
# modules/multi-cloud-app/versions.tf
terraform {
  required_providers {
    aws        = { source = "hashicorp/aws", version = "~> 5.0" }
    cloudflare = { source = "cloudflare/cloudflare", version = "~> 4.0" }
    heroku     = { source = "heroku/heroku", version = "~> 5.0" }
  }
}

# modules/multi-cloud-app/main.tf
resource "aws_s3_bucket" "assets" {
  bucket = "${var.app_name}-${var.environment}-assets"
}

resource "heroku_app" "main" {
  name = "${var.app_name}-${var.environment}"
  config_vars = {
    S3_BUCKET = aws_s3_bucket.assets.id
  }
}

resource "cloudflare_record" "app" {
  zone_id = var.cloudflare_zone_id
  name    = var.environment == "prod" ? "@" : var.environment
  type    = "CNAME"
  value   = heroku_app.main.web_url
  proxied = true
}
```

Resources automatically depend on each other when referencing attributes.

## Module Testing

Every module needs a working example:

```hcl
# modules/aws-vpc-network/examples/complete/main.tf
module "vpc_example" {
  source = "../.."

  environment = "dev"
  vpc_cidr    = "10.0.0.0/16"
}

output "vpc_id" {
  value = module.vpc_example.vpc_id
}
```

Test with: `terraform init && terraform plan`

See `standards/testing/terraform-testing.md` for comprehensive testing strategies.

## Best Practices

**Single Responsibility**
- ✅ `aws-vpc-network` - Creates VPC with subnets
- ✅ `aws-ecs-cluster` - Creates ECS cluster
- ❌ `aws-infrastructure` - Too broad

**Configurable with Defaults**
```hcl
variable "enable_monitoring" {
  type    = bool
  default = true  # Security best practice as default
}
```

**No Hard-Coded Values**
```hcl
# Bad: instance_type = "t3.medium"
# Good: instance_type = var.instance_type
```

**Use Dynamic Blocks Only When Truly Dynamic**
- Good: `for_each = var.ingress_rules` (variable count)
- Avoid: Using dynamic for static configs

**Consistent Naming**
```hcl
locals {
  name_prefix = "${var.environment}-${var.project_name}"
}

resource "aws_vpc" "main" {
  tags = {
    Name = "${local.name_prefix}-vpc"
  }
}
```
