---
description: "Convert a Delphi VCL form to an ASP.NET Core API endpoint with DTOs, validation, and service layer"
agent: "agent"
tools: [read, search, editFiles, createFiles]
name: "Migrate Delphi Form to API"
argument-hint: "Provide the Delphi form source (.pas/.dfm) to convert to an API endpoint"
---
Convert a Delphi VCL form into an ASP.NET Core Minimal API endpoint with proper separation of concerns.

## Migration Mapping

| Delphi Form Element | .NET Equivalent |
|---------------------|-----------------|
| `TForm` | API endpoint group |
| `TEdit`, `TMemo` | Request DTO properties |
| `TComboBox`, `TCheckBox` | Request DTO properties (enum/bool) |
| `TDBGrid` / `TStringGrid` | List endpoint returning `IReadOnlyList<T>` |
| `TButton.OnClick` | POST/PUT/DELETE endpoint handler |
| `TLabel` (display) | Response DTO properties |
| Inline validation (`if Trim(edtName.Text) = ''`) | FluentValidation `AbstractValidator<T>` |
| `ShowMessage('Success')` | HTTP 200/201 response |
| `ShowMessage('Error: ...')` | ProblemDetails error response |

## Steps

1. **Extract business logic** — Separate UI event handlers from domain logic
2. **Create request/response DTOs** — Map form inputs to `record` request types, outputs to response types
3. **Create a validator** — Convert inline checks to `FluentValidation` rules
4. **Create a service** — Move business logic from button handlers into service methods
5. **Create the endpoint group** — Map HTTP verbs to operations:
   - Form load / grid display → `GET` endpoint
   - Save button → `POST` (create) or `PUT` (update) endpoint
   - Delete button → `DELETE` endpoint
   - Search/filter → `GET` with query parameters
6. **Write tests** — xUnit tests for the service layer

## Constraints

- Keep endpoints thin — all logic goes in the service layer
- Use `TypedResults` and `Results<T1, T2>` union types
- Accept `CancellationToken` in all async methods
- Never mix UI concerns with business logic in the service
- Replace `TTimer` operations with background services if needed
- Replace `Application.ProcessMessages` with proper async patterns
