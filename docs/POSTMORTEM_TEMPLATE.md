# Deployment Failure Postmortem

## Incident Summary

| Field | Value |
|---|---|
| **Date** | YYYY-MM-DD |
| **Duration** | Start time — End time (total minutes) |
| **Severity** | Low / Medium / High / Critical |
| **Affected Resources** | e.g., S3 bucket, DynamoDB table |
| **Detected By** | CI pipeline / CD pipeline / monitoring / manual |
| **Resolved By** | @github-username |

## Timeline

| Time | Event |
|---|---|
| HH:MM | PR merged to main |
| HH:MM | CD pipeline started |
| HH:MM | Issue detected (describe how) |
| HH:MM | Rollback initiated |
| HH:MM | Service restored |

## Root Cause

_Describe what caused the failure. Be specific — reference the exact commit, configuration change, or external factor._

## Impact

_What was affected? Did users experience downtime? Was data at risk?_

## Resolution

_What was done to fix it? Include the specific steps, commands, or rollback procedure._

```bash
# Example rollback commands
git revert <commit-hash>
git push origin main
# Wait for CD pipeline, approve deployment
```

## Lessons Learned

### What Went Well

- e.g., CI pipeline caught the issue before deploy
- e.g., Rollback procedure worked as documented

### What Went Wrong

- e.g., Missing validation for edge case
- e.g., No alerting on the affected resource

## Action Items

| Action | Owner | Due Date | Status |
|---|---|---|---|
| Add validation for X | @username | YYYY-MM-DD | Open |
| Update monitoring for Y | @username | YYYY-MM-DD | Open |
| Add test case for Z | @username | YYYY-MM-DD | Open |
