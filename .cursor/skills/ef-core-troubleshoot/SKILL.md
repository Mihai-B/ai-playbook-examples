---
name: ef-core-troubleshoot
description: Diagnose and fix common Entity Framework Core issues including migration conflicts, query performance problems, and relationship configuration errors. Use when EF Core throws exceptions, migrations fail, or queries are slow.
---

# EF Core Troubleshooting

Diagnose and resolve common Entity Framework Core issues systematically.

## When to Use

- When EF Core throws exceptions (migration errors, relationship errors, concurrency conflicts)
- When migrations fail to apply or have snapshot mismatches
- When queries are slow or generating excessive SQL
- When encountering unexpected cascade deletes or tracking behavior

## Parameters

- **symptom** (required) — the error message, exception type, or observed behavior (e.g. "migration already applied", "N+1 queries in logs", "DbUpdateConcurrencyException")

## Context

This project uses EF Core with code-first migrations. The `DbContext` (`AppDbContext`) is in `src/Infrastructure/Data/`, entity configurations in `src/Infrastructure/Data/Configurations/`, and the startup project is `src/Api/`.

## Task

1. **Identify the issue category** — Match the symptom to one of: migration conflict, N+1 query, relationship configuration error, concurrency conflict, or performance problem
2. **Run diagnostics** — Execute relevant commands:
   ```bash
   dotnet ef migrations list --project src/Infrastructure
   dotnet ef migrations script --idempotent --project src/Infrastructure
   dotnet ef database update --project src/Infrastructure -- --dry-run
   ```
3. **Apply the fix** — Follow the resolution steps for the identified category:

   **Migration Conflicts** (`The migration '...' has already been applied` or snapshot mismatch):
   - Remove the last migration and regenerate
   - Never manually edit `ModelSnapshot.cs`

   **N+1 Query Problems** (excessive SQL queries for related data):
   - Add `.Include()` for eager loading
   - Use `.AsSplitQuery()` for multiple collection includes
   - Use `.Select()` projection for needed fields only

   **Relationship Errors** (`Unable to determine the relationship` or unexpected cascades):
   - Configure relationships explicitly with fluent API
   - Set `.OnDelete(DeleteBehavior.Restrict)` for most cases

   **Concurrency Conflicts** (`DbUpdateConcurrencyException`):
   - Add `[Timestamp]` or `RowVersion` property
   - Implement retry or conflict resolution with `entry.GetDatabaseValues()`

4. **Verify the fix** — Confirm the issue is resolved

## Constraints

- Never manually edit `ModelSnapshot.cs` — always regenerate
- Never manually edit generated migration files
- Enable sensitive data logging only in development: `optionsBuilder.EnableSensitiveDataLogging()`
- Always review generated SQL before applying to production
- Use `AsNoTracking()` for read-only queries
- Use `AsSplitQuery()` when including multiple collections

## Examples

Input: "Getting `The migration 'AddUserEmail' has already been applied` when running `dotnet ef database update`"

Resolution:
1. Run `dotnet ef migrations list --project src/Infrastructure` to check status
2. If snapshot is out of sync:
   ```bash
   dotnet ef migrations remove --project src/Infrastructure
   dotnet ef migrations add AddUserEmail --project src/Infrastructure
   ```

Input: "Seeing 50+ SQL queries when loading a page that shows users with their posts"

Resolution:
```csharp
// Before — triggers N+1
var users = await context.Users.ToListAsync(ct);
foreach (var user in users)
    Console.WriteLine(user.Posts.Count);

// After — single query with eager loading
var users = await context.Users
    .Include(u => u.Posts)
    .AsNoTracking()
    .ToListAsync(ct);
```

## Output

1. Identified root cause
2. Diagnostic command results
3. Applied fix with code changes
4. Verification that the issue is resolved
5. Performance checklist review:
   - [ ] `AsNoTracking()` on read-only queries
   - [ ] `Select()` projections instead of full entities
   - [ ] Indexes on columns in WHERE/JOIN/ORDER BY
   - [ ] `AsSplitQuery()` for multi-collection includes
   - [ ] Batch operations with `ExecuteUpdateAsync()` / `ExecuteDeleteAsync()` (EF Core 7+)
   - [ ] Generated SQL reviewed via logging
