# Reviewer Examples

## Example 1: Review with mixed severity findings

**Input**: `Review the changes in src/services/UserService.ts`

**Output**:

```
## src/services/UserService.ts

### CRITICAL: SQL injection via unsanitized input
**Location**: src/services/UserService.ts:42
**Issue**: User-supplied `searchTerm` is interpolated directly into a raw SQL query
**Fix**: Use Prisma's parameterized query or `prisma.$queryRaw` with tagged template

### WARNING: Missing error handling on external API call
**Location**: src/services/UserService.ts:78
**Issue**: The call to `emailService.send()` has no try/catch — a failure will crash the request
**Fix**: Wrap in try/catch and return a meaningful error response

### SUGGESTION: Ambiguous variable name
**Location**: src/services/UserService.ts:15
**Issue**: `data` doesn't convey what the variable holds
**Fix**: Rename to `userProfile` or `userData`

## Summary
**Verdict**: Request changes — 1 critical issue must be resolved before merge
```

## Example 2: Clean review with approval

**Input**: `Review src/hooks/useAuth.ts`

**Output**:

```
## src/hooks/useAuth.ts

No issues found. The hook follows established patterns:
- Proper cleanup in useEffect
- Error states handled correctly
- Token refresh logic matches the auth middleware expectations

## Summary
**Verdict**: Approve
```
