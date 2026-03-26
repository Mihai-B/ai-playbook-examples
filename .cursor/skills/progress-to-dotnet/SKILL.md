---
name: progress-to-dotnet
description: Migrate Progress OpenEdge ABL (4GL) code to .NET 8+ C#. Use when converting Progress procedures, classes, temp-tables, ProDataSets, or business logic to modern C# with ASP.NET Core, EF Core, and clean architecture patterns.
---

# Progress OpenEdge to .NET Migration

Migrate Progress OpenEdge ABL code to idiomatic .NET 8+ C# following clean architecture conventions.

## When to Use

- When converting Progress ABL procedures, classes, or functions to C#
- When replacing Progress AppServer services with ASP.NET Core API endpoints
- When migrating temp-tables and ProDataSets to EF Core entities and DTOs
- When modernizing a legacy OpenEdge application to .NET

## Parameters

- **source** (required) — Progress ABL source code (`.p`, `.cls`, `.w`, or `.i` file content) to migrate
- **target** (optional) — migration target: `api` (headless, default), `blazor` (with UI). Determines whether UI procedures become API endpoints or Blazor components.

## Context

This project uses .NET 8+ with ASP.NET Core Minimal API and Entity Framework Core. The target architecture follows clean architecture with `src/Domain/`, `src/Application/`, `src/Infrastructure/`, and `src/Api/`. Progress source code typically uses procedural ABL (`.p` files), may include OO ABL classes (`.cls`), relies on temp-tables and ProDataSets for data transfer, and uses `FIND`, `FOR EACH`, and `QUERY` statements for database access.

## Task

Follow this order for each Progress source file:

1. **Analyze** — Identify the source type (procedure, class, persistent procedure, window/UI, include file)
2. **Map types** — Convert Progress types to C# equivalents (see [references/type-mapping.md](references/type-mapping.md))
3. **Extract business logic** — Separate UI code (`ENABLE`, `DISPLAY`, `WAIT-FOR`) from domain logic
4. **Convert** — Translate to idiomatic C# with proper patterns:
   - Procedure (`.p`) → service class with methods
   - OO class (`.cls`) → C# class with dependency injection
   - Temp-table → DTO record or EF Core entity
   - ProDataSet → aggregate DTO or response object
   - `FOR EACH` / `FIND` → EF Core LINQ queries
   - `BUFFER` → entity variable or `DbSet<T>` query
   - `INPUT`/`OUTPUT`/`INPUT-OUTPUT` parameters → method parameters and return types
   - `RUN procedure` → service method call via DI
   - Persistent procedure → scoped DI service
   - `PUBLISH`/`SUBSCRIBE` events → MediatR notifications or .NET events
   - Include files (`.i`) → shared classes, constants, or extension methods
5. **Test** — Write xUnit tests for converted logic

## Constraints

- Always use dependency injection — never use static state like Progress shared variables or `NEW` without DI
- Replace `FIND ... NO-ERROR` + `AVAILABLE` checks with `FirstOrDefaultAsync` + null checks
- Convert `FOR EACH` loops with database access to EF Core LINQ `.Where().ToListAsync()`
- Replace `DEFINE TEMP-TABLE` with C# `record` DTOs or entity classes
- Replace `RUN ... ON SERVER` (AppServer calls) with HTTP client calls or service method invocations
- Use `async/await` for all I/O operations — Progress ABL is synchronous
- Replace `TRANSACTION` blocks with EF Core `SaveChangesAsync` or explicit `BeginTransactionAsync`
- Convert `ERROR-STATUS:ERROR` / `NO-ERROR` patterns to structured `try/catch` exception handling
- Replace `SHARED` variables with DI-injected services
- Map `LOGICAL` to `bool`, `INTEGER` to `int`, `DECIMAL` to `decimal`, `CHARACTER` to `string`
- Replace `MESSAGE ... VIEW-AS ALERT-BOX` with proper logging (`ILogger<T>`) or API error responses
- Convert `ASSIGN` statements to simple C# property assignments or object initializers

## Examples

**Progress procedure with database access and temp-table:**

```abl
DEFINE TEMP-TABLE ttCustomer NO-UNDO
    FIELD CustNum  AS INTEGER
    FIELD Name     AS CHARACTER
    FIELD Balance  AS DECIMAL.

DEFINE OUTPUT PARAMETER TABLE FOR ttCustomer.

FOR EACH Customer WHERE Customer.State = "CA" NO-LOCK:
    CREATE ttCustomer.
    ASSIGN
        ttCustomer.CustNum = Customer.CustNum
        ttCustomer.Name    = Customer.Name
        ttCustomer.Balance = Customer.Balance.
END.
```

**Becomes C# with EF Core + DTO:**

```csharp
public record CustomerDto(int CustNum, string Name, decimal Balance);

public async Task<IReadOnlyList<CustomerDto>> GetByStateAsync(
    string state, CancellationToken ct)
{
    return await _db.Customers
        .Where(c => c.State == state)
        .AsNoTracking()
        .Select(c => new CustomerDto(c.CustNum, c.Name, c.Balance))
        .ToListAsync(ct);
}
```

For full before/after migration examples (procedures, classes, temp-tables, AppServer calls), see [references/examples.md](references/examples.md).

## Output

For each Progress source file, produce:

1. One or more C# files following the project structure (`src/Domain/`, `src/Application/`, etc.)
2. A migration summary table showing what each Progress construct became in .NET
3. Any DI registration lines needed in `Program.cs`
4. xUnit tests for the converted business logic

## Additional Resources

- Type mapping reference: [references/type-mapping.md](references/type-mapping.md)
- Full migration examples: [references/examples.md](references/examples.md)
