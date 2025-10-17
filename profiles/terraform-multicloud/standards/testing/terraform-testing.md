# Terraform Testing

## Testing Levels

1. **Static Analysis** - Syntax, formatting, security (no deployment)
2. **Unit Tests** - Individual modules in isolation
3. **Integration Tests** - Modules working together
4. **Smoke Tests** - Post-deployment validation

## Static Analysis

### Basic Validation

```bash
# Format check
terraform fmt -check -recursive

# Syntax validation (no backend)
terraform init -backend=false
terraform validate
```

### tfsec (Security)

```bash
brew install tfsec
tfsec terraform/
```

**Config:**
```yaml
# tfsec.yml
severity_overrides:
  aws-s3-enable-bucket-logging: WARNING
  aws-ec2-enable-at-rest-encryption: ERROR
```

### Checkov (Compliance)

```bash
pip install checkov
checkov --directory terraform/

# Skip specific checks
checkov -d terraform/ --skip-check CKV_AWS_18,CKV_AWS_19
```

### TFLint (Best Practices)

```bash
brew install tflint
tflint --init
tflint --recursive
```

**Config:**
```hcl
# .tflint.hcl
plugin "aws" {
  enabled = true
  source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

rule "terraform_naming_convention" {
  enabled = true
}
```

**Run all three** for comprehensive static analysis.

## Unit Testing

### Terratest (Go)

```go
// test/vpc_test.go
package test

import (
	"testing"
	"github.com/gruntwork-io/terratest/modules/terraform"
	"github.com/stretchr/testify/assert"
)

func TestVPCModule(t *testing.T) {
	t.Parallel()

	terraformOptions := &terraform.Options{
		TerraformDir: "../modules/aws-vpc-network",
		Vars: map[string]interface{}{
			"environment": "test",
			"vpc_cidr":    "10.99.0.0/16",
		},
	}

	defer terraform.Destroy(t, terraformOptions)
	terraform.InitAndApply(t, terraformOptions)

	// Test outputs
	vpcID := terraform.Output(t, terraformOptions, "vpc_id")
	assert.NotEmpty(t, vpcID)

	subnets := terraform.OutputList(t, terraformOptions, "private_subnet_ids")
	assert.Equal(t, 2, len(subnets))
}
```

**Run:** `go test -v -timeout 30m`

### Terraform Test (Native)

```hcl
# tests/vpc.tftest.hcl
run "vpc_created" {
  command = apply

  variables {
    environment = "test"
    vpc_cidr    = "10.99.0.0/16"
  }

  assert {
    condition     = length(aws_subnet.private) == 2
    error_message = "Expected 2 private subnets"
  }
}
```

**Run:** `terraform test`

## Integration Testing

### Module Examples

Every module should have a working example:

```hcl
# modules/aws-vpc-network/examples/complete/main.tf
module "vpc_example" {
  source = "../.."

  environment = "test"
  vpc_cidr    = "10.99.0.0/16"
}
```

**Test script:**
```bash
#!/bin/bash
for example in modules/*/examples/*; do
  cd "$example"
  terraform init && terraform plan
  cd -
done
```

### Policy Testing (OPA)

```rego
# policies/aws_s3_encryption.rego
package terraform.aws.s3

deny[msg] {
  resource := input.resource.aws_s3_bucket[name]
  not resource.server_side_encryption_configuration

  msg := sprintf("S3 bucket '%s' must have encryption", [name])
}
```

**Run with conftest:**
```bash
brew install conftest
conftest test terraform/plan.json -p policies/
```

## Smoke Testing

Post-deployment validation:

```bash
#!/bin/bash
# smoke-test.sh
WORKSPACE=${1:-$(terraform workspace show)}

echo "Running smoke tests for workspace: $WORKSPACE"

# Check S3 bucket
aws s3 ls "s3://$WORKSPACE-myapp-assets" > /dev/null
echo "✓ S3 accessible"

# Check DNS
dig "$WORKSPACE.myapp.com" | grep -q "ANSWER: 1"
echo "✓ DNS resolves"

# Check Heroku app
curl -s "https://myapp-$WORKSPACE.herokuapp.com/health" | grep -q "ok"
echo "✓ App healthy"

echo "All smoke tests passed for $WORKSPACE!"
```

Run after every deployment: `./smoke-test.sh` (auto-detects workspace) or `./smoke-test.sh prod`

## Test Environments

Use isolated test infrastructure:

```hcl
# test/fixtures/main.tf
locals {
  test_env = "test-${random_id.test.hex}"
}

resource "random_id" "test" {
  byte_length = 4
}

module "vpc_under_test" {
  source = "../../modules/aws-vpc-network"
  environment = local.test_env
  vpc_cidr    = "10.99.0.0/16"
}
```

Ensures tests don't interfere with each other.

## CI/CD Integration

```yaml
# .github/workflows/test.yml
name: Terraform Tests

on:
  pull_request:
    paths: ['terraform/**', 'test/**']

jobs:
  static-analysis:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Format Check
        run: terraform fmt -check -recursive

      - name: Validate
        working-directory: terraform
        run: |
          terraform init -backend=false
          for workspace in dev staging prod; do
            terraform workspace select $workspace || terraform workspace new $workspace
            terraform validate
          done

      - name: Security Scan
        uses: aquasecurity/tfsec-action@v1.0.3
        with:
          working_directory: terraform

      - name: Compliance Check
        uses: bridgecrewio/checkov-action@master
        with:
          directory: terraform/
          framework: terraform

  unit-tests:
    runs-on: ubuntu-latest
    needs: static-analysis

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v4

      - name: Run Terratest
        working-directory: test
        run: go test -v -timeout 30m
```

**Run on every PR** to catch issues early.

## Testing Checklist

Before merging changes:

- [ ] `terraform fmt` passes
- [ ] `terraform validate` passes (all environments)
- [ ] `tfsec` passes (no high/critical issues)
- [ ] `checkov` passes (required policies)
- [ ] Module examples can plan successfully
- [ ] Unit tests pass (if using Terratest)
- [ ] Smoke tests pass in test environment
- [ ] Manual review of `terraform plan` output
- [ ] Cost estimate reviewed (if significant changes)

## Testing Strategy Summary

| Test Type | When | Tool | Cost |
|-----------|------|------|------|
| Format | Every commit | terraform fmt | Free |
| Validate | Every commit | terraform validate | Free |
| Security | Every PR | tfsec | Free |
| Compliance | Every PR | checkov | Free |
| Linting | Every PR | tflint | Free |
| Unit | Before merge | Terratest | AWS costs |
| Integration | Weekly | Terratest | AWS costs |
| Smoke | After deploy | Custom scripts | Free |

**Minimize costs:** Run expensive tests (Terratest) only when necessary.
