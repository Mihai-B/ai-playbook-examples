---
name: create-migration
description: Generate an Entity Framework Core migration based on a described schema change. Use when adding columns, tables, indexes, or modifying the database schema.
disable-model-invocation: true
---

# Create EF Core Migration

Generate an Entity Framework Core code-first migration from a described schema change.

## When to Use

- When adding a new column, table, or index
- When modifying existing schema (rename, type change, constraint)
- When setting up a new entity with EF configuration

## Parameters

- **change** (required) — description of the schema change (e.g. "Add Email column to Users table", "Create OrderItems table with FK to Orders")

## Context

This project uses EF Core with code-first migrations. The infrastructure project is at `src/Infrastructure/` and the `DbContext` is `AppDbContext`. Entity classes live in `src/Domain/Entities/` and fluent configurations in `src/Infrastructure/Data/Configurations/`.

## Task

1. **Modify the entity** — Update or create the entity class in `src/Domain/Entities/`
2. **Add EF configuration** — Create or update the `IEntityTypeConfiguration<T>` in `src/Infrastructure/Data/Configurations/`
3. **Update the DbContext** if needed — Add or modify `DbSet<T>` in `src/Infrastructure/Data/AppDbContext.cs`
4. **Provide the migration command** — The `dotnet ef` command to generate the migration
5. **Provide rollback guidance** — How to revert if needed

## Constraints

- Name migrations descriptively: `Add{What}{Where}` (e.g. `AddEmailIndexToUsers`)
- Never drop columns in the same release as code removal — use two-phase approach
- Add indexes for columns used in WHERE clauses or JOINs
- Use fluent API configuration over data annotations for complex mappings
- Include a comment in the entity explaining any non-obvious constraints
- Always suggest generating an idempotent SQL script for review

## Examples

Input: `/create-migration Add a non-nullable Email column with unique index to the Users table`

Output:
1. Updated `User.cs` entity with `public required string Email { get; set; }`
2. Updated `UserConfiguration.cs` with `.HasMaxLength(255).IsRequired()` and `.HasIndex(x => x.Email).IsUnique()`
3. Commands:
   - `dotnet ef migrations add AddEmailToUsers --project src/Infrastructure`
   - `dotnet ef migrations script --idempotent --project src/Infrastructure`
   - Rollback: `dotnet ef migrations remove --project src/Infrastructure`

## Output

1. Modified entity class (full file)
2. EF configuration changes (full file)
3. Any DbContext changes
4. The `dotnet ef migrations add` command to run
5. The `dotnet ef migrations script --idempotent` command for SQL review
6. Rollback command
