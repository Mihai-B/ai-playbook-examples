# Implement CRUD for Entity

## Parameters

- **entity** (required) — singular name of the entity in PascalCase (e.g. `Product`, `Employee`)
- **properties** (optional) — comma-separated `Name:Type` pairs. If omitted, infer sensible properties from the entity name.

## Task

Generate the full vertical slice for the entity. Follow the patterns and conventions from the `crud-implementation` skill.

Produce these files, layer by layer:

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

## Example

Input: `Product Name:string, Price:decimal, SKU:string, StockQuantity:int`

## Output

Generate all files with complete, compilable C# code. Show `Program.cs` registration lines separately at the end.

## Entity to Implement

$ARGUMENTS
