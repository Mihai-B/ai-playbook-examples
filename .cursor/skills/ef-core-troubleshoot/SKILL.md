---
name: ef-core-troubleshoot
description: Diagnose and fix common Entity Framework Core issues including migration conflicts, query performance problems, and relationship configuration errors. Use when EF Core throws exceptions, migrations fail, or queries are slow.
---

# EF Core Troubleshooting

## Common Issues

### Migration Conflicts

**Symptom**: `The migration '...' has already been applied` or snapshot mismatch errors.

**Resolution**:
1. Check pending migrations: `dotnet ef migrations list --project src/Infrastructure`
2. If snapshot is out of sync, remove the last migration and regenerate:
   ```bash
   dotnet ef migrations remove --project src/Infrastructure
   dotnet ef migrations add CorrectMigrationName --project src/Infrastructure
   ```
3. Never manually edit the `ModelSnapshot.cs` — always regenerate

### N+1 Query Problems

**Symptom**: Excessive SQL queries in logs for related data.

**Resolution**:
- Use `.Include()` for eager loading known navigations
- Use `.AsSplitQuery()` for queries with multiple collection includes
- Use projection with `.Select()` to fetch only needed fields
- Enable query logging to identify: `optionsBuilder.LogTo(Console.WriteLine)`

```csharp
// Bad — triggers N+1
var users = await context.Users.ToListAsync(ct);
foreach (var user in users)
    Console.WriteLine(user.Posts.Count); // lazy load per user

// Good — single query
var users = await context.Users
    .Include(u => u.Posts)
    .AsNoTracking()
    .ToListAsync(ct);
```

### Relationship Configuration Errors

**Symptom**: `Unable to determine the relationship` or unexpected cascade deletes.

**Resolution**:
- Always configure relationships explicitly in `OnModelCreating`
- Specify delete behavior: `.OnDelete(DeleteBehavior.Restrict)` for most cases
- Use `HasOne/HasMany/WithOne/WithMany` fluent API over conventions for complex models

### Concurrency Conflicts

**Symptom**: `DbUpdateConcurrencyException` during save.

**Resolution**:
- Add a `[Timestamp]` or `RowVersion` property to the entity
- Catch `DbUpdateConcurrencyException` and implement retry or conflict resolution
- Use `entry.GetDatabaseValues()` to compare current vs stored values

## Diagnostic Commands

```bash
# List all migrations and their status
dotnet ef migrations list --project src/Infrastructure

# Generate SQL script for review
dotnet ef migrations script --idempotent --project src/Infrastructure

# Check model snapshot vs database
dotnet ef database update --project src/Infrastructure -- --dry-run

# Enable detailed EF logging in DbContext
# Add to OnConfiguring: optionsBuilder.EnableSensitiveDataLogging().LogTo(Console.WriteLine)
```

## Performance Checklist

- [ ] Use `AsNoTracking()` for read-only queries
- [ ] Use `Select()` projections instead of loading full entities
- [ ] Add indexes for columns in WHERE/JOIN/ORDER BY
- [ ] Use `AsSplitQuery()` for multi-collection includes
- [ ] Batch updates with `ExecuteUpdateAsync()` / `ExecuteDeleteAsync()` (EF Core 7+)
- [ ] Review generated SQL via logging before production
