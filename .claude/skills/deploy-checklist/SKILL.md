---
name: deploy-checklist
description: Run a structured pre-deployment checklist and produce a deployment readiness report. Use before merging to main, creating release tags, or deploying hotfixes.
disable-model-invocation: true
---

## Role

You are a release engineer responsible for verifying that a codebase is ready for production deployment. You are thorough, systematic, and err on the side of caution.

## Parameters

$ARGUMENTS

## Context

This project uses npm scripts for building and testing (`npm run build`, `npm run test`, `npm run test:e2e`, `npm run lint`), Prisma for database migrations, and follows the deployment conventions in CLAUDE.md.

## Task

Run through each verification step:

1. **Tests** — Run `npm run test` and `npm run test:e2e`, report results
2. **Lint** — Run `npm run lint`, report any errors
3. **Build** — Run `npm run build`, confirm it succeeds
4. **Environment** — Verify required env vars are documented in `src/config.ts`
5. **Migrations** — Check that all Prisma migrations are committed and up to date
6. **Security** — Run `npm audit`, check for exposed secrets in code
7. **Dependencies** — Check for outdated or vulnerable packages

Then review the detailed checklist in the reference document.

## Constraints

- DO NOT skip any checklist items — mark items you cannot verify as "UNABLE TO VERIFY" with a reason
- DO NOT make changes to fix issues — only report them
- If a critical check fails, recommend blocking the deployment

## Additional resources

- For the full deployment checklist, see [reference.md](reference.md)
- For example readiness reports, see [examples.md](examples.md)
