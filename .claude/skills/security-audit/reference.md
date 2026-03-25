# Security Audit Reference

## OWASP Top 10 Checklist (Web Application Focus)

### 1. Injection
- SQL/NoSQL injection via raw queries
- Command injection via `exec()` or `spawn()`
- XSS via unescaped user content in HTML

### 2. Broken Authentication
- Passwords stored in plaintext or weak hashing
- Missing rate limiting on login/registration
- JWT tokens with no expiry or overly long expiry
- Refresh tokens stored in localStorage

### 3. Sensitive Data Exposure
- Password hashes returned in API responses
- PII logged to console or log files
- Secrets hardcoded in source code
- Missing HTTPS enforcement

### 4. Broken Access Control
- Missing `authorize()` middleware on protected routes
- IDOR: users can access other users' data by changing IDs
- Privilege escalation: non-admin can access admin endpoints

### 5. Security Misconfiguration
- Permissive CORS (`Access-Control-Allow-Origin: *`)
- Missing security headers (CSP, X-Frame-Options, etc.)
- Debug mode enabled in production
- Default credentials

## Severity Definitions

| Severity | Criteria |
|----------|----------|
| CRITICAL | Exploitable with direct data loss, full account takeover, or RCE |
| HIGH | Exploitable with significant impact (data leak, auth bypass) |
| MEDIUM | Exploitable with limited impact or requires specific conditions |
| LOW | Minor issue, defense-in-depth concern |

## Report Format

For each finding:

```
### [SEVERITY]: Brief title
**Location**: file:line
**Vulnerability**: What's wrong
**Attack vector**: How this could be exploited
**Remediation**: Code example showing the fix
```

End with a summary table and overall risk assessment.
