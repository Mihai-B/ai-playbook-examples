# CRUD Code Templates

Quick copy-paste templates for each layer. Replace `{Entity}` / `{Entities}` with actual names.

## Entity Template

```csharp
namespace Domain.Entities;

public class {Entity}
{
    public int Id { get; set; }
    // Add properties here
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }

    public override string ToString() => $"{Entity} #{Id}";
}
```

## EF Configuration Template

```csharp
namespace Infrastructure.Data.Configurations;

using Domain.Entities;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

public class {Entity}Configuration : IEntityTypeConfiguration<{Entity}>
{
    public void Configure(EntityTypeBuilder<{Entity}> builder)
    {
        builder.HasKey(x => x.Id);
        // builder.Property(x => x.Name).HasMaxLength(200).IsRequired();
        // builder.HasIndex(x => x.Name);
    }
}
```

## Repository Interface Template

```csharp
namespace Application.Interfaces;

using Domain.Entities;

public interface I{Entity}Repository
{
    Task<{Entity}?> GetByIdAsync(int id, CancellationToken ct);
    Task<(IReadOnlyList<{Entity}> Items, int TotalCount)> GetPagedAsync(int page, int pageSize, CancellationToken ct);
    Task<{Entity}> AddAsync({Entity} entity, CancellationToken ct);
    Task UpdateAsync({Entity} entity, CancellationToken ct);
    Task DeleteAsync({Entity} entity, CancellationToken ct);
}
```

## Repository Implementation Template

```csharp
namespace Infrastructure.Repositories;

using Application.Interfaces;
using Domain.Entities;
using Infrastructure.Data;
using Microsoft.EntityFrameworkCore;

public class {Entity}Repository : I{Entity}Repository
{
    private readonly AppDbContext _db;

    public {Entity}Repository(AppDbContext db) => _db = db;

    public async Task<{Entity}?> GetByIdAsync(int id, CancellationToken ct)
        => await _db.{Entities}.AsNoTracking().FirstOrDefaultAsync(x => x.Id == id, ct);

    public async Task<(IReadOnlyList<{Entity}> Items, int TotalCount)> GetPagedAsync(
        int page, int pageSize, CancellationToken ct)
    {
        var total = await _db.{Entities}.CountAsync(ct);
        var items = await _db.{Entities}
            .AsNoTracking()
            .OrderBy(x => x.Id)
            .Skip((page - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync(ct);
        return (items, total);
    }

    public async Task<{Entity}> AddAsync({Entity} entity, CancellationToken ct)
    {
        entity.CreatedAt = DateTime.UtcNow;
        _db.{Entities}.Add(entity);
        await _db.SaveChangesAsync(ct);
        return entity;
    }

    public async Task UpdateAsync({Entity} entity, CancellationToken ct)
    {
        entity.UpdatedAt = DateTime.UtcNow;
        _db.{Entities}.Update(entity);
        await _db.SaveChangesAsync(ct);
    }

    public async Task DeleteAsync({Entity} entity, CancellationToken ct)
    {
        _db.{Entities}.Remove(entity);
        await _db.SaveChangesAsync(ct);
    }
}
```

## DTO Templates

```csharp
namespace Application.{Entities};

public record {Entity}Response(
    int Id,
    // Add properties here
    DateTime CreatedAt);

public record Create{Entity}Request(
    // Add required creation fields
);

public record Update{Entity}Request(
    // Add updatable fields
);
```

## FluentValidation Template

```csharp
namespace Application.{Entities};

using FluentValidation;

public class Create{Entity}Validator : AbstractValidator<Create{Entity}Request>
{
    public Create{Entity}Validator()
    {
        // RuleFor(x => x.Name).NotEmpty().MaximumLength(200);
    }
}

public class Update{Entity}Validator : AbstractValidator<Update{Entity}Request>
{
    public Update{Entity}Validator()
    {
        // RuleFor(x => x.Name).NotEmpty().MaximumLength(200);
    }
}
```

## Service Interface Template

```csharp
namespace Application.{Entities};

public interface I{Entity}Service
{
    Task<{Entity}Response?> GetByIdAsync(int id, CancellationToken ct);
    Task<PagedResult<{Entity}Response>> GetPagedAsync(int page, int pageSize, CancellationToken ct);
    Task<{Entity}Response> CreateAsync(Create{Entity}Request request, CancellationToken ct);
    Task<{Entity}Response?> UpdateAsync(int id, Update{Entity}Request request, CancellationToken ct);
    Task<bool> DeleteAsync(int id, CancellationToken ct);
}
```

## Endpoint Group Template

```csharp
namespace Api.Endpoints;

using Application.{Entities};
using Microsoft.AspNetCore.Http.HttpResults;

public static class {Entity}Endpoints
{
    public static RouteGroupBuilder Map{Entity}Endpoints(this WebApplication app)
    {
        var group = app.MapGroup("/api/{entities}").WithTags("{Entities}");

        group.MapGet("/", GetAll);
        group.MapGet("/{id:int}", GetById);
        group.MapPost("/", Create);
        group.MapPut("/{id:int}", Update);
        group.MapDelete("/{id:int}", Delete);

        return group;
    }

    private static async Task<Ok<PagedResult<{Entity}Response>>> GetAll(
        int page, int pageSize, I{Entity}Service svc, CancellationToken ct)
        => TypedResults.Ok(await svc.GetPagedAsync(page, pageSize, ct));

    private static async Task<Results<Ok<{Entity}Response>, NotFound>> GetById(
        int id, I{Entity}Service svc, CancellationToken ct)
    {
        var result = await svc.GetByIdAsync(id, ct);
        return result is not null ? TypedResults.Ok(result) : TypedResults.NotFound();
    }

    private static async Task<Created<{Entity}Response>> Create(
        Create{Entity}Request req, I{Entity}Service svc, CancellationToken ct)
    {
        var result = await svc.CreateAsync(req, ct);
        return TypedResults.Created($"/api/{entities}/{result.Id}", result);
    }

    private static async Task<Results<Ok<{Entity}Response>, NotFound>> Update(
        int id, Update{Entity}Request req, I{Entity}Service svc, CancellationToken ct)
    {
        var result = await svc.UpdateAsync(id, req, ct);
        return result is not null ? TypedResults.Ok(result) : TypedResults.NotFound();
    }

    private static async Task<Results<NoContent, NotFound>> Delete(
        int id, I{Entity}Service svc, CancellationToken ct)
    {
        var deleted = await svc.DeleteAsync(id, ct);
        return deleted ? TypedResults.NoContent() : TypedResults.NotFound();
    }
}
```

## DI Registration (Program.cs)

```csharp
builder.Services.AddScoped<I{Entity}Repository, {Entity}Repository>();
builder.Services.AddScoped<I{Entity}Service, {Entity}Service>();

// After app = builder.Build()
app.Map{Entity}Endpoints();
```

## Test Template

```csharp
namespace UnitTests;

using Application.{Entities};
using Application.Interfaces;
using Domain.Entities;
using FluentAssertions;
using NSubstitute;

public class {Entity}ServiceTests
{
    private readonly I{Entity}Repository _repo = Substitute.For<I{Entity}Repository>();
    private readonly {Entity}Service _sut;

    public {Entity}ServiceTests() => _sut = new {Entity}Service(_repo);

    [Fact]
    public async Task GetByIdAsync_Exists_ReturnsResponse()
    {
        _repo.GetByIdAsync(1, Arg.Any<CancellationToken>())
            .Returns(new {Entity} { Id = 1 /* set properties */ });

        var result = await _sut.GetByIdAsync(1, CancellationToken.None);

        result.Should().NotBeNull();
        result!.Id.Should().Be(1);
    }

    [Fact]
    public async Task GetByIdAsync_NotFound_ReturnsNull()
    {
        _repo.GetByIdAsync(99, Arg.Any<CancellationToken>())
            .Returns(({Entity}?)null);

        var result = await _sut.GetByIdAsync(99, CancellationToken.None);

        result.Should().BeNull();
    }
}
```
