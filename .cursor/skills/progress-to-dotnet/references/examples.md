# Progress OpenEdge ABL to .NET Migration Examples

## Example 1: Procedure with Temp-Table Output → Service + DTO

### Before (Progress ABL)

```abl
/* GetCustomerOrders.p — Returns orders for a customer */
DEFINE INPUT  PARAMETER piCustNum   AS INTEGER   NO-UNDO.
DEFINE INPUT  PARAMETER pcStatus    AS CHARACTER NO-UNDO.
DEFINE OUTPUT PARAMETER TABLE FOR ttOrder.

DEFINE TEMP-TABLE ttOrder NO-UNDO
    FIELD OrderNum   AS INTEGER
    FIELD OrderDate  AS DATE
    FIELD ShipDate   AS DATE
    FIELD Total      AS DECIMAL FORMAT ">>>,>>9.99"
    FIELD Status     AS CHARACTER.

DEFINE VARIABLE dSubtotal AS DECIMAL NO-UNDO.

FOR EACH Order WHERE Order.CustNum = piCustNum
                 AND (pcStatus = "" OR Order.Status = pcStatus)
                 NO-LOCK
                 BY Order.OrderDate DESCENDING:

    dSubtotal = 0.

    FOR EACH OrderLine WHERE OrderLine.OrderNum = Order.OrderNum NO-LOCK:
        dSubtotal = dSubtotal + (OrderLine.Qty * OrderLine.Price).
    END.

    CREATE ttOrder.
    ASSIGN
        ttOrder.OrderNum  = Order.OrderNum
        ttOrder.OrderDate = Order.OrderDate
        ttOrder.ShipDate  = Order.ShipDate
        ttOrder.Total     = dSubtotal
        ttOrder.Status    = Order.Status.
END.
```

### After (.NET 8+)

**DTO** (`src/Application/Orders/OrderDto.cs`):

```csharp
namespace Application.Orders;

public record OrderDto(
    int OrderNum,
    DateOnly OrderDate,
    DateOnly? ShipDate,
    decimal Total,
    string Status);
```

**Repository** (`src/Infrastructure/Repositories/OrderRepository.cs`):

```csharp
namespace Infrastructure.Repositories;

public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _db;

    public OrderRepository(AppDbContext db) => _db = db;

    public async Task<IReadOnlyList<Order>> GetByCustomerAsync(
        int custNum, string? status, CancellationToken ct)
    {
        var query = _db.Orders
            .Include(o => o.OrderLines)
            .Where(o => o.CustNum == custNum);

        if (!string.IsNullOrEmpty(status))
            query = query.Where(o => o.Status == status);

        return await query
            .OrderByDescending(o => o.OrderDate)
            .AsNoTracking()
            .ToListAsync(ct);
    }
}
```

**Service** (`src/Infrastructure/Services/OrderService.cs`):

```csharp
namespace Infrastructure.Services;

public class OrderService : IOrderService
{
    private readonly IOrderRepository _repository;

    public OrderService(IOrderRepository repository)
    {
        _repository = repository;
    }

    public async Task<IReadOnlyList<OrderDto>> GetByCustomerAsync(
        int custNum, string? status, CancellationToken ct)
    {
        var orders = await _repository.GetByCustomerAsync(custNum, status, ct);

        return orders.Select(o => new OrderDto(
            o.OrderNum,
            o.OrderDate,
            o.ShipDate,
            o.OrderLines.Sum(ol => ol.Qty * ol.Price),
            o.Status)).ToList();
    }
}
```

**Endpoint** (`src/Api/Endpoints/OrderEndpoints.cs`):

```csharp
namespace Api.Endpoints;

public static class OrderEndpoints
{
    public static RouteGroupBuilder MapOrderEndpoints(this WebApplication app)
    {
        var group = app.MapGroup("/api/orders").WithTags("Orders");

        group.MapGet("/by-customer/{custNum:int}", GetByCustomer);

        return group;
    }

    private static async Task<Ok<IReadOnlyList<OrderDto>>> GetByCustomer(
        int custNum, string? status, IOrderService svc, CancellationToken ct)
        => TypedResults.Ok(await svc.GetByCustomerAsync(custNum, status, ct));
}
```

### What Changed

| Progress ABL | .NET |
|-------------|------|
| `DEFINE TEMP-TABLE ttOrder` | `record OrderDto(...)` |
| `DEFINE INPUT PARAMETER piCustNum AS INTEGER` | Method parameter `int custNum` |
| `DEFINE OUTPUT PARAMETER TABLE FOR ttOrder` | Return type `IReadOnlyList<OrderDto>` |
| `FOR EACH Order WHERE ... NO-LOCK` | `.Where(...).AsNoTracking().ToListAsync()` |
| Nested `FOR EACH OrderLine` to sum | `.OrderLines.Sum(ol => ol.Qty * ol.Price)` in LINQ |
| `CREATE ttOrder. ASSIGN ...` | `new OrderDto(...)` projection in `Select()` |
| `BY Order.OrderDate DESCENDING` | `.OrderByDescending(o => o.OrderDate)` |
| `pcStatus = "" OR Order.Status = pcStatus` | Conditional `.Where()` with `string.IsNullOrEmpty` |
| `RUN GetCustomerOrders.p (...)` caller | `await _orderService.GetByCustomerAsync(...)` |

---

## Example 2: CRUD Persistent Procedure → Service Class

### Before (Progress ABL)

```abl
/* CustomerCRUD.p — Persistent procedure for customer management */

DEFINE TEMP-TABLE ttCustomer NO-UNDO
    FIELD CustNum  AS INTEGER
    FIELD Name     AS CHARACTER FORMAT "x(30)"
    FIELD Address  AS CHARACTER FORMAT "x(50)"
    FIELD City     AS CHARACTER FORMAT "x(20)"
    FIELD State    AS CHARACTER FORMAT "x(2)"
    FIELD Balance  AS DECIMAL   FORMAT "->>>,>>9.99"
    FIELD CreditLimit AS DECIMAL FORMAT "->>>,>>9.99".

PROCEDURE GetCustomer:
    DEFINE INPUT  PARAMETER piCustNum AS INTEGER NO-UNDO.
    DEFINE OUTPUT PARAMETER TABLE FOR ttCustomer.

    FIND Customer WHERE Customer.CustNum = piCustNum NO-LOCK NO-ERROR.
    IF NOT AVAILABLE Customer THEN
        RETURN ERROR SUBSTITUTE("Customer &1 not found", piCustNum).

    CREATE ttCustomer.
    BUFFER-COPY Customer TO ttCustomer.
END PROCEDURE.

PROCEDURE CreateCustomer:
    DEFINE INPUT PARAMETER TABLE FOR ttCustomer.

    FIND FIRST ttCustomer NO-ERROR.
    IF NOT AVAILABLE ttCustomer THEN
        RETURN ERROR "No customer data provided".

    DO TRANSACTION:
        CREATE Customer.
        BUFFER-COPY ttCustomer EXCEPT CustNum TO Customer.
        /* CustNum is auto-assigned by sequence */
        Customer.CustNum = NEXT-VALUE(NextCustNum).
    END.
END PROCEDURE.

PROCEDURE UpdateCustomer:
    DEFINE INPUT PARAMETER TABLE FOR ttCustomer.

    FIND FIRST ttCustomer NO-ERROR.
    IF NOT AVAILABLE ttCustomer THEN
        RETURN ERROR "No customer data provided".

    FIND Customer WHERE Customer.CustNum = ttCustomer.CustNum
        EXCLUSIVE-LOCK NO-ERROR.
    IF NOT AVAILABLE Customer THEN
        RETURN ERROR SUBSTITUTE("Customer &1 not found", ttCustomer.CustNum).

    BUFFER-COPY ttCustomer EXCEPT CustNum TO Customer.
END PROCEDURE.

PROCEDURE DeleteCustomer:
    DEFINE INPUT PARAMETER piCustNum AS INTEGER NO-UNDO.

    FIND Customer WHERE Customer.CustNum = piCustNum EXCLUSIVE-LOCK NO-ERROR.
    IF NOT AVAILABLE Customer THEN
        RETURN ERROR SUBSTITUTE("Customer &1 not found", piCustNum).

    IF Customer.Balance <> 0 THEN
        RETURN ERROR "Cannot delete customer with outstanding balance".

    FOR EACH Order WHERE Order.CustNum = piCustNum:
        FOR EACH OrderLine WHERE OrderLine.OrderNum = Order.OrderNum:
            DELETE OrderLine.
        END.
        DELETE Order.
    END.

    DELETE Customer.
END PROCEDURE.
```

### After (.NET 8+)

**Entity** (`src/Domain/Entities/Customer.cs`):

```csharp
namespace Domain.Entities;

public class Customer
{
    public int CustNum { get; set; }
    public required string Name { get; set; }
    public string? Address { get; set; }
    public string? City { get; set; }
    public string? State { get; set; }
    public decimal Balance { get; set; }
    public decimal CreditLimit { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }

    public ICollection<Order> Orders { get; set; } = [];
}
```

**DTOs** (`src/Application/Customers/`):

```csharp
namespace Application.Customers;

public record CustomerResponse(
    int CustNum, string Name, string? Address,
    string? City, string? State, decimal Balance, decimal CreditLimit);

public record CreateCustomerRequest(
    string Name, string? Address, string? City,
    string? State, decimal CreditLimit);

public record UpdateCustomerRequest(
    string Name, string? Address, string? City,
    string? State, decimal CreditLimit);
```

**Service** (`src/Infrastructure/Services/CustomerService.cs`):

```csharp
namespace Infrastructure.Services;

public class CustomerService : ICustomerService
{
    private readonly ICustomerRepository _repository;
    private readonly TimeProvider _timeProvider;

    public CustomerService(ICustomerRepository repository, TimeProvider timeProvider)
    {
        _repository = repository;
        _timeProvider = timeProvider;
    }

    public async Task<CustomerResponse?> GetByIdAsync(int custNum, CancellationToken ct)
    {
        var customer = await _repository.GetByIdAsync(custNum, ct);
        return customer is null ? null : MapToResponse(customer);
    }

    public async Task<CustomerResponse> CreateAsync(
        CreateCustomerRequest request, CancellationToken ct)
    {
        var customer = new Customer
        {
            Name = request.Name,
            Address = request.Address,
            City = request.City,
            State = request.State,
            CreditLimit = request.CreditLimit,
            Balance = 0,
            CreatedAt = _timeProvider.GetUtcNow().DateTime
        };

        var created = await _repository.AddAsync(customer, ct);
        return MapToResponse(created);
    }

    public async Task UpdateAsync(int custNum, UpdateCustomerRequest request, CancellationToken ct)
    {
        var customer = await _repository.GetByIdAsync(custNum, ct)
            ?? throw new KeyNotFoundException($"Customer {custNum} not found");

        customer.Name = request.Name;
        customer.Address = request.Address;
        customer.City = request.City;
        customer.State = request.State;
        customer.CreditLimit = request.CreditLimit;
        customer.UpdatedAt = _timeProvider.GetUtcNow().DateTime;

        await _repository.UpdateAsync(customer, ct);
    }

    public async Task DeleteAsync(int custNum, CancellationToken ct)
    {
        var customer = await _repository.GetByIdAsync(custNum, ct)
            ?? throw new KeyNotFoundException($"Customer {custNum} not found");

        if (customer.Balance != 0)
            throw new InvalidOperationException("Cannot delete customer with outstanding balance");

        await _repository.DeleteWithOrdersAsync(custNum, ct);
    }

    private static CustomerResponse MapToResponse(Customer c) => new(
        c.CustNum, c.Name, c.Address, c.City, c.State, c.Balance, c.CreditLimit);
}
```

### What Changed

| Progress ABL | .NET |
|-------------|------|
| Persistent procedure with internal procedures | Scoped service class with methods |
| `DEFINE TEMP-TABLE ttCustomer` | `record CustomerResponse` / `CreateCustomerRequest` / `UpdateCustomerRequest` |
| `FIND Customer WHERE ... NO-LOCK NO-ERROR` + `AVAILABLE` | `await _repository.GetByIdAsync(...)` + null check |
| `FIND ... EXCLUSIVE-LOCK` | Default EF tracking query (for update) |
| `BUFFER-COPY Customer TO ttCustomer` | Manual mapping in `MapToResponse()` |
| `BUFFER-COPY ttCustomer TO Customer` | Property assignments from request DTO |
| `CREATE Customer` | `_db.Customers.Add(new Customer { ... })` |
| `DELETE Customer` | `_db.Customers.Remove(entity)` |
| `RETURN ERROR "message"` | `throw new KeyNotFoundException(...)` or `throw new InvalidOperationException(...)` |
| `DO TRANSACTION:` block | EF `SaveChangesAsync()` (implicit transaction) |
| `NEXT-VALUE(NextCustNum)` | Database-generated identity or sequence |
| `RUN CustomerCRUD.p PERSISTENT SET hProc` | `builder.Services.AddScoped<ICustomerService, CustomerService>()` |

---

## Example 3: Business Logic with String Manipulation → Utility Class

### Before (Progress ABL)

```abl
/* FormatUtils.i — Include file with formatting functions */

FUNCTION FormatPhoneNumber RETURNS CHARACTER (pcPhone AS CHARACTER):
    DEFINE VARIABLE cDigits AS CHARACTER NO-UNDO.
    DEFINE VARIABLE i       AS INTEGER   NO-UNDO.
    DEFINE VARIABLE c       AS CHARACTER NO-UNDO.

    DO i = 1 TO LENGTH(pcPhone):
        c = SUBSTRING(pcPhone, i, 1).
        IF c >= "0" AND c <= "9" THEN
            cDigits = cDigits + c.
    END.

    IF LENGTH(cDigits) <> 10 THEN
        RETURN pcPhone. /* Return as-is if not 10 digits */

    RETURN SUBSTITUTE("(&1) &2-&3",
        SUBSTRING(cDigits, 1, 3),
        SUBSTRING(cDigits, 4, 3),
        SUBSTRING(cDigits, 7, 4)).
END FUNCTION.

FUNCTION ParseDelimitedList RETURNS INTEGER (pcList AS CHARACTER,
                                             pcDelim AS CHARACTER):
    RETURN NUM-ENTRIES(pcList, pcDelim).
END FUNCTION.

FUNCTION GetListEntry RETURNS CHARACTER (pcList AS CHARACTER,
                                          piEntry AS INTEGER,
                                          pcDelim AS CHARACTER):
    IF piEntry < 1 OR piEntry > NUM-ENTRIES(pcList, pcDelim) THEN
        RETURN "".
    RETURN ENTRY(piEntry, pcList, pcDelim).
END FUNCTION.

FUNCTION BuildWhereClause RETURNS CHARACTER (pcField AS CHARACTER,
                                              pcOperator AS CHARACTER,
                                              pcValue AS CHARACTER):
    CASE pcOperator:
        WHEN "EQ" THEN RETURN SUBSTITUTE("&1 = '&2'", pcField, pcValue).
        WHEN "NE" THEN RETURN SUBSTITUTE("&1 <> '&2'", pcField, pcValue).
        WHEN "GT" THEN RETURN SUBSTITUTE("&1 > '&2'", pcField, pcValue).
        WHEN "LT" THEN RETURN SUBSTITUTE("&1 < '&2'", pcField, pcValue).
        WHEN "BEGINS" THEN RETURN SUBSTITUTE("&1 BEGINS '&2'", pcField, pcValue).
        OTHERWISE RETURN "".
    END CASE.
END FUNCTION.
```

### After (.NET 8+)

```csharp
namespace Application.Common;

public static class FormatExtensions
{
    public static string FormatAsPhoneNumber(this string phone)
    {
        var digits = new string(phone.Where(char.IsDigit).ToArray());

        return digits.Length == 10
            ? $"({digits[..3]}) {digits[3..6]}-{digits[6..]}"
            : phone;
    }
}
```

```csharp
namespace Application.Common;

public static class DelimitedListExtensions
{
    public static string[] SplitDelimited(this string list, char delimiter = ',')
        => list.Split(delimiter, StringSplitOptions.TrimEntries);

    public static string? GetEntry(this string list, int oneBasedIndex, char delimiter = ',')
    {
        var entries = list.SplitDelimited(delimiter);
        var index = oneBasedIndex - 1;
        return index >= 0 && index < entries.Length ? entries[index] : null;
    }
}
```

> **Note**: `BuildWhereClause` is an anti-pattern (SQL string concatenation). Replace with EF Core LINQ expressions or a specification pattern — never build SQL strings manually.

### What Changed

| Progress ABL | .NET |
|-------------|------|
| `FUNCTION` in include file (`.i`) | Static extension methods in shared class |
| `{FormatUtils.i}` include | `using Application.Common;` import |
| `SUBSTRING(str, 1, 3)` (1-based) | `str[..3]` (0-based range) |
| `LENGTH(str)` | `str.Length` |
| `SUBSTITUTE("&1 &2", a, b)` | `$"{a} {b}"` string interpolation |
| `NUM-ENTRIES(list, delim)` | `list.Split(delim).Length` |
| `ENTRY(n, list, delim)` | `list.Split(delim)[n - 1]` (0-based) |
| String concatenation in loop | `Where(char.IsDigit)` LINQ filter |
| `BuildWhereClause` (SQL strings) | EF Core LINQ expressions (never build raw SQL) |

---

## Example 4: AppServer Call → HTTP Client / Service

### Before (Progress ABL)

```abl
/* Client-side code calling AppServer */
DEFINE VARIABLE hServer   AS HANDLE  NO-UNDO.
DEFINE VARIABLE lSuccess  AS LOGICAL NO-UNDO.

CREATE SERVER hServer.
hServer:CONNECT("-AppService InventoryService -H appserver.company.com -S 5162").

RUN CheckInventory ON hServer
    (INPUT "WIDGET-A",
     INPUT 100,
     OUTPUT lSuccess).

IF lSuccess THEN
    RUN ReserveStock ON hServer
        (INPUT "WIDGET-A",
         INPUT 100,
         INPUT "ORD-2024-001").

hServer:DISCONNECT().
DELETE OBJECT hServer.
```

```abl
/* Server-side: CheckInventory.p */
DEFINE INPUT  PARAMETER pcSKU    AS CHARACTER NO-UNDO.
DEFINE INPUT  PARAMETER piQty    AS INTEGER   NO-UNDO.
DEFINE OUTPUT PARAMETER plAvail  AS LOGICAL   NO-UNDO.

FIND Inventory WHERE Inventory.SKU = pcSKU NO-LOCK NO-ERROR.
IF AVAILABLE Inventory AND Inventory.QtyOnHand >= piQty THEN
    plAvail = TRUE.
ELSE
    plAvail = FALSE.
```

### After (.NET 8+)

**Service interface** (`src/Application/Inventory/IInventoryService.cs`):

```csharp
namespace Application.Inventory;

public interface IInventoryService
{
    Task<bool> CheckAvailabilityAsync(string sku, int quantity, CancellationToken ct);
    Task ReserveStockAsync(string sku, int quantity, string orderRef, CancellationToken ct);
}
```

**Service implementation** (`src/Infrastructure/Services/InventoryService.cs`):

```csharp
namespace Infrastructure.Services;

public class InventoryService : IInventoryService
{
    private readonly AppDbContext _db;

    public InventoryService(AppDbContext db) => _db = db;

    public async Task<bool> CheckAvailabilityAsync(
        string sku, int quantity, CancellationToken ct)
    {
        var inventory = await _db.Inventory
            .AsNoTracking()
            .FirstOrDefaultAsync(i => i.Sku == sku, ct);

        return inventory is not null && inventory.QtyOnHand >= quantity;
    }

    public async Task ReserveStockAsync(
        string sku, int quantity, string orderRef, CancellationToken ct)
    {
        var inventory = await _db.Inventory
            .FirstOrDefaultAsync(i => i.Sku == sku, ct)
            ?? throw new KeyNotFoundException($"SKU {sku} not found");

        if (inventory.QtyOnHand < quantity)
            throw new InvalidOperationException(
                $"Insufficient stock for {sku}: requested {quantity}, available {inventory.QtyOnHand}");

        inventory.QtyOnHand -= quantity;
        inventory.QtyReserved += quantity;

        _db.StockReservations.Add(new StockReservation
        {
            Sku = sku,
            Quantity = quantity,
            OrderRef = orderRef,
            ReservedAt = DateTime.UtcNow
        });

        await _db.SaveChangesAsync(ct);
    }
}
```

**Caller** (previously the AppServer client code):

```csharp
public class OrderFulfillmentService
{
    private readonly IInventoryService _inventory;

    public OrderFulfillmentService(IInventoryService inventory)
    {
        _inventory = inventory;
    }

    public async Task FulfillOrderAsync(
        string sku, int qty, string orderRef, CancellationToken ct)
    {
        var available = await _inventory.CheckAvailabilityAsync(sku, qty, ct);

        if (available)
            await _inventory.ReserveStockAsync(sku, qty, orderRef, ct);
    }
}
```

### What Changed

| Progress ABL | .NET |
|-------------|------|
| `CREATE SERVER` / `CONNECT` / `DISCONNECT` / `DELETE OBJECT` | DI-injected `IInventoryService` (no manual connection management) |
| `RUN CheckInventory ON hServer (INPUT ..., OUTPUT ...)` | `await _inventory.CheckAvailabilityAsync(...)` |
| `RUN ReserveStock ON hServer (INPUT ...)` | `await _inventory.ReserveStockAsync(...)` |
| `DEFINE OUTPUT PARAMETER plAvail AS LOGICAL` | `Task<bool>` return type |
| `FIND Inventory WHERE ... NO-LOCK NO-ERROR` + `AVAILABLE` | `FirstOrDefaultAsync(...)` + null check |
| `Inventory.QtyOnHand >= piQty` → `plAvail = TRUE` | `return inventory is not null && inventory.QtyOnHand >= quantity` |
| AppServer connection string (`-H`, `-S`, `-AppService`) | DI registration: `builder.Services.AddScoped<IInventoryService, InventoryService>()` |
| Two separate `.p` files (client + server) | Single service class, injected wherever needed |
