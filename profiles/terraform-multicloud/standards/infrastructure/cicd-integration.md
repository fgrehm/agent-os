# CI/CD Integration

## GitHub Actions - Pull Request Workflow

```yaml
# .github/workflows/terraform-pr.yml
name: Terraform PR Check

on:
  pull_request:
    paths: ['terraform/**']

permissions:
  contents: read
  pull-requests: write

jobs:
  terraform-check:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        workspace: [dev, staging, prod]

    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
      HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}

    steps:
      - uses: actions/checkout@v4

      - uses: hashicorp/setup-terraform@v3
        with:
          terraform_version: 1.5.0

      - name: Terraform Format
        working-directory: terraform
        run: terraform fmt -check -recursive

      - name: Terraform Init
        working-directory: terraform
        run: terraform init

      - name: Select Workspace
        working-directory: terraform
        run: |
          terraform workspace select ${{ matrix.workspace }} || terraform workspace new ${{ matrix.workspace }}

      - name: Terraform Validate
        working-directory: terraform
        run: terraform validate

      - name: Terraform Plan
        working-directory: terraform
        run: terraform plan -var-file=${{ matrix.workspace }}.tfvars -no-color -input=false

      - name: Comment PR (optional - use github-script action)
        # Post plan output to PR comments
```

**Key steps:** fmt → init → workspace select → validate → plan

## Deployment Workflow

```yaml
# .github/workflows/terraform-deploy.yml
name: Terraform Deploy

on:
  push:
    branches: [main]
    paths: ['terraform/**']
  workflow_dispatch:
    inputs:
      workspace:
        description: 'Workspace to deploy'
        type: choice
        options: [dev, staging, prod]
        required: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ github.event.inputs.workspace || 'dev' }}

    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
      HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
      TF_WORKSPACE: ${{ github.event.inputs.workspace || 'dev' }}

    steps:
      - uses: actions/checkout@v4
      - uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        working-directory: terraform
        run: terraform init

      - name: Select Workspace
        working-directory: terraform
        run: terraform workspace select $TF_WORKSPACE

      - name: Terraform Plan
        working-directory: terraform
        run: terraform plan -var-file=$TF_WORKSPACE.tfvars -out=tfplan

      - name: Terraform Apply
        working-directory: terraform
        run: terraform apply -auto-approve tfplan
```

**Key pattern:** init → workspace select → plan → apply (with saved plan file)

## Security Scanning

### tfsec (Infrastructure Security)

```yaml
# Add to PR workflow
- name: Run tfsec
  uses: aquasecurity/tfsec-action@v1.0.3
  with:
    working_directory: terraform
    soft_fail: false
```

### Checkov (Policy Compliance)

```yaml
- name: Run Checkov
  uses: bridgecrewio/checkov-action@master
  with:
    directory: terraform/
    framework: terraform
```

### Trivy (Misconfigurations)

```yaml
- name: Run Trivy
  uses: aquasecurity/trivy-action@master
  with:
    scan-type: 'config'
    scan-ref: 'terraform/'
```

**Run all three** for comprehensive coverage.

## Drift Detection

```yaml
# .github/workflows/drift-detection.yml
name: Drift Detection

on:
  schedule:
    - cron: '0 8 * * *'  # Daily at 8 AM

jobs:
  detect-drift:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        workspace: [dev, staging, prod]

    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
      HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}

    steps:
      - uses: actions/checkout@v4
      - uses: hashicorp/setup-terraform@v3

      - name: Terraform Init
        working-directory: terraform
        run: terraform init

      - name: Select Workspace
        working-directory: terraform
        run: terraform workspace select ${{ matrix.workspace }}

      - name: Terraform Plan
        id: plan
        working-directory: terraform
        run: terraform plan -var-file=${{ matrix.workspace }}.tfvars -detailed-exitcode
        continue-on-error: true

      - name: Create Issue on Drift
        if: steps.plan.outputs.exitcode == 2
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: `Drift detected in ${{ matrix.workspace }} workspace`,
              body: `Configuration drift detected in workspace: \`${{ matrix.workspace }}\``,
              labels: ['terraform', 'drift', '${{ matrix.workspace }}']
            })
```

**Key:** Use `-detailed-exitcode` (exit 2 = changes detected)

## Cost Estimation

```yaml
# .github/workflows/cost-estimate.yml
- name: Setup Infracost
  uses: infracost/actions/setup@v2
  with:
    api-key: ${{ secrets.INFRACOST_API_KEY }}

- name: Generate Cost Estimate
  run: |
    infracost breakdown --path=terraform/environments/prod \
      --format=json --out-file=/tmp/infracost.json

- name: Post PR Comment
  uses: infracost/actions/comment@v1
  with:
    path: /tmp/infracost.json
```

Shows cost changes in PRs.

## Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/antonbabenko/pre-commit-terraform
    rev: v1.83.5
    hooks:
      - id: terraform_fmt
      - id: terraform_validate
      - id: terraform_tfsec
```

**Install:** `pip install pre-commit && pre-commit install`

## Environment Protection

**Configure in GitHub:**

1. **Repository → Settings → Environments**
2. **Create environment:** `prod`
3. **Required reviewers:** Add 2+ team members
4. **Wait timer:** 5 minutes
5. **Deployment branches:** `main` only

Prevents accidental production deployments and enforces change control.

## Rollback Strategy

```yaml
# .github/workflows/rollback.yml
on:
  workflow_dispatch:
    inputs:
      environment:
        type: choice
        options: [staging, prod]
      git_ref:
        description: 'Git commit to rollback to'
        required: true

jobs:
  rollback:
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.inputs.git_ref }}

      - name: Terraform Apply Previous State
        run: terraform apply -auto-approve
```

**Alternative:** Restore from S3 state versioning.

## Notifications

```yaml
- name: Notify Slack
  if: always()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

Or use GitHub Discussions/Issues for deployment tracking.

## Secrets Management

**Required secrets in GitHub Actions:**
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `CLOUDFLARE_API_TOKEN`
- `HEROKU_API_KEY`
- `INFRACOST_API_KEY` (optional)
- `SLACK_WEBHOOK` (optional)

**Configure in:** Repository → Settings → Secrets and variables → Actions

**Best practice:** Use OIDC for AWS (no long-lived credentials).

## Workflow Patterns

**Development Workspace:**
- Auto-deploy on merge to `main`
- Run all validation checks on PR
- Workspace: `dev`

**Staging Workspace:**
- Manual approval required
- Deploy after dev validation
- Workspace: `staging`

**Production Workspace:**
- Multiple reviewers required
- Wait timer before deployment
- Deployment window restrictions (optional)
- Workspace: `prod`

**Workspace Safety:**
```yaml
# Prevent accidental workspace selection
- name: Validate Workspace
  run: |
    if [[ "$TF_WORKSPACE" != "dev" && "$TF_WORKSPACE" != "staging" && "$TF_WORKSPACE" != "prod" ]]; then
      echo "Invalid workspace: $TF_WORKSPACE"
      exit 1
    fi
```
