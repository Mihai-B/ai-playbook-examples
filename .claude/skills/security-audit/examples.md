# Security Audit Examples

## Example: Authentication flow audit

**Input**: `Audit the authentication flow in src/api/routes/auth.ts`

**Output**:

```
## Security Audit: Authentication Flow

### CRITICAL: Password hash returned in login response
**Location**: src/api/routes/auth.ts:45
**Vulnerability**: The login endpoint returns the full user object including `passwordHash`
**Attack vector**: Any client can read the password hash from the API response
**Remediation**:

```ts
// Before
return res.json({ data: user });

// After — explicitly select safe fields
const { passwordHash, ...safeUser } = user;
return res.json({ data: safeUser });
```

### HIGH: No rate limiting on login endpoint
**Location**: src/api/routes/auth.ts:38
**Vulnerability**: Login endpoint has no rate limiting, enabling brute-force attacks
**Attack vector**: Attacker can attempt unlimited password guesses
**Remediation**:

```ts
import rateLimit from "express-rate-limit";

const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 5, // 5 attempts per window
  message: {
    error: { code: "RATE_LIMITED", message: "Too many login attempts" },
  },
});

router.post("/auth/login", loginLimiter, loginHandler);
```

### MEDIUM: JWT secret loaded from string literal fallback
**Location**: src/api/routes/auth.ts:8
**Vulnerability**: `const secret = process.env.JWT_SECRET || "default-secret"` falls back to a guessable value
**Attack vector**: If env var is missing, attacker can forge valid tokens
**Remediation**: Remove the fallback — fail at startup if `JWT_SECRET` is not set (handled by `src/config.ts`)

## Summary
| Finding | Severity | Exploitability |
|---------|----------|----------------|
| Password hash in response | CRITICAL | Easy |
| No rate limiting on login | HIGH | Easy |
| JWT secret fallback | MEDIUM | Medium |

**Risk level**: HIGH — critical issue requires immediate fix before deployment
```
