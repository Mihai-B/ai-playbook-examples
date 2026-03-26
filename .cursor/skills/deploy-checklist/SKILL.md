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

## Parameters

- **environment** (optional) — target environment: `staging` or `production` (default: `production`). Determines which config files to verify.

## Context

This is a .NET 8+ application using ASP.NET Core, Entity Framework Core, and Docker. Configuration is managed through `appsettings.{Environment}.json` and environment variables. Database migrations use EF Core code-first. The solution includes unit tests (xUnit) and may include integration tests.

## Task

Run each check in order, recording pass/fail status:

1. **Verify tests pass** — Run `dotnet test` across the full solution
2. **Check for build warnings** — Run `dotnet build -warnaserror` and resolve all warnings
3. **Review environment config** — Confirm all required config values are set for the target environment via `appsettings.{Environment}.json` and environment variables
4. **Check database migrations** — Ensure all migrations are committed, generate an idempotent script: `dotnet ef migrations script --idempotent`
5. **Review security** — Check for exposed secrets, missing input validation, disabled auth
6. **Verify Docker build** — Run `docker build .` and confirm the image builds successfully
7. **Check dependencies** — Run `dotnet list package --vulnerable` for known vulnerabilities
8. **Review the checklist** — Go through [detailed checklist items](references/checklist.md)

## Constraints

- Every check must be executed, not skipped
- Record actual command output for evidence
- Flag any WARN or FAIL items with specific remediation steps
- Do not approve deployment if any Critical items fail

## Examples

Input: `/deploy-checklist environment=staging`

Output:

```
## Deployment Readiness Report — Staging

| Check              | Status | Notes              |
|--------------------|--------|--------------------|
| Tests              | PASS   | 142 passed, 0 failed |
| Build (warnings)   | PASS   |                    |
| Environment Config | PASS   |                    |
| Migrations         | PASS   | 3 pending, script generated |
| Security           | WARN   | 1 NuGet advisory   |
| Docker Build       | PASS   |                    |
| Dependencies       | PASS   |                    |

**Recommendation**: Ready to deploy (1 non-critical advisory)
```

## Output

Produce a deployment readiness report as a markdown table with:
- Each check name, status (PASS / WARN / FAIL), and notes
- A final **Recommendation**: `Ready to deploy` / `Needs attention` / `Do not deploy`
