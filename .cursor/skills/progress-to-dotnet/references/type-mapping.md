# Progress OpenEdge ABL to C# Type Mapping Reference

## Primitive Types

| Progress ABL | C# | Notes |
|-------------|-----|-------|
| `INTEGER` | `int` | |
| `INT64` | `long` | |
| `DECIMAL` | `decimal` | Progress default is 2 decimal places; C# `decimal` handles arbitrary precision |
| `LOGICAL` | `bool` | Progress uses `YES`/`NO` or `TRUE`/`FALSE`; C# uses `true`/`false` |
| `CHARACTER` | `string` | Progress strings are mutable; C# strings are immutable |
| `LONGCHAR` | `string` | For large text; no size distinction needed in C# |
| `DATE` | `DateOnly` | .NET 6+ `DateOnly`; use `DateTime` if time component needed |
| `DATETIME` | `DateTime` | |
| `DATETIME-TZ` | `DateTimeOffset` | Preserves timezone offset |
| `HANDLE` | — | No direct equivalent; replace with typed reference or DI service |
| `ROWID` | `Guid` or `int` | Map to entity primary key type |
| `RECID` | `int` or `long` | Legacy; map to entity primary key |
| `RAW` | `byte[]` | |
| `MEMPTR` | `byte[]` or `Memory<byte>` | Use `Memory<byte>` for high-performance scenarios |
| `BLOB` | `byte[]` | Map to entity `byte[]` property |
| `CLOB` | `string` | Map to `string` with appropriate max length |
| `COM-HANDLE` | — | Replace with managed .NET interop or HTTP client |

## Collection / Data Types

| Progress ABL | C# | Notes |
|-------------|-----|-------|
| `TEMP-TABLE` | `record` DTO or entity class | Define as `record` for DTOs; `class` for EF entities |
| `ProDataSet` | Aggregate DTO or response class | Contains multiple related collections |
| `BUFFER` | Entity variable / `DbSet<T>` | `DEFINE BUFFER` → local variable from DbSet query |
| `QUERY` | `IQueryable<T>` | `OPEN QUERY` → LINQ chain; `GET NEXT` → enumeration |
| `EXTENT` (array) | `T[]` or `List<T>` | `DEFINE VARIABLE x AS INTEGER EXTENT 10` → `int[]` |
| `DATASET-HANDLE` | `object` | Avoid; use strongly-typed aggregate DTOs |
| `TABLE-HANDLE` | `object` | Avoid; use strongly-typed `List<T>` |

## Database Operations

| Progress ABL | C# / .NET | Notes |
|-------------|-----------|-------|
| `FIND Customer WHERE ...` | `_db.Customers.FirstOrDefaultAsync(...)` | `FIND FIRST` → `FirstOrDefaultAsync`; `FIND LAST` → `OrderByDescending().FirstOrDefaultAsync()` |
| `FIND ... NO-ERROR` + `AVAILABLE` | `var x = await ...; if (x is not null)` | Replace `AVAILABLE(buffer)` with null check |
| `FOR EACH ... NO-LOCK` | `.Where(...).AsNoTracking().ToListAsync()` | `NO-LOCK` maps to `AsNoTracking()` |
| `FOR EACH ... EXCLUSIVE-LOCK` | `.Where(...).ToListAsync()` | Default EF tracking (for updates) |
| `CREATE Customer` | `_db.Customers.Add(new Customer { ... })` | |
| `DELETE Customer` | `_db.Customers.Remove(entity)` | Or `ExecuteDeleteAsync()` for bulk |
| `ASSIGN field = value` | `entity.Property = value` | Or object initializer syntax |
| `VALIDATE Customer` | `_db.SaveChangesAsync()` | Validation happens at save |
| `RELEASE Customer` | — | Not needed; EF tracks changes automatically |
| `CAN-FIND(Customer WHERE ...)` | `_db.Customers.AnyAsync(...)` | |
| `TRANSACTION` block | `_db.SaveChangesAsync()` or `BeginTransactionAsync()` | EF wraps `SaveChangesAsync` in a transaction; use explicit transactions for multi-context operations |
| `UNDO, THROW` | `throw new ...` | Standard C# exception |
| `UNDO, RETRY` | Retry loop or Polly | Use Polly for resilience patterns |

## Parameter Mapping

| Progress ABL | C# | Notes |
|-------------|-----|-------|
| `INPUT parameter` | Method parameter | `DEFINE INPUT PARAMETER pId AS INTEGER` → `int pId` |
| `OUTPUT parameter` | Return type or `out` | Prefer return type; use `out` sparingly |
| `INPUT-OUTPUT parameter` | `ref` parameter or return | Prefer transforming to return value |
| `TABLE parameter` (input) | `IReadOnlyList<T>` parameter | `DEFINE INPUT PARAMETER TABLE FOR tt` → `IReadOnlyList<TDto>` |
| `TABLE parameter` (output) | `IReadOnlyList<T>` return | `DEFINE OUTPUT PARAMETER TABLE FOR tt` → return `IReadOnlyList<TDto>` |
| `DATASET parameter` | Aggregate DTO | `DEFINE OUTPUT PARAMETER DATASET FOR ds` → return aggregate DTO |

## Procedure / Program Mapping

| Progress ABL | C# | Notes |
|-------------|-----|-------|
| `.p` (procedure) | Service class | Each procedure becomes a method; related procedures group into a service |
| `.cls` (class) | C# class | Direct mapping; add DI and interfaces |
| `.w` (window) | API endpoint or Blazor page | Separate UI triggers from business logic |
| `.i` (include file) | Shared class / constants / extension method | `{file.i}` includes become `using` imports or shared utilities |
| `RUN procedure.p` | Service method call | `RUN calc.p (INPUT x, OUTPUT y)` → `var y = await _calcService.CalculateAsync(x, ct)` |
| `RUN ... ON SERVER hAppSrv` | HTTP client call or service call | AppServer calls become API calls or DI service calls |
| `PERSISTENT RUN` | Scoped DI service | Persistent procedure lifetime → DI scoped lifetime |
| `DELETE PROCEDURE hProc` | — | Lifetime managed by DI container |
| `PUBLISH "event"` | MediatR notification or .NET event | `PUBLISH "OrderCreated"` → `await _mediator.Publish(new OrderCreated(...))` |
| `SUBSCRIBE TO "event"` | MediatR handler or event subscription | `SUBSCRIBE TO "OrderCreated"` → `INotificationHandler<OrderCreated>` |
| `THIS-PROCEDURE` | `this` | Current class instance |
| `SUPER:method()` | `base.Method()` | Inheritance call |
| `SESSION:` attributes | `IHttpContextAccessor` or config | `SESSION:DATE-FORMAT` → culture settings; `SESSION:TEMP-DIRECTORY` → config |

## Error Handling

| Progress ABL | C# | Notes |
|-------------|-----|-------|
| `NO-ERROR` + `ERROR-STATUS:ERROR` | `try/catch` | `FIND ... NO-ERROR. IF ERROR-STATUS:ERROR THEN ...` → `try { ... } catch { ... }` |
| `RETURN ERROR "msg"` | `throw new InvalidOperationException("msg")` | Use specific exception types |
| `UNDO, THROW NEW AppError(...)` | `throw new DomainException(...)` | Map to custom exception hierarchy |
| `CATCH e AS Progress.Lang.Error` | `catch (Exception ex)` | Use specific catch types |
| `ROUTINE-LEVEL ON ERROR UNDO, THROW` | Global exception handler | Configure in `Program.cs` with `UseExceptionHandler` |
| `BLOCK-LEVEL ON ERROR UNDO, THROW` | Method-level `try/catch` | |
| `RETURN ERROR-STATUS:GET-MESSAGE(1)` | `ex.Message` | |

## UI / Presentation Mapping

| Progress ABL | .NET Equivalent | Notes |
|-------------|-----------------|-------|
| `DEFINE FRAME` | API response shape / Blazor layout | |
| `DISPLAY field` | Include in response DTO | |
| `ENABLE field` | Editable input in request DTO | |
| `UPDATE field` | `PUT`/`PATCH` endpoint | |
| `WAIT-FOR` | — | Event loop; remove in API context |
| `MESSAGE ... VIEW-AS ALERT-BOX` | `ILogger.LogWarning()` or error response | |
| `APPLY "CHOOSE"` | — | UI trigger; replace with endpoint handler |
| `APPLY "CLOSE"` | — | Navigation; remove in API context |
| `BROWSE` widget | HTML table / data grid component | |
| `CHOOSE ROW` / `VALUE-CHANGED` | Click/change handler | |

## Pattern Replacements

| Progress ABL Pattern | C# Pattern |
|---------------------|------------|
| `NEW ClassName()` | Constructor injection via DI |
| `DELETE OBJECT obj` | Let GC / DI handle lifetime |
| `VALID-OBJECT(obj)` | `obj is not null` |
| `obj:Method()` | `obj.Method()` |
| `DYNAMIC-NEW` | Factory pattern via DI |
| `DEFINE ... SHARED` | Inject via DI (scoped service) |
| `DEFINE ... NEW SHARED` | Register + inject via DI |
| `DEFINE STREAM` / `INPUT FROM` / `OUTPUT TO` | `StreamReader` / `StreamWriter` or `File.ReadAllLinesAsync` |
| `SUBSTITUTE("&1 &2", a, b)` | `$"{a} {b}"` (string interpolation) |
| `STRING(value)` | `value.ToString()` |
| `INTEGER(value)` | `int.Parse(value)` or `Convert.ToInt32(value)` |
| `DECIMAL(value)` | `decimal.Parse(value)` |
| `TRIM(value)` | `value.Trim()` |
| `INDEX(str, sub)` | `str.IndexOf(sub)` (0-based; Progress is 1-based) |
| `SUBSTRING(str, pos, len)` | `str.Substring(pos - 1, len)` or `str[(pos-1)..(pos-1+len)]` |
| `ENTRY(n, list, delim)` | `list.Split(delim)[n - 1]` |
| `NUM-ENTRIES(list, delim)` | `list.Split(delim).Length` |
| `REPLACE(str, old, new)` | `str.Replace(old, new)` |
| `CAPS(str)` / `LC(str)` | `str.ToUpperInvariant()` / `str.ToLowerInvariant()` |
| `BEGINS` | `str.StartsWith(...)` |
| `MATCHES` | `Regex.IsMatch(...)` |
| `TODAY` | `DateOnly.FromDateTime(DateTime.UtcNow)` |
| `NOW` | `DateTime.UtcNow` or `TimeProvider.GetUtcNow()` |
| `INTERVAL(dt1, dt2, "days")` | `(dt1 - dt2).Days` |
| `ADD-INTERVAL(dt, n, "months")` | `dt.AddMonths(n)` |
