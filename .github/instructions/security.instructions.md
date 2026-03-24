---
description: "Use when handling authentication, authorization, input validation, secrets, or security-sensitive code"
applyTo: "src/middleware/auth**,src/api/**/*.ts"
---
# Security Guidelines

## Input Validation

- Validate ALL user input at the API boundary using Zod schemas
- Never trust client-side validation alone
- Sanitize strings that will be rendered as HTML to prevent XSS

```ts
const CreateUserSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100),
  role: z.enum(["user", "admin"]),
});
```

## Authentication

- Use JWT tokens with short expiry (15 min access, 7 day refresh)
- Store refresh tokens server-side, not in localStorage
- Hash passwords with bcrypt (cost factor ≥ 12)
- Rate-limit login endpoints

## Authorization

- Check permissions in middleware, not in route handlers
- Use role-based access control (RBAC) with the `authorize()` middleware
- Never expose internal IDs in error messages

## Secrets

- Never hardcode secrets — use environment variables
- Never log sensitive data (tokens, passwords, PII)
- Use `src/config.ts` for validated environment variable access
