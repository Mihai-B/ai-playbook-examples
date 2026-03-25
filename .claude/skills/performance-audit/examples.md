# Performance Audit Examples

## Example: Dashboard service audit

**Input**: `Audit the performance of src/services/DashboardService.ts`

**Output**:

```
## Performance Audit: DashboardService

### HIGH: N+1 query in getDashboardStats
**Location**: src/services/DashboardService.ts:24-38
**Issue**: Fetches all projects, then loops to count tasks per project individually
**Impact**: 1 + N database queries where N = number of projects (50+ for large orgs)
**Fix**:
```

```ts
// Before: N+1
const projects = await prisma.project.findMany();
for (const p of projects) {
  p.taskCount = await prisma.task.count({ where: { projectId: p.id } });
}

// After: Single query with aggregation
const projects = await prisma.project.findMany({
  include: { _count: { select: { tasks: true } } },
});
```

```
### MEDIUM: Unbounded user list in getRecentActivity
**Location**: src/services/DashboardService.ts:52
**Issue**: `findMany()` with no `take` limit — fetches all activity records
**Impact**: Response time grows linearly with data; 10k+ records = multi-second response
**Fix**: Add `take: 50` and cursor-based pagination

### LOW: Redundant date formatting in loop
**Location**: src/services/DashboardService.ts:70-75
**Issue**: `new Intl.DateTimeFormat()` created inside a loop for each record
**Impact**: Minor — ~5ms overhead for 100 records
**Fix**: Create formatter once outside the loop

## Summary
| Issue | Impact | Effort |
|-------|--------|--------|
| N+1 query in getDashboardStats | HIGH | Low |
| Unbounded list in getRecentActivity | MEDIUM | Low |
| Redundant date formatting | LOW | Low |

**Estimated improvement**: 80%+ reduction in dashboard load time for large accounts
```
