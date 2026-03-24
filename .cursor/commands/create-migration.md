# Create EF Core Migration

Generate an Entity Framework Core migration based on the described schema change.

## Parameters

- **change** (required) — description of the schema change (e.g. "Add Email column to Users table")

## Context

This project uses EF Core with code-first migrations. The infrastructure project is at `src/Infrastructure/` and the `DbContext` is `AppDbContext`.

## Task

1. **Modify the entity** — Update the relevant entity class in `src/Domain/Entities/`
2. **Update the DbContext** if needed — Add or modify `DbSet<T>` or `OnModelCreating` configuration in `src/Infrastructure/Data/AppDbContext.cs`
3. **Suggest the migration command** — Provide the `dotnet ef` command to generate the migration
4. **Provide rollback guidance** — Describe how to revert if needed

## Constraints

- Name the migration descriptively: `Add{What}{Where}` (e.g. `AddEmailIndexToUsers`)
- Never drop columns in the same release as code removal — use two-phase approach
- Add indexes for columns used in WHERE clauses or JOINs
- Use fluent API configuration over data annotations for complex mappings
- Include a comment in the entity explaining any non-obvious constraints
- Always suggest generating an idempotent SQL script for review

## Output

1. Modified entity class (full file)
2. Any DbContext changes
3. The `dotnet ef migrations add` command to run
4. The `dotnet ef migrations script --idempotent` command for SQL review
5. Rollback command

## Schema Change

$ARGUMENTS
