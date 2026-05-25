# Contributing Guide

## Commit Conventions

This repository uses [Conventional Commits](https://www.conventionalcommits.org/). Every commit message and PR title must follow the format:

```
type: short description
```

### Allowed Types

| Type | When to Use | Example |
|---|---|---|
| `feat` | New infrastructure resource | `feat: add CloudWatch alarm for DynamoDB throttling` |
| `fix` | Bug fix or misconfiguration | `fix: correct S3 bucket policy condition` |
| `docs` | Documentation only | `docs: update architecture diagram` |
| `chore` | Maintenance, dependencies | `chore: update Terraform provider to 5.x` |
| `refactor` | Code restructuring, no behavior change | `refactor: extract S3 lifecycle rule into local` |
| `ci` | CI/CD workflow changes | `ci: add tfsec to pull request checks` |
| `test` | Adding or updating tests | `test: add validation for DynamoDB attribute types` |
| `perf` | Performance improvements | `perf: enable S3 transfer acceleration` |

The commit lint workflow validates PR titles automatically. PRs with non-conforming titles will fail the check and cannot be merged.

## Pull Request Process

1. Create a feature branch from `main`:
   ```bash
   git checkout -b feat/your-feature-name
   ```
2. Make changes and commit with conventional messages
3. Push and open a PR with a conventional title (e.g., `feat: enable TTL on DynamoDB table`)
4. CI pipeline runs automatically — all checks must pass before merge is allowed
5. Get at least one approval from a reviewer
6. Squash merge to `main` — the PR title becomes the commit message, enforcing conventions automatically
7. CD pipeline runs and waits for production approval before deploying

## CI Pipeline Checks

Every PR must pass these checks before merge:

- **Terraform Format** — `terraform fmt -check` ensures consistent formatting
- **Terraform Validate** — `terraform validate` catches syntax and type errors
- **Security Scan (tfsec)** — flags misconfigurations such as missing encryption or public access
- **Terraform Plan** — plan output is posted as a PR comment so reviewers can see the exact diff

All four jobs run in parallel; `plan` waits for the first three to complete before running.

## Deployment

Merges to `main` that touch `terraform/**` trigger the CD pipeline:

1. Automatic `terraform plan` (pre-deploy)
2. **Manual approval required** in the `production` GitHub environment
3. `terraform apply` executes only after a reviewer approves

To approve a pending deployment, go to **Actions → CD Pipeline → Review deployments**.

## Security

- Never commit `.tfvars` files with real values — use `terraform.tfvars.example` as a template
- Store AWS credentials as GitHub Secrets (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
- All S3 buckets must have server-side encryption (`AES256`) and public access blocks enabled
- DynamoDB tables must enable server-side encryption and point-in-time recovery

## Versioning

This repo uses [release-please](https://github.com/googleapis/release-please) for automated semantic versioning. When PRs with conventional commit titles are merged, release-please opens a Release PR that bumps `version.txt` and updates `CHANGELOG.md`. Merging the Release PR creates a GitHub Release and tag.

| Commit Type | Version Bump |
|---|---|
| `feat` | minor (e.g., 1.0.0 → 1.1.0) |
| `fix`, `perf` | patch (e.g., 1.0.0 → 1.0.1) |
| `feat!` or `BREAKING CHANGE` footer | major (e.g., 1.0.0 → 2.0.0) |
| `docs`, `chore`, `ci`, `refactor`, `test` | no version bump |
