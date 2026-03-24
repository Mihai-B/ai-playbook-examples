---
name: deploy-checklist
description: "Run a pre-deployment checklist before releasing to production. Use when deploying, releasing, or preparing a production build."
---
# Deploy Checklist

Run through a structured pre-deployment checklist to catch issues before they reach production.

## When to Use

- Before merging to main/production branch
- Before creating a release tag
- When preparing a hotfix deployment

## Procedure

1. **Verify tests pass** — Run the full test suite (`npm run test`)
2. **Check for lint errors** — Run `npm run lint`
3. **Review environment config** — Confirm all required env vars are set for production
4. **Check database migrations** — Ensure all migrations are committed and tested
5. **Review security** — Check for exposed secrets, missing input validation
6. **Verify build** — Run `npm run build` and confirm no errors
7. **Check dependencies** — Run `npm audit` for known vulnerabilities
8. **Review the checklist** — Go through [detailed checklist items](./references/checklist.md)

## Output

Produce a deployment readiness report:

```
## Deployment Readiness Report

| Check              | Status | Notes          |
|--------------------|--------|----------------|
| Tests              | PASS   |                |
| Lint               | PASS   |                |
| Env Config         | PASS   |                |
| Migrations         | PASS   |                |
| Security           | WARN   | 1 advisory     |
| Build              | PASS   |                |
| Dependencies       | PASS   |                |

**Recommendation**: Ready to deploy / Needs attention
```
