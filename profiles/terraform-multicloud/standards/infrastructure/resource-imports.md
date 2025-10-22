## Terraform resource import standards

### Import Strategy

**When to Import vs Recreate:**
- **Import**: Existing production resources, resources with complex state, resources that cannot have downtime
- **Recreate**: Test/dev resources, simple resources, resources that are easily replaceable
- **Never Import**: Secrets, temporary resources, resources scheduled for deprecation

**Import Prerequisites:**
- Resource already exists in cloud provider
- You have sufficient permissions to read resource configuration
- Resource type is supported by Terraform provider
- You understand the resource's current configuration and dependencies

### Import Workflow

**1. Identify Resource to Import**
```bash
# Document the resource
# Resource Type: aws_s3_bucket
# Cloud ID: legacy-data-bucket-prod
# Purpose: Production data storage for customer uploads
# Owner: Platform Team
```

**2. Review Naming Conventions**
Before writing Terraform configuration, consult `@agent-os/standards/global/naming-conventions.md`:
- Ensure resource name follows project conventions
- Verify resource matches environment strategy (`@agent-os/standards/global/environment-strategy.md`)
- Check if resource tags/labels meet standards
- Confirm naming aligns with module design patterns

**3. Write Terraform Configuration**
```hcl
# Write configuration BEFORE importing
# This ensures imported resource immediately conforms to standards

resource "aws_s3_bucket" "customer_uploads_prod" {
  # Name follows convention: {purpose}_{environment}
  bucket = "customer-uploads-prod"

  # Tags follow project standards
  tags = {
    Environment = "production"
    ManagedBy   = "terraform"
    Owner       = "platform-team"
    Purpose     = "customer-uploads"
  }
}
```

**4. Generate Import Block**
```hcl
# Terraform 1.5+ import block (preferred)
import {
  to = aws_s3_bucket.customer_uploads_prod
  id = "legacy-data-bucket-prod"
}
```

**5. Run Import**
```bash
# Using import block (Terraform 1.5+)
terraform plan -generate-config-out=generated.tf  # Preview what will be imported
terraform apply  # Execute import

# Or using CLI (legacy)
terraform import aws_s3_bucket.customer_uploads_prod legacy-data-bucket-prod
```

**6. Reconcile Configuration**
```bash
# After import, compare actual vs desired configuration
terraform plan

# You'll likely see differences:
# - Missing tags
# - Non-standard naming (in cloud, can't always rename)
# - Configuration drift
```

**7. Apply Standardization**
```bash
# IMPORTANT: Review changes carefully
# Some changes may require downtime or cause disruption

terraform apply

# Common standardization changes:
# - Add required tags
# - Enable logging/monitoring
# - Apply security policies
# - Configure backup settings
```

### Handling Non-Compliant Names

**Scenario**: Imported resource has non-standard name in cloud provider

```hcl
# Cloud resource name: legacy-data-bucket-prod (non-compliant)
# Cannot rename S3 buckets after creation

resource "aws_s3_bucket" "customer_uploads_prod" {
  # Keep existing cloud name (immutable)
  bucket = "legacy-data-bucket-prod"

  # Use compliant Terraform resource name
  # Document the discrepancy

  tags = {
    # Use tag to indicate standard name
    StandardName = "customer-uploads-prod"
    LegacyName   = "legacy-data-bucket-prod"
    Environment  = "production"
    ManagedBy    = "terraform"
  }
}
```

**Document Exception:**
```hcl
# NOTE: Bucket name "legacy-data-bucket-prod" predates naming standards
# Keeping existing name to avoid data migration
# Standard name would be: customer-uploads-prod
# Tracked in: INFRA-123
```

### Standardization Checklist

After importing, ensure resource meets all standards:

**Naming (`@agent-os/standards/global/naming-conventions.md`):**
- [ ] Terraform resource name follows convention
- [ ] Cloud resource name follows convention (or exception documented)
- [ ] Resource matches environment naming pattern

**Tagging/Labeling:**
- [ ] All required tags present (Environment, ManagedBy, Owner, etc.)
- [ ] Tags follow project tag standards
- [ ] Cost allocation tags applied

**Security (`@agent-os/standards/global/security-compliance.md`):**
- [ ] Encryption enabled where applicable
- [ ] Access controls follow least privilege
- [ ] Security group rules reviewed
- [ ] Audit logging enabled

**State Management (`@agent-os/standards/global/state-management.md`):**
- [ ] Resource in correct state workspace
- [ ] State backend configured properly
- [ ] Resource added to appropriate module (if applicable)

**Monitoring:**
- [ ] CloudWatch/monitoring enabled
- [ ] Alerts configured
- [ ] Logging enabled and centralized

### Import in Modules

**Importing into Module:**
```bash
# Import into module instance
terraform import 'module.vpc.aws_vpc.main' vpc-abc123

# For module with count
terraform import 'module.database[0].aws_db_instance.main' db-instance-id

# For module with for_each
terraform import 'module.database["prod"].aws_db_instance.main' db-instance-id
```

**Module Design Consideration:**
- Imported resources should fit module's interface
- May need to refactor module to accommodate existing resource patterns
- Document any compromises made for legacy resources

### Bulk Imports

**Strategy for Importing Multiple Resources:**

1. **Create Import Plan:**
   ```
   # import-plan.md
   ## Batch 1: Networking (Low Risk)
   - VPC: vpc-abc123
   - Subnets: subnet-1, subnet-2
   - Route Tables: rtb-1, rtb-2

   ## Batch 2: Compute (Medium Risk)
   - EC2 instances in dev environment

   ## Batch 3: Data (High Risk - Schedule Maintenance)
   - RDS databases
   - S3 buckets with data
   ```

2. **Use Scripting for Bulk:**
   ```bash
   #!/bin/bash
   # bulk-import.sh

   # Import VPCs
   for vpc_id in vpc-abc123 vpc-def456; do
     resource_name="vpc_${vpc_id//-/_}"
     terraform import "aws_vpc.${resource_name}" "$vpc_id"
   done
   ```

3. **Validate After Each Batch:**
   ```bash
   terraform plan  # Should show 0 changes if import was correct
   ```

### Import Testing

**Test Import in Non-Prod First:**
```bash
# 1. Import similar resource in dev environment
cd environments/dev
terraform import aws_s3_bucket.test_import test-bucket-dev

# 2. Verify standardization works
terraform plan
terraform apply

# 3. Document lessons learned
# 4. Apply to production with confidence
```

### Troubleshooting Imports

**Import Fails:**
```bash
# Error: resource already managed
# Solution: Check if resource already in state
terraform state list | grep resource_name

# Error: resource not found
# Solution: Verify resource ID and permissions
aws s3 ls  # Verify resource exists
```

**State Conflicts:**
```bash
# Remove from state if needed (careful!)
terraform state rm aws_s3_bucket.old_name

# Re-import with correct configuration
terraform import aws_s3_bucket.new_name bucket-id
```

**Configuration Drift After Import:**
```bash
# Large diff after import is normal
# Review each change:
# - Required changes (tags, security)
# - Optional changes (can defer)
# - Breaking changes (need maintenance window)

# Apply in stages if needed
terraform apply -target=aws_s3_bucket.customer_uploads_prod.tags
```

### Documentation Requirements

**Document Every Import:**
```markdown
## Import Record

**Date:** 2024-10-22
**Resource:** aws_s3_bucket.customer_uploads_prod
**Cloud ID:** legacy-data-bucket-prod
**Reason:** Migrating legacy infrastructure to Terraform
**Operator:** @username
**PR:** #123

**Deviations from Standards:**
- Bucket name doesn't follow convention (immutable resource)
- Missing encryption (will enable in follow-up PR #124)

**Follow-up Actions:**
- [ ] Enable versioning (INFRA-125)
- [ ] Configure lifecycle rules (INFRA-126)
- [ ] Set up cross-region replication (INFRA-127)
```

### CI/CD Considerations

**Import in CI/CD:**
- Imports should be done manually, not in CI/CD pipeline
- After import, commit configuration changes through normal PR process
- Use `terraform plan` in CI to verify no unexpected changes
- Document imports in PR description

**Preventing Accidental Imports:**
```hcl
# Use import blocks to make imports explicit and reviewable
import {
  to = aws_s3_bucket.customer_uploads_prod
  id = "legacy-data-bucket-prod"
}

# This appears in git diff and can be reviewed
```

### Best Practices

1. **Import Early, Standardize Gradually:**
   - Import first to establish Terraform management
   - Apply standards incrementally to minimize risk
   - Some changes (encryption, monitoring) may require downtime

2. **One Resource at a Time:**
   - Import and validate each resource individually
   - Easier to troubleshoot issues
   - Clearer audit trail

3. **Review Before Import:**
   - Understand resource dependencies
   - Check for resources that reference this one
   - Plan standardization changes

4. **Use Import Blocks (Terraform 1.5+):**
   - More declarative and reviewable
   - Better for team workflows
   - Easier to track in version control

5. **Test Plan After Import:**
   - `terraform plan` should show only expected changes
   - Unexpected changes indicate configuration mismatch
   - Address all discrepancies before moving to production

6. **Coordinate with Team:**
   - Notify team before importing production resources
   - Schedule imports during maintenance windows if standardization requires changes
   - Document in team chat/wiki

### Related Standards

- `@agent-os/standards/global/naming-conventions.md` - Naming standards for resources
- `@agent-os/standards/global/state-management.md` - State handling and workspaces
- `@agent-os/standards/global/environment-strategy.md` - Environment separation patterns
- `@agent-os/standards/infrastructure/module-design.md` - Module structure for imported resources
- `@agent-os/standards/global/security-compliance.md` - Security requirements for resources
