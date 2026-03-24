---
name: deploy-checklist
description: Run a pre-deployment checklist for .NET applications before releasing to production. Use when deploying, releasing, creating a release tag, or preparing a production build.
---

# Deploy Checklist

Run through a structured pre-deployment checklist to catch issues before they reach production.

## When to Use

- Before merging to main/production branch
- Before creating a release tag
- When preparing a hotfix deployment

## Procedure

1. **Verify tests pass** — Run `dotnet test` across the full solution
2. **Check for build warnings** — Run `dotnet build -warnaserror` and resolve all warnings
3. **Review environment config** — Confirm all required config values are set for production via `appsettings.Production.json` and environment variables
4. **Check database migrations** — Ensure all migrations are committed, generate an idempotent script: `dotnet ef migrations script --idempotent`
5. **Review security** — Check for exposed secrets, missing input validation, disabled auth
6. **Verify Docker build** — Run `docker build .` and confirm the image builds successfully
7. **Check dependencies** — Run `dotnet list package --vulnerable` for known vulnerabilities
8. **Review the checklist** — Go through [detailed checklist items](references/checklist.md)

## Output

Produce a deployment readiness report:

```
## Deployment Readiness Report

| Check              | Status | Notes          |
|--------------------|--------|----------------|
| Tests              | PASS   |                |
| Build (warnings)   | PASS   |                |
| Environment Config | PASS   |                |
| Migrations         | PASS   |                |
| Security           | WARN   | 1 advisory     |
| Docker Build       | PASS   |                |
| Dependencies       | PASS   |                |

**Recommendation**: Ready to deploy / Needs attention
```
