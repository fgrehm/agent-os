# Provider Patterns

## AWS Provider

```hcl
# versions.tf
terraform {
  required_version = ">= 1.5"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# main.tf
provider "aws" {
  region = var.aws_region

  # Cross-account access
  assume_role {
    role_arn     = var.aws_assume_role_arn
    session_name = "terraform-${var.environment}"
  }

  # Auto-tag all resources
  default_tags {
    tags = {
      Environment = var.environment
      ManagedBy   = "terraform"
      Project     = var.project_name
    }
  }
}
```

### Multi-Region Setup

```hcl
provider "aws" {
  alias  = "us_west"
  region = "us-west-2"

  assume_role {
    role_arn = var.aws_assume_role_arn
  }

  default_tags {
    tags = local.common_tags
  }
}

# Use in resources
resource "aws_s3_bucket" "primary" {
  provider = aws  # Default region
  bucket   = "primary-bucket"
}

resource "aws_s3_bucket" "dr" {
  provider = aws.us_west
  bucket   = "dr-bucket"
}
```

## Cloudflare Provider

```hcl
# versions.tf
terraform {
  required_providers {
    cloudflare = {
      source  = "cloudflare/cloudflare"
      version = "~> 4.0"
    }
  }
}

# main.tf
provider "cloudflare" {
  # Uses CLOUDFLARE_API_TOKEN environment variable
}
```

**Authentication:**
```bash
export CLOUDFLARE_API_TOKEN="your-api-token"
```

Use API tokens (not email + API key) for better security.

## Heroku Provider

```hcl
# versions.tf
terraform {
  required_providers {
    heroku = {
      source  = "heroku/heroku"
      version = "~> 5.0"
    }
  }
}

# main.tf
provider "heroku" {
  # Uses HEROKU_API_KEY environment variable
}
```

**Authentication:**
```bash
export HEROKU_API_KEY="your-api-key"
```

## Multi-Provider Configuration

```hcl
# main.tf
terraform {
  required_version = ">= 1.5"

  required_providers {
    aws        = { source = "hashicorp/aws", version = "~> 5.0" }
    cloudflare = { source = "cloudflare/cloudflare", version = "~> 4.0" }
    heroku     = { source = "heroku/heroku", version = "~> 5.0" }
  }

  backend "s3" {
    bucket         = "myorg-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"
  }
}

provider "aws" {
  region = var.aws_region
}

provider "cloudflare" {
  # Uses CLOUDFLARE_API_TOKEN env var
}

provider "heroku" {
  # Uses HEROKU_API_KEY env var
}
```

## Cross-Provider Integration

**Example: Heroku app with AWS S3 and Cloudflare DNS**

```hcl
# AWS S3 bucket
resource "aws_s3_bucket" "assets" {
  bucket = "${var.environment}-${var.app_name}-assets"
}

resource "aws_s3_bucket_public_access_block" "assets" {
  bucket = aws_s3_bucket.assets.id

  block_public_acls   = true
  block_public_policy = true
}

# AWS IAM for Heroku
resource "aws_iam_user" "app" {
  name = "${var.app_name}-s3-access"
}

resource "aws_iam_access_key" "app" {
  user = aws_iam_user.app.name
}

resource "aws_iam_user_policy" "app_s3" {
  user = aws_iam_user.app.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Action = ["s3:GetObject", "s3:PutObject"]
      Resource = "${aws_s3_bucket.assets.arn}/*"
    }]
  })
}

# Heroku app with AWS credentials
resource "heroku_app" "main" {
  name   = "${var.app_name}-${var.environment}"
  region = "us"

  config_vars = {
    AWS_REGION = var.aws_region
    S3_BUCKET  = aws_s3_bucket.assets.id
  }

  sensitive_config_vars = {
    AWS_ACCESS_KEY_ID     = aws_iam_access_key.app.id
    AWS_SECRET_ACCESS_KEY = aws_iam_access_key.app.secret
  }
}

# Cloudflare DNS pointing to Heroku
data "cloudflare_zone" "main" {
  name = var.domain
}

resource "cloudflare_record" "app" {
  zone_id = data.cloudflare_zone.main.id
  name    = var.environment == "prod" ? "@" : var.environment
  type    = "CNAME"
  value   = trimsuffix(heroku_app.main.web_url, "/")
  proxied = true
}
```

**Key pattern:** Heroku depends on AWS resources, Cloudflare depends on Heroku app URL.

## Version Pinning

### Production (Strict)

```hcl
terraform {
  required_providers {
    aws        = { source = "hashicorp/aws", version = "5.31.0" }  # Exact
    cloudflare = { source = "cloudflare/cloudflare", version = "4.20.0" }
    heroku     = { source = "heroku/heroku", version = "5.2.8" }
  }
}
```

### Development (Flexible)

```hcl
terraform {
  required_providers {
    aws        = { source = "hashicorp/aws", version = "~> 5.31" }  # Patch updates OK
    cloudflare = { source = "cloudflare/cloudflare", version = "~> 4.20" }
    heroku     = { source = "heroku/heroku", version = "~> 5.2" }
  }
}
```

## Data Sources

### Querying Existing Resources

```hcl
# AWS
data "aws_vpc" "existing" {
  filter {
    name   = "tag:Name"
    values = ["${var.environment}-vpc"]
  }
}

data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# Cloudflare
data "cloudflare_zone" "main" {
  name = var.domain
}

# Heroku
data "heroku_addon" "database" {
  name = "${heroku_app.main.name}::postgres"
}
```

## Authentication Best Practices

### Environment Variables (Recommended)

```bash
# AWS
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."

# Cloudflare
export CLOUDFLARE_API_TOKEN="..."

# Heroku
export HEROKU_API_KEY="..."
```

### CI/CD Secrets

**GitHub Actions:**
```yaml
env:
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
  HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
```

**GitLab CI:**
```yaml
variables:
  AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
  AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
  CLOUDFLARE_API_TOKEN: $CLOUDFLARE_API_TOKEN
  HEROKU_API_KEY: $HEROKU_API_KEY
```

Store as protected secrets in your CI/CD platform.

### AWS AssumeRole (Best Practice)

```hcl
locals {
  aws_account_ids = {
    dev     = "111111111111"
    staging = "222222222222"
    prod    = "999999999999"
  }

  role_arn = "arn:aws:iam::${local.aws_account_ids[var.environment]}:role/TerraformRole"
}

provider "aws" {
  region = var.aws_region

  assume_role {
    role_arn     = local.role_arn
    session_name = "terraform-${var.environment}"
  }
}
```

Separate AWS accounts per environment for better isolation.

## Resource Dependencies

Handle cross-provider dependencies explicitly:

```hcl
# Heroku needs AWS resources first
resource "heroku_app" "main" {
  name = var.app_name

  config_vars = {
    DATABASE_URL = aws_db_instance.main.endpoint
  }

  depends_on = [aws_db_instance.main]
}

# Cloudflare needs Heroku app URL
resource "cloudflare_record" "app" {
  zone_id = data.cloudflare_zone.main.id
  name    = var.subdomain
  type    = "CNAME"
  value   = heroku_app.main.web_url

  depends_on = [heroku_app.main]
}
```

Terraform infers most dependencies automatically, but explicit `depends_on` helps with complex chains.
