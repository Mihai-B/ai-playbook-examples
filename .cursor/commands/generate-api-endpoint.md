# Generate API Endpoint

## Parameters

- **resource** (required) — name of the resource (e.g. `Products`, `Employees`)
- **properties** (optional) — comma-separated list of properties. If not provided, infer sensible defaults from the resource name.

## Context

This is a .NET 8+ project using ASP.NET Core Minimal API with Entity Framework Core. The codebase follows a clean architecture layout:

- `src/Api/Endpoints/` — endpoint group classes
- `src/Application/` — service interfaces, DTOs, validators
- `src/Domain/Entities/` — EF Core entity classes
- `src/Infrastructure/Repositories/` — repository implementations
- `tests/UnitTests/` — xUnit test classes

## Task

Generate a complete CRUD API for the given resource, producing these files:

1. **Entity** (`src/Domain/Entities/{Resource}.cs`) — EF Core entity with properties
2. **DTOs** (`src/Application/{Resource}/` ) — `Create{Resource}Request`, `Update{Resource}Request`, `{Resource}Response` records
3. **Validator** (`src/Application/{Resource}/Create{Resource}Validator.cs`) — FluentValidation rules
4. **Service interface + implementation** (`src/Application/{Resource}/I{Resource}Service.cs`, `src/Infrastructure/Services/{Resource}Service.cs`)
5. **Endpoint group** (`src/Api/Endpoints/{Resource}Endpoints.cs`) — Minimal API MapGroup with full CRUD
6. **Unit tests** (`tests/UnitTests/{Resource}ServiceTests.cs`) — xUnit + NSubstitute tests for the service layer

## Constraints

- Use `TypedResults` for all endpoint responses
- Accept `CancellationToken` in all async methods
- Use `Results<T1, T2>` union return types
- Follow PascalCase naming and file-scoped namespaces
- Add XML docs on all public members
- Validate input with FluentValidation in an endpoint filter
- Return ProblemDetails for errors

## Example

Input: `resource=Books properties=Title,Author,ISBN,Year`

Output structure:
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

## Output

Generate all files listed above with complete, compilable code. Register the endpoint group in `Program.cs` with `app.MapBookEndpoints()`.

## Resource to Generate

$ARGUMENTS
