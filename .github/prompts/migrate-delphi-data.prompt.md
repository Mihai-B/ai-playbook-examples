---
description: "Convert a Delphi data module with ADO datasets to EF Core repositories and services"
agent: "agent"
tools: [read, search, editFiles, createFiles]
name: "Migrate Delphi Data Module to EF Core"
argument-hint: "Provide the Delphi data module source (.pas/.dfm) to convert to EF Core"
---
Convert a Delphi data module (`TDataModule`) with ADO components to Entity Framework Core repositories and services.

## Migration Mapping

| Delphi Data Module | .NET Equivalent |
|--------------------|-----------------|
| `TDataModule` | Repository + Service classes |
| `TADOConnection` | `DbContext` (registered as scoped DI service) |
| `TADOQuery` | EF Core LINQ query or `FromSqlRaw` |
| `TADOTable` | `DbSet<T>` |
| `TADOStoredProc` | `FromSqlRaw` / `ExecuteSqlRawAsync` |
| `TDataSource` | — (not needed; binding handled differently) |
| `TField` mappings | Entity properties with EF configuration |
| `Query.SQL.Text := '...'` | LINQ `.Where()`, `.Select()`, `.OrderBy()` |
| `Query.Parameters.ParamByName(...)` | LINQ expressions or `SqlParameter` |
| `Query.Open` / `Query.ExecSQL` | `ToListAsync()` / `ExecuteSqlRawAsync()` |
| `while not Query.Eof` iteration | LINQ `.Select()` projection |
| `Query.FieldByName('X').AsString` | Entity property access `entity.X` |
| `Connection.BeginTrans` / `CommitTrans` | `SaveChangesAsync()` or `BeginTransactionAsync()` |

## Steps

1. **Identify entities** — Each table accessed by the data module becomes an entity in `src/Domain/Entities/`
2. **Create EF configurations** — Map field types, max lengths, indexes, and relationships in `IEntityTypeConfiguration<T>`
3. **Create repository** — Convert each `TADOQuery` to a repository method:
   - `SELECT` queries → `async Task<T?>` or `async Task<IReadOnlyList<T>>` with LINQ
   - `INSERT` → `AddAsync` + `SaveChangesAsync`
   - `UPDATE` → modify tracked entity + `SaveChangesAsync`
   - `DELETE` → `Remove` + `SaveChangesAsync` or `ExecuteDeleteAsync`
4. **Create service** — Orchestrate repository calls, handle mapping to DTOs
5. **Convert SQL to LINQ** where possible:
   ```
   SELECT * FROM Customers WHERE State = :State ORDER BY Name
   →
   _db.Customers.Where(c => c.State == state).OrderBy(c => c.Name).AsNoTracking().ToListAsync(ct)
   ```
6. **Write tests** — Mock repositories with NSubstitute, test service logic

## Constraints

- Prefer LINQ over raw SQL — only use `FromSqlRaw` for complex queries that can't be expressed in LINQ
- Always use `AsNoTracking()` for read-only queries
- Always paginate list queries — never return unbounded results
- Parameterize all queries — never concatenate user input into SQL
- Replace `try/except` with structured `try/catch` using specific exception types
- Convert `Currency` fields to `decimal` with `HasPrecision(18, 2)` in EF config
- Replace `Variant` field access with strongly-typed entity properties
