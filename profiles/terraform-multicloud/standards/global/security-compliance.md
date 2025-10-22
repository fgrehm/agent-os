# Security & Compliance

## Secret Management

**NEVER** commit secrets to git. Use secure secret management solutions.

### AWS Secrets Manager

```hcl
# Store secrets
resource "aws_secretsmanager_secret" "db_password" {
  name = "${var.environment}/database/master-password"

  recovery_window_in_days = 7
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id     = aws_secretsmanager_secret.db_password.id
  secret_string = random_password.db_master.result
}

# Reference secrets
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = aws_secretsmanager_secret.db_password.id
}

locals {
  db_password = jsondecode(data.aws_secretsmanager_secret_version.db_password.secret_string)
}
```

### Environment Variables for CI/CD

Use environment variables for provider credentials:

```bash
# AWS
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export AWS_SESSION_TOKEN="..."  # If using temporary credentials

# Cloudflare
export CLOUDFLARE_API_TOKEN="..."

# Heroku
export HEROKU_API_KEY="..."
```

**In Terraform:**
```hcl
# Providers pick up credentials automatically
provider "aws" {
  region = var.aws_region
  # No hardcoded credentials
}

provider "cloudflare" {
  # Uses CLOUDFLARE_API_TOKEN env var
}

provider "heroku" {
  # Uses HEROKU_API_KEY env var
}
```

## Sensitive Output Handling

Mark all sensitive data:

```hcl
output "database_endpoint" {
  value     = aws_db_instance.main.endpoint
  sensitive = false  # Public info
}

output "database_password" {
  value     = random_password.db_master.result
  sensitive = true  # Secret
}

output "api_keys" {
  value = {
    cloudflare = cloudflare_api_token.app.value
    heroku     = heroku_app.main.config_vars["SECRET_KEY"]
  }
  sensitive = true
}
```

## IAM Least Privilege

Follow principle of least privilege for all IAM resources:

```hcl
# Good: Specific permissions
resource "aws_iam_policy" "app_s3_access" {
  name = "${var.environment}-app-s3-access"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject",
          "s3:PutObject",
          "s3:DeleteObject"
        ]
        Resource = [
          "${aws_s3_bucket.app_data.arn}/*"
        ]
      }
    ]
  })
}

# Avoid: Overly broad permissions
resource "aws_iam_policy" "bad_example" {
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = "s3:*"      # Too broad
        Resource = "*"         # Too broad
      }
    ]
  })
}
```

## Resource Access Controls

### S3 Bucket Security

```hcl
resource "aws_s3_bucket" "secure_data" {
  bucket = "${var.environment}-secure-data"
}

# Block public access
resource "aws_s3_bucket_public_access_block" "secure_data" {
  bucket = aws_s3_bucket.secure_data.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

# Enable encryption
resource "aws_s3_bucket_server_side_encryption_configuration" "secure_data" {
  bucket = aws_s3_bucket.secure_data.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm     = "aws:kms"
      kms_master_key_id = aws_kms_key.s3.arn
    }
  }
}

# Enable versioning
resource "aws_s3_bucket_versioning" "secure_data" {
  bucket = aws_s3_bucket.secure_data.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

### Network Security

```hcl
# Security group with minimal access
resource "aws_security_group" "app" {
  name        = "${var.environment}-app-sg"
  description = "Application security group"
  vpc_id      = aws_vpc.main.id

  # Explicit ingress rules only
  ingress {
    description     = "HTTPS from ALB"
    from_port       = 443
    to_port         = 443
    protocol        = "tcp"
    security_groups = [aws_security_group.alb.id]
  }

  # Explicit egress (avoid 0.0.0.0/0 if possible)
  egress {
    description = "HTTPS to external APIs"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Restrict if possible
  }

  tags = merge(local.common_tags, {
    Name = "${var.environment}-app-sg"
  })
}
```

## Compliance Tagging

Track compliance requirements with tags:

```hcl
locals {
  compliance_tags = {
    Compliance     = var.compliance_framework  # e.g., "SOC2", "HIPAA"
    DataClass      = var.data_classification   # e.g., "confidential", "public"
    BackupRequired = var.backup_required       # "true" or "false"
    Encrypted      = "true"
  }

  common_tags = merge(local.base_tags, local.compliance_tags)
}
```

## Audit Logging

Enable comprehensive logging:

```hcl
# AWS CloudTrail
resource "aws_cloudtrail" "main" {
  name                          = "${var.environment}-audit-trail"
  s3_bucket_name                = aws_s3_bucket.audit_logs.id
  include_global_service_events = true
  is_multi_region_trail         = true
  enable_log_file_validation    = true

  event_selector {
    read_write_type           = "All"
    include_management_events = true
  }
}

# Cloudflare Audit Logs
resource "cloudflare_logpush_job" "audit" {
  enabled          = true
  name             = "${var.environment}-audit-logs"
  dataset          = "audit_logs"
  destination_conf = "s3://cloudflare-audit-logs/..."
}
```

## Vulnerability Scanning

Include security scanning in CI/CD:

```yaml
# .github/workflows/terraform.yml
- name: Run tfsec
  uses: aquasecurity/tfsec-action@v1.0.0
  with:
    soft_fail: false

- name: Run checkov
  uses: bridgecrewio/checkov-action@master
  with:
    directory: .
    framework: terraform
```

## Data Encryption

Encrypt sensitive data at rest and in transit:

```hcl
# RDS encryption
resource "aws_db_instance" "main" {
  storage_encrypted = true
  kms_key_id        = aws_kms_key.rds.arn
  # ...
}

# EBS encryption
resource "aws_ebs_volume" "data" {
  encrypted  = true
  kms_key_id = aws_kms_key.ebs.arn
  # ...
}

# Force SSL for RDS
resource "aws_db_parameter_group" "main" {
  parameter {
    name  = "rds.force_ssl"
    value = "1"
  }
}
```

## Checklist for New Resources

Before creating resources, verify:

- [ ] No hardcoded secrets or credentials
- [ ] Sensitive outputs marked as `sensitive = true`
- [ ] IAM policies follow least privilege
- [ ] Encryption enabled where applicable
- [ ] Public access blocked unless explicitly required
- [ ] Audit logging enabled
- [ ] Compliance tags applied
- [ ] Security groups have minimal required access
- [ ] Resource names don't contain sensitive info
