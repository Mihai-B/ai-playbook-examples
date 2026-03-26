---
name: implement-crud
description: Generate the full vertical slice for a CRUD entity including entity, EF config, repository, service, DTOs, validators, endpoints, and tests. Use when implementing a complete CRUD feature for a new entity.
disable-model-invocation: true
---

# Implement CRUD for Entity

Generate the full vertical slice for an entity following Clean Architecture patterns.

## When to Use

- When adding a new entity that needs full CRUD operations
- When scaffolding a complete feature slice from database to API

## Parameters

- **entity** (required) — singular name of the entity in PascalCase (e.g. `Product`, `Employee`)
- **properties** (optional) — comma-separated `Name:Type` pairs (e.g. `Name:string, Price:decimal`). If omitted, infer sensible properties from the entity name.

## Context

This project uses .NET 8+ with ASP.NET Core Minimal API and Entity Framework Core. Follow the patterns and conventions from the `crud-implementation` skill. The architecture is:

- `src/Domain/Entities/` — entity classes
- `src/Infrastructure/Data/Configurations/` — EF fluent configurations
- `src/Infrastructure/Data/AppDbContext.cs` — DbContext with DbSets
- `src/Application/Interfaces/` — repository interfaces
- `src/Infrastructure/Repositories/` — repository implementations
- `src/Application/{Feature}/` — DTOs and validators
- `src/Infrastructure/Services/` — service implementations
- `src/Api/Endpoints/` — Minimal API endpoint groups
- `tests/UnitTests/` — xUnit tests

## Task

Produce these files, layer by layer (bottom-up):

1. Entity — `src/Domain/Entities/{Entity}.cs`
2. EF Configuration — `src/Infrastructure/Data/Configurations/{Entity}Configuration.cs`
3. DbContext update — add `DbSet` to `src/Infrastructure/Data/AppDbContext.cs`
4. Repository interface — `src/Application/Interfaces/I{Entity}Repository.cs`
5. Repository implementation — `src/Infrastructure/Repositories/{Entity}Repository.cs`
6. DTOs — `src/Application/{Entities}/{Entity}Response.cs`, `Create{Entity}Request.cs`, `Update{Entity}Request.cs`
7. Validators — `src/Application/{Entities}/Create{Entity}Validator.cs`, `Update{Entity}Validator.cs`
8. Service interface + implementation — `src/Application/{Entities}/I{Entity}Service.cs`, `src/Infrastructure/Services/{Entity}Service.cs`
9. Endpoints — `src/Api/Endpoints/{Entity}Endpoints.cs` (GET list, GET by id, POST, PUT, DELETE)
10. Tests — `tests/UnitTests/{Entity}ServiceTests.cs`
11. DI registration lines for `Program.cs`

## Constraints

- Include `CreatedAt` / `UpdatedAt` audit fields on the entity
- Use `AsNoTracking()` for all read queries
- Accept `CancellationToken` in every async method
- Use `TypedResults` and `Results<T1, T2>` in endpoints
- Paginate list queries — never return unbounded results
- Use `record` types for DTOs
- Create separate validators for Create and Update
- Add XML docs on all public members

## Examples

Input: `/implement-crud Product Name:string, Price:decimal, SKU:string, StockQuantity:int`

Output files:

```
src/Domain/Entities/Product.cs
src/Infrastructure/Data/Configurations/ProductConfiguration.cs
src/Application/Interfaces/IProductRepository.cs
src/Infrastructure/Repositories/ProductRepository.cs
src/Application/Products/ProductResponse.cs
src/Application/Products/CreateProductRequest.cs
src/Application/Products/UpdateProductRequest.cs
src/Application/Products/CreateProductValidator.cs
src/Application/Products/UpdateProductValidator.cs
src/Application/Products/IProductService.cs
src/Infrastructure/Services/ProductService.cs
src/Api/Endpoints/ProductEndpoints.cs
tests/UnitTests/ProductServiceTests.cs
```

Registration:

```csharp
builder.Services.AddScoped<IProductRepository, ProductRepository>();
builder.Services.AddScoped<IProductService, ProductService>();
app.MapProductEndpoints();
```

## Output

Generate all files with complete, compilable C# code. Show `Program.cs` registration lines separately at the end.
