# CI/CD Best Practices — Terraform Infrastructure

![CI Pipeline](https://github.com/EricBorba/ce-lab-cicd-best-practices/actions/workflows/ci.yml/badge.svg)
![CD Pipeline](https://github.com/EricBorba/ce-lab-cicd-best-practices/actions/workflows/cd.yml/badge.svg)
![Commit Lint](https://github.com/EricBorba/ce-lab-cicd-best-practices/actions/workflows/commit-lint.yml/badge.svg)
![Release](https://github.com/EricBorba/ce-lab-cicd-best-practices/actions/workflows/release.yml/badge.svg)

## Architecture

This repository manages shared infrastructure resources:

- **S3 Bucket** — Versioned artifact storage with AES256 encryption and lifecycle policies (transitions to STANDARD_IA after 90 days)
- **DynamoDB Table** — Application state store with point-in-time recovery, encryption at rest, and TTL support

## CI/CD Workflows

| Workflow | Trigger | Purpose |
|---|---|---|
| CI Pipeline | PR to `main` | Format check, validate, tfsec security scan, plan |
| CD Pipeline | Push to `main` | Plan + deploy with manual approval gate |
| Commit Lint | PR open/edit | Enforce conventional commit titles |
| Release Please | Push to `main` | Automated semantic versioning and changelog |

### CI Pipeline

Every pull request targeting `main` runs four parallel jobs:

1. **Terraform Format** — `terraform fmt -check -recursive -diff`
2. **Terraform Validate** — `terraform validate` with provider cache
3. **Security Scan (tfsec)** — static analysis for misconfigurations (soft fail)
4. **Terraform Plan** — runs after all three pass; posts results as a PR comment

![CI pipeline all checks passing](screenshots/01-ci-pipeline-all-checks-passing.png)

The plan output is automatically posted as a comment on the PR so reviewers can see exactly what will change before approving.

![PR showing CI results comment and checks passed](screenshots/02-pr-ci-results-comment-ready-to-merge.png)

### CD Pipeline

Merges to `main` that touch `terraform/**` trigger the CD pipeline:

1. **Plan (Pre-Deploy)** — runs `terraform plan` and saves the plan artifact
2. **Deploy to Production** — pauses and waits for manual approval in the `production` GitHub environment before running `terraform apply`

![CD pipeline waiting for production approval](screenshots/03-cd-pipeline-waiting-production-approval.png)

![CD pipeline completed after approval](screenshots/04-cd-pipeline-deploy-to-production-success.png)

### Release Please

When a PR with a conventional commit title is merged, `release-please` opens a Release PR that bumps `version.txt` and updates `CHANGELOG.md`. Merging the Release PR creates a GitHub Release with a version tag.

![Release PR created by release-please bot](screenshots/07-release-please-release-pr-created.png)

![Release Please workflow completing successfully](screenshots/08-release-please-release-finalized-after-merge.png)

## Quick Start

```bash
cd terraform
terraform init
terraform plan
terraform apply
```

## Required GitHub Secrets

| Secret | Description |
|---|---|
| `AWS_ACCESS_KEY_ID` | IAM user access key with permissions to manage S3 and DynamoDB |
| `AWS_SECRET_ACCESS_KEY` | Corresponding IAM secret key |

## Required GitHub Environment

The CD workflow uses a `production` environment with **Required reviewers** enabled. Set it up at **Settings → Environments → New environment** and add yourself as a required reviewer.

![GitHub Actions workflow permissions settings](screenshots/05-github-actions-workflow-permissions-settings.png)

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for commit conventions, PR process, and CI/CD guidelines.
