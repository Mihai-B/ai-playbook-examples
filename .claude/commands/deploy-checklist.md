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
8. **Review the checklist** — Go through the detailed checklist below

## Detailed Checklist

### Code Quality

- [ ] All feature branches are merged
- [ ] No TODO/FIXME comments related to this release
- [ ] Code review approved by at least one team member
- [ ] All TypeScript strict mode errors resolved

### Testing

- [ ] Unit tests pass (`npm run test`)
- [ ] E2E tests pass (`npm run test:e2e`)
- [ ] Manual smoke test of critical user flows
- [ ] Performance benchmarks within acceptable range

### Database

- [ ] All migrations applied to staging environment
- [ ] Rollback migration tested
- [ ] No destructive schema changes without a migration plan
- [ ] Seed data updated if needed

### Configuration

- [ ] Environment variables set for production
- [ ] Feature flags configured correctly
- [ ] Third-party API keys rotated if needed
- [ ] CORS settings reviewed

### Security

- [ ] `npm audit` shows no critical vulnerabilities
- [ ] No secrets committed to version control
- [ ] Authentication and authorization tested
- [ ] Rate limiting configured for public endpoints
- [ ] CSP headers set appropriately

### Infrastructure

- [ ] Health check endpoint responding
- [ ] Logging and monitoring configured
- [ ] Error tracking (Sentry/similar) connected
- [ ] Backup strategy confirmed
- [ ] Rollback plan documented

### Communication

- [ ] Release notes drafted
- [ ] Team notified of deployment window
- [ ] On-call engineer identified

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

$ARGUMENTS
