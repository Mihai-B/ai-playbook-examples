---
name: generate-api-endpoint
description: Scaffold a complete CRUD API endpoint with Minimal API, DTOs, validation, service layer, and tests. Use when creating new REST API routes or resources in .NET.
disable-model-invocation: true
---

# Generate API Endpoint

Scaffold a complete CRUD API for a given resource using ASP.NET Core Minimal API.

## When to Use

- When adding a new REST resource to the API
- When scaffolding a vertical slice for a new entity

## Parameters

- **resource** (required) — name of the resource in PascalCase (e.g. `Products`, `Employees`)
- **properties** (optional) — comma-separated list of properties. If not provided, infer sensible defaults from the resource name.

## Context

This is a .NET 8+ project using ASP.NET Core Minimal API with Entity Framework Core. The codebase follows a clean architecture layout:

- `src/Api/Endpoints/` — endpoint group classes
- `src/Application/` — service interfaces, DTOs, validators
- `src/Domain/Entities/` — EF Core entity classes
- `src/Infrastructure/Repositories/` — repository implementations
- `src/Infrastructure/Services/` — service implementations
- `tests/UnitTests/` — xUnit test classes

## Task

Generate all files for a complete CRUD API:

1. **Entity** (`src/Domain/Entities/{Resource}.cs`) — EF Core entity with properties
2. **DTOs** (`src/Application/{Resource}/`) — `Create{Resource}Request`, `Update{Resource}Request`, `{Resource}Response` records
3. **Validator** (`src/Application/{Resource}/Create{Resource}Validator.cs`) — FluentValidation rules
4. **Service interface + implementation** (`src/Application/{Resource}/I{Resource}Service.cs`, `src/Infrastructure/Services/{Resource}Service.cs`)
5. **Endpoint group** (`src/Api/Endpoints/{Resource}Endpoints.cs`) — Minimal API MapGroup with full CRUD
6. **Unit tests** (`tests/UnitTests/{Resource}ServiceTests.cs`) — xUnit + NSubstitute tests for the service layer
7. **DI registration** — Lines to add to `Program.cs`

## Constraints

- Use `TypedResults` for all endpoint responses
- Accept `CancellationToken` in all async methods
- Use `Results<T1, T2>` union return types
- Follow PascalCase naming and file-scoped namespaces
- Add XML docs on all public members
- Validate input with FluentValidation in an endpoint filter
- Return ProblemDetails for errors

## Examples

Input: `/generate-api-endpoint resource=Books properties=Title,Author,ISBN,Year`

Output files:

```
src/Domain/Entities/Book.cs
src/Application/Books/BookResponse.cs
src/Application/Books/CreateBookRequest.cs
src/Application/Books/UpdateBookRequest.cs
src/Application/Books/CreateBookValidator.cs
src/Application/Books/IBookService.cs
src/Infrastructure/Services/BookService.cs
src/Api/Endpoints/BookEndpoints.cs
tests/UnitTests/BookServiceTests.cs
```

Registration in `Program.cs`:

```csharp
builder.Services.AddScoped<IBookService, BookService>();
app.MapBookEndpoints();
```

## Output

Generate all files listed above with complete, compilable code. Show `Program.cs` registration lines separately at the end.
