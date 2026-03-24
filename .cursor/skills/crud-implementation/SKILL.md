---
name: crud-implementation
description: Patterns and conventions for implementing CRUD features in .NET Clean Architecture. Use when creating entities, repositories, services, endpoints, or DTOs, or when the user asks to add a new resource or feature that involves data persistence.
---

# CRUD Implementation Guide

## Architecture Overview

Every CRUD feature follows a vertical slice through these layers:

```
Endpoint → Service → Repository → EF Core → Database
   ↕          ↕          ↕
  DTOs     Interfaces   Entity
```

Always implement bottom-up: Entity → EF Config → Repository → Service → DTOs → Endpoint → Tests.

## Layer Responsibilities

| Layer | Location | Responsibility |
|-------|----------|----------------|
| Entity | `src/Domain/Entities/` | Domain model, no dependencies |
| EF Config | `src/Infrastructure/Data/Configurations/` | Table mapping, indexes, constraints |
| Repository | `src/Infrastructure/Repositories/` | Data access, queries, persistence |
| Service | `src/Infrastructure/Services/` | Orchestration, mapping, business rules |
| DTOs | `src/Application/{Feature}/` | Request/response contracts |
| Validator | `src/Application/{Feature}/` | Input validation with FluentValidation |
| Endpoint | `src/Api/Endpoints/` | HTTP routing, thin handlers |

## Key Patterns

### Entity

```csharp
namespace Domain.Entities;

public class Product
{
    public int Id { get; set; }
    public required string Name { get; set; }
    public decimal Price { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }
}
```

- Always include `CreatedAt` / `UpdatedAt` audit fields
- Use `required` modifier for non-nullable reference properties
- Keep entities free of framework dependencies

### EF Configuration

```csharp
namespace Infrastructure.Data.Configurations;

public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.HasKey(x => x.Id);
        builder.Property(x => x.Name).HasMaxLength(200).IsRequired();
        builder.Property(x => x.Price).HasPrecision(18, 2);
        builder.HasIndex(x => x.Name);
    }
}
```

- Always configure max lengths explicitly — never rely on defaults
- Add indexes on columns used in filters, sorting, or lookups
- Use `HasPrecision` for decimal columns

### Repository

```csharp
public class ProductRepository : IProductRepository
{
    private readonly AppDbContext _db;

    public ProductRepository(AppDbContext db) => _db = db;

    public async Task<Product?> GetByIdAsync(int id, CancellationToken ct)
        => await _db.Products.AsNoTracking().FirstOrDefaultAsync(x => x.Id == id, ct);

    public async Task<(IReadOnlyList<Product> Items, int TotalCount)> GetPagedAsync(
        int page, int pageSize, CancellationToken ct)
    {
        var total = await _db.Products.CountAsync(ct);
        var items = await _db.Products
            .AsNoTracking()
            .OrderBy(x => x.Id)
            .Skip((page - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync(ct);
        return (items, total);
    }

    public async Task<Product> AddAsync(Product entity, CancellationToken ct)
    {
        _db.Products.Add(entity);
        await _db.SaveChangesAsync(ct);
        return entity;
    }
}
```

- `AsNoTracking()` for all read-only queries
- Always paginate list queries — never return unbounded results
- Accept `CancellationToken` in every async method

### DTOs

```csharp
public record ProductResponse(int Id, string Name, decimal Price, DateTime CreatedAt);
public record CreateProductRequest(string Name, decimal Price);
public record UpdateProductRequest(string Name, decimal Price);
```

- Use `record` types for immutability
- Never expose audit fields in request DTOs
- Keep response DTOs flat — avoid nested entity graphs

### Endpoints

```csharp
public static class ProductEndpoints
{
    public static RouteGroupBuilder MapProductEndpoints(this WebApplication app)
    {
        var group = app.MapGroup("/api/products").WithTags("Products");

        group.MapGet("/", GetAll);
        group.MapGet("/{id:int}", GetById);
        group.MapPost("/", Create);
        group.MapPut("/{id:int}", Update);
        group.MapDelete("/{id:int}", Delete);

        return group;
    }

    private static async Task<Ok<PagedResult<ProductResponse>>> GetAll(
        int page, int pageSize, IProductService svc, CancellationToken ct)
        => TypedResults.Ok(await svc.GetPagedAsync(page, pageSize, ct));

    private static async Task<Results<Ok<ProductResponse>, NotFound>> GetById(
        int id, IProductService svc, CancellationToken ct)
    {
        var result = await svc.GetByIdAsync(id, ct);
        return result is not null ? TypedResults.Ok(result) : TypedResults.NotFound();
    }

    private static async Task<Created<ProductResponse>> Create(
        CreateProductRequest req, IProductService svc, CancellationToken ct)
    {
        var result = await svc.CreateAsync(req, ct);
        return TypedResults.Created($"/api/products/{result.Id}", result);
    }
}
```

- `TypedResults` for OpenAPI schema generation
- `Results<T1, T2>` union types for multiple status codes
- Route constraints like `{id:int}` for type safety

## Decision Guide

| Question | Default | Alternative |
|----------|---------|-------------|
| Soft delete or hard delete? | Hard delete | Soft delete (`IsDeleted` flag) for audit-sensitive data |
| Sync or async validation? | Async (FluentValidation) | Sync only if no DB lookups needed in validator |
| AutoMapper or manual mapping? | Manual mapping | AutoMapper only if >10 DTOs to maintain |
| Repository per entity? | Yes | Shared generic repository only if entities are trivially similar |
| Pagination style? | Offset-based (`page`/`pageSize`) | Cursor-based for large datasets with real-time inserts |

## Common Mistakes to Avoid

- Returning `IQueryable` from repositories — always materialize in the repository
- Forgetting `AsNoTracking()` on read paths — causes unnecessary change tracking overhead
- Mapping entities to DTOs inside the endpoint — keep it in the service layer
- Missing `CancellationToken` propagation — pass it through every async call
- No validation on `Update` requests — create a separate `UpdateValidator`
- Exposing auto-increment IDs in create responses without using `Created()` with location header

## Additional Resources

For code templates, see [templates.md](templates.md).
