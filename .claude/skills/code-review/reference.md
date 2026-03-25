# Code Review Reference

## Review Checklist

### 1. Bugs
- Logic errors and off-by-one mistakes
- Null/undefined access without guards
- Race conditions in async code
- Incorrect error propagation
- Missing return statements

### 2. Security
- SQL/NoSQL injection via unsanitized input
- Missing Zod validation on request bodies
- Exposed secrets or API keys
- Authentication/authorization gaps
- XSS via unescaped user content

### 3. Performance
- N+1 database queries (loop with individual fetches)
- Missing database indexes on queried fields
- Unbounded `findMany()` without `take` limit
- Unnecessary React re-renders (inline objects/functions in JSX)
- Large payloads without pagination

### 4. Best Practices
- Ambiguous variable/function names
- Missing error handling on async operations
- Code duplication that should be extracted
- Business logic in route handlers (should be in service layer)
- Tight coupling between modules

## Output Format

For each issue found:

```
### [Category] Brief title
**Severity**: Critical / Warning / Suggestion
**Location**: File and line reference
**Issue**: What's wrong
**Fix**: How to resolve it
```

End with a brief summary of findings and overall recommendation.
