Review the provided code and report issues in these categories:

## Review Checklist

1. **Bugs**: Logic errors, off-by-one, null/undefined risks, race conditions
2. **Security**: Injection risks, missing input validation, exposed secrets, auth gaps
3. **Performance**: N+1 queries, unnecessary re-renders, missing indexes, unbounded operations
4. **Best Practices**: Naming, error handling, code duplication, separation of concerns

## Output Format

For each issue found:

```
### [Category] Brief title
**Severity**: Critical / Warning / Suggestion
**Location**: File and line reference
**Issue**: What's wrong
**Fix**: How to resolve it
```

If no issues are found in a category, skip it. End with a brief summary.

## Code to Review

$ARGUMENTS
