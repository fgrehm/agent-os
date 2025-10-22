# Communication Style

## Principle

Infrastructure projects require **technical, direct communication**. Avoid marketing jargon and corporate buzzwords. Focus on concrete, measurable impact.

## Language Guidelines

### ✅ Use Technical Language

**Good:**
- "This change reduces monthly AWS costs by $500"
- "RDS multi-AZ deployment improves availability to 99.95%"
- "Encryption adds 2-3ms latency per request"
- "Auto-scaling handles traffic spikes up to 10x baseline"
- "This creates a security risk: unencrypted data at rest"

**Avoid:**
- "This leverages cloud-native best practices to drive value"
- "Synergizing our infrastructure to move the needle"
- "Circling back on our strategic cloud initiatives"
- "Stakeholder alignment on our digital transformation"

### Business Impact (When Needed)

Discuss business impact in **concrete, measurable terms**:

**✅ Concrete Impact:**
- "Reduces infrastructure costs by 30% ($2,000/month)"
- "Improves page load time from 3s to 800ms"
- "Prevents 99.9% of SQL injection attempts"
- "Supports up to 100,000 concurrent users"
- "Meets SOC2 compliance requirements for encryption"
- "Reduces deployment time from 45 minutes to 5 minutes"

**❌ Vague Buzzwords:**
- "Optimizes the customer journey"
- "Drives innovation across the platform"
- "Delivers transformational value"
- "Enables digital excellence"
- "Maximizes ROI on cloud investments"

### Specifications and Documentation

**Technical Focus:**
```markdown
# Spec: Multi-AZ RDS Deployment

## Problem
Database has single point of failure. Outages cause application downtime.

## Solution
Deploy RDS in multi-AZ configuration with automatic failover.

## Impact
- Availability: 99.5% → 99.95%
- Failover time: ~15 minutes (manual) → ~2 minutes (automatic)
- Cost increase: $150/month
- Maintenance windows: No longer require downtime

## Technical Details
- Primary instance: us-east-1a
- Standby instance: us-east-1b
- Automatic failover on AZ failure
- Synchronous replication
```

**Not This:**
```markdown
# Spec: Database Excellence Initiative

## Vision
Leverage cutting-edge database technology to deliver best-in-class
uptime and drive customer satisfaction.

## Strategy
Synergize our data layer with industry-leading practices to maximize
platform resilience and optimize stakeholder value.
```

### Code Comments

**Good:**
```hcl
# Force SSL for database connections (security requirement)
resource "aws_db_parameter_group" "main" {
  parameter {
    name  = "rds.force_ssl"
    value = "1"
  }
}

# Production requires larger instances for peak traffic
instance_type = terraform.workspace == "prod" ? "t3.large" : "t3.small"
```

**Avoid:**
```hcl
# Leveraging enterprise-grade security protocols
resource "aws_db_parameter_group" "main" {
  # ...
}

# Optimizing our infrastructure to drive business value
instance_type = terraform.workspace == "prod" ? "t3.large" : "t3.small"
```

### Commit Messages

**Technical and Specific:**
```
feat(rds): enable multi-AZ for production database

- Configures automatic failover
- Improves availability to 99.95%
- Adds $150/month to AWS bill

Addresses #123
```

**Not:**
```
feat: enhance database capabilities

Leveraging cloud-native best practices to optimize our data layer
and drive operational excellence across the platform.
```

### Issue/PR Descriptions

**Problem-Solution Format:**
```markdown
## Problem
S3 bucket allows public access. Security audit flagged as high risk.

## Solution
- Enable public access block on all S3 buckets
- Add bucket policy denying non-SSL requests
- Enable versioning for data recovery

## Impact
- Security: Eliminates public exposure risk
- Compliance: Meets SOC2 requirement 3.4.2
- Cost: No change

## Testing
- tfsec passes all S3 security checks
- Manual verification: aws s3api get-public-access-block
```

**Not:**
```markdown
## Overview
Synergizing our storage infrastructure with industry-leading security
paradigms to maximize data protection and drive stakeholder confidence.
```

## Buzzwords to Avoid

Common business jargon that doesn't belong in infrastructure projects:

- ❌ Leverage, utilize (just say "use")
- ❌ Synergy, synergize
- ❌ Move the needle, game-changer
- ❌ Circle back, touch base
- ❌ Low-hanging fruit
- ❌ Boil the ocean
- ❌ Drink our own champagne
- ❌ Think outside the box
- ❌ Best-in-class, world-class
- ❌ Digital transformation
- ❌ Paradigm shift
- ❌ Value-add, value proposition
- ❌ Strategic initiative
- ❌ Core competency

## When Business Context Matters

**Appropriate business discussion:**
- Cost analysis: "This reduces AWS spend by 25%"
- Availability requirements: "SLA requires 99.9% uptime"
- Compliance: "HIPAA requires encryption at rest"
- Performance: "API response time target is <200ms"
- Capacity: "Support Black Friday traffic (50x normal load)"

**Frame in terms of requirements and constraints**, not abstract business value.

## Examples by Role

### infrastructure-engineer
**Good:** "Created VPC with 3 AZs for high availability. NAT Gateways cost $96/month."

**Avoid:** "Leveraged cloud-native networking to drive infrastructure excellence."

### security-engineer
**Good:** "Enabled MFA for S3 delete operations. Prevents accidental data loss."

**Avoid:** "Optimized our security posture to maximize data protection value."

### devops-engineer
**Good:** "GitHub Actions runs tests on every PR. Catches bugs before deployment."

**Avoid:** "Implemented CI/CD to accelerate our digital transformation journey."

### environment-engineer
**Good:** "Workspaces separate dev/staging/prod. Prevents cross-environment mistakes."

**Avoid:** "Architected environment strategy to enable agile delivery excellence."

## Summary

**Write like an engineer, not a consultant.**

- Be specific and measurable
- Focus on technical accuracy
- Discuss impact in concrete terms (cost, time, reliability, security)
- Avoid buzzwords and jargon
- Use simple, direct language
