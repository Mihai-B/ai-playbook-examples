# Deploy Checklist Reference

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

## Report Format

```
## Deployment Readiness Report

| Check          | Status | Notes          |
|----------------|--------|----------------|
| Tests          | PASS   |                |
| Lint           | PASS   |                |
| Build          | PASS   |                |
| Env Config     | PASS   |                |
| Migrations     | PASS   |                |
| Security       | WARN   | 1 advisory     |
| Dependencies   | PASS   |                |

**Blockers**: [Critical issues]
**Warnings**: [Non-critical items]
**Recommendation**: Ready to deploy / Needs attention / Blocked
```
