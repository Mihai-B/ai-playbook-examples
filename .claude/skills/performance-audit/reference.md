# Performance Audit Reference

## Common Performance Anti-patterns

### Database
| Anti-pattern | Fix |
|-------------|-----|
| N+1 queries (loop with individual fetches) | Use `include` or `_count` in Prisma |
| `findMany()` without `take` limit | Add pagination with `take` and `skip` |
| Missing index on frequently queried field | Add `@@index` in Prisma schema |
| `select: *` when only a few fields needed | Use `select` to fetch only required fields |
| Multiple sequential queries | Use `prisma.$transaction` for batching |

### API
| Anti-pattern | Fix |
|-------------|-----|
| Returning full records when client needs subset | Use `select` to limit response fields |
| No pagination on list endpoints | Add `page` and `limit` query params |
| Synchronous heavy computation in request handler | Move to background job or worker |

### Frontend (React)
| Anti-pattern | Fix |
|-------------|-----|
| Inline objects/functions in JSX props | Extract to variables or `useCallback` |
| Fetching data in every render cycle | Move to `useEffect` with proper deps |
| Large list without virtualization | Use `react-window` or `react-virtualized` |
| Uncontrolled bundle size | Lazy-load routes with `React.lazy` |

## Report Format

For each issue:

```
### [SEVERITY]: Brief title
**Location**: file:line
**Issue**: What's happening and why it's slow
**Impact**: Estimated performance cost
**Fix**: Code example showing the solution
```

End with a summary table:

| Issue | Impact | Effort |
|-------|--------|--------|
| ... | HIGH/MEDIUM/LOW | LOW/MEDIUM/HIGH |
