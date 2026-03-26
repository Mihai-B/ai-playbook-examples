---
name: crud-implementation
description: Patterns and conventions for implementing CRUD features in .NET Clean Architecture. Use when creating entities, repositories, services, endpoints, or DTOs, or when the user asks to add a new resource or feature that involves data persistence.
---

# CRUD Implementation Guide

Reference patterns and conventions for building CRUD features in .NET Clean Architecture.

## When to Use

- When creating a new entity, repository, service, or endpoint
- When the user asks to add a resource that involves data persistence
- When reviewing CRUD code for convention compliance
- As a reference for the `implement-crud` skill

## Parameters

None — this is a reference skill loaded automatically when CRUD work is detected.

## Context

Every CRUD feature follows a vertical slice through these layers:

```
Endpoint → Service → Repository → EF Core → Database
   ↕          ↕          ↕
  DTOs     Interfaces   Entity
```

Always implement bottom-up: Entity → EF Config → Repository → Service → DTOs → Endpoint → Tests.

| Layer | Location | Responsibility |
|-------|----------|----------------|
| Entity | `src/Domain/Entities/` | Domain model, no dependencies |
| EF Config | `src/Infrastructure/Data/Configurations/` | Table mapping, indexes, constraints |
| Repository | `src/Infrastructure/Repositories/` | Data access, queries, persistence |
| Service | `src/Infrastructure/Services/` | Orchestration, mapping, business rules |
| DTOs | `src/Application/{Feature}/` | Request/response contracts |
| Validator | `src/Application/{Feature}/` | Input validation with FluentValidation |
| Endpoint | `src/Api/Endpoints/` | HTTP routing, thin handlers |

## Task

When applying these patterns, follow these conventions per layer:

**Entity** — Include `CreatedAt` / `UpdatedAt` audit fields; use `required` modifier for non-nullable reference properties; keep entities free of framework dependencies.

**EF Configuration** — Always configure max lengths explicitly; add indexes on columns used in filters, sorting, or lookups; use `HasPrecision` for decimal columns.

**Repository** — Use `AsNoTracking()` for all read-only queries; always paginate list queries; accept `CancellationToken` in every async method.

**DTOs** — Use `record` types for immutability; never expose audit fields in request DTOs; keep response DTOs flat.

**Endpoints** — Use `TypedResults` for OpenAPI schema generation; `Results<T1, T2>` union types for multiple status codes; route constraints like `{id:int}` for type safety.

## Constraints

- Return `IQueryable` from repositories — always materialize in the repository
- Always use `AsNoTracking()` on read paths
- Map entities to DTOs in the service layer, not the endpoint
- Propagate `CancellationToken` through every async call
- Create separate validators for Create and Update requests
- Use `Created()` with location header for POST responses

| Question | Default | Alternative |
|----------|---------|-------------|
| Soft delete or hard delete? | Hard delete | Soft delete (`IsDeleted` flag) for audit-sensitive data |
| Sync or async validation? | Async (FluentValidation) | Sync only if no DB lookups needed in validator |
| AutoMapper or manual mapping? | Manual mapping | AutoMapper only if >10 DTOs to maintain |
| Repository per entity? | Yes | Shared generic repository only if entities are trivially similar |
| Pagination style? | Offset-based (`page`/`pageSize`) | Cursor-based for large datasets with real-time inserts |

## Examples

See [templates.md](templates.md) for complete code templates for each layer, including:
- Entity with audit fields
- `IEntityTypeConfiguration<T>` with max lengths, precision, and indexes
- Repository with paged queries and `AsNoTracking()`
- Record DTOs for request/response
- Minimal API endpoint group with `TypedResults`

## Output

This skill does not produce output directly — it provides patterns for other skills (`implement-crud`, `generate-api-endpoint`) and for manual CRUD implementation.
