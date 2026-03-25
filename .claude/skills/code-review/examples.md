# Code Review Examples

## Example 1: Service with multiple issues

**Input**: `Review src/services/OrderService.ts`

**Output**:

```
### [Bug] Quantity can be negative
**Severity**: Critical
**Location**: src/services/OrderService.ts:34
**Issue**: No validation prevents negative `quantity` values, allowing negative-total orders
**Fix**: Add `z.number().int().positive()` to the order schema

### [Performance] Unbounded query in getOrders
**Severity**: Warning
**Location**: src/services/OrderService.ts:58
**Issue**: `findMany()` has no `take` limit — will fetch all orders for large accounts
**Fix**: Add default pagination: `take: 20, skip: (page - 1) * 20`

### [Best Practices] Business logic in route handler
**Severity**: Suggestion
**Location**: src/api/routes/orders.ts:22-45
**Issue**: Price calculation and discount logic lives in the route handler instead of the service layer
**Fix**: Move to `OrderService.calculateTotal()` for testability and reuse

**Summary**: 1 critical bug, 1 performance warning, 1 suggestion. Fix the negative quantity validation before merge.
```

## Example 2: Clean code with minor suggestion

**Input**: `Review src/services/NotificationService.ts`

**Output**:

```
### [Best Practices] Generic error message
**Severity**: Suggestion
**Location**: src/services/NotificationService.ts:41
**Issue**: Catch block logs "Something went wrong" — not helpful for debugging
**Fix**: Log the actual error: `logger.error("Failed to send notification", { error, userId })`

**Summary**: No bugs or security issues. 1 minor suggestion. Approve.
```
