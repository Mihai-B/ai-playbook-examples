# Code Review

Review the provided code and report issues in the following categories.

## Review Checklist

1. **Bugs** — Logic errors, null reference risks, race conditions, off-by-one errors
2. **Security** — SQL injection, missing input validation, exposed secrets, authorization gaps
3. **Performance** — N+1 queries, missing `AsNoTracking()`, unbounded result sets, unnecessary allocations
4. **Best Practices** — Naming conventions, missing `CancellationToken`, exception handling, SOLID violations, missing XML docs

## Constraints

- Evaluate against .NET 8+ / ASP.NET Core / EF Core conventions
- Reference specific line numbers when possible
- Suggest concrete fixes, not vague improvements

## Output Format

For each issue found:

```
### [Category] Brief title
**Severity**: Critical / Warning / Suggestion
**Location**: File and line reference
**Issue**: What's wrong
**Fix**: How to resolve it (with code snippet if applicable)
```

If no issues are found in a category, skip it. End with a brief summary and an overall assessment (Approve / Approve with suggestions / Request changes).

## Code to Review

$ARGUMENTS
