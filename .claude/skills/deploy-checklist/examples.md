# Deploy Checklist Examples

## Example 1: Ready with warnings

**Input**: `Check if we're ready to deploy the user-auth feature branch`

**Output**:

```
## Deployment Readiness Report

| Check          | Status | Notes                              |
|----------------|--------|------------------------------------|
| Tests          | PASS   | 142 unit, 18 e2e — all green       |
| Lint           | PASS   | No errors                          |
| Build          | PASS   | Built in 12s                       |
| Env Config     | WARN   | New `JWT_SECRET` var not in .env.example |
| Migrations     | PASS   | 1 new migration committed          |
| Security       | WARN   | 2 moderate npm advisories          |
| Dependencies   | PASS   | All up to date                     |

**Blockers**: None
**Warnings**: Update .env.example with JWT_SECRET, review npm advisories
**Recommendation**: Ready to deploy with warnings addressed
```

## Example 2: Blocked deployment

**Input**: `Pre-deploy check for the payments refactor`

**Output**:

```
## Deployment Readiness Report

| Check          | Status | Notes                              |
|----------------|--------|------------------------------------|
| Tests          | FAIL   | 3 unit tests failing in PaymentService |
| Lint           | PASS   | No errors                          |
| Build          | PASS   | Built in 15s                       |
| Env Config     | WARN   | STRIPE_WEBHOOK_SECRET not documented |
| Migrations     | FAIL   | Migration `add-payment-status` not committed |
| Security       | PASS   | No advisories                      |
| Dependencies   | PASS   | All up to date                     |

**Blockers**:
1. 3 failing unit tests in PaymentService — must fix before merge
2. Uncommitted migration `add-payment-status` — run `npx prisma migrate dev`

**Warnings**: Document STRIPE_WEBHOOK_SECRET in .env.example
**Recommendation**: Blocked — resolve 2 blockers before deploying
```
