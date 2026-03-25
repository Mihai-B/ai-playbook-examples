# Refactor Examples

## Example: Long function decomposition

**Input**: `Refactor src/services/PaymentService.ts — the processPayment function is 120 lines`

**Output**:

```
## Changes Made

### 1. Extracted validation into `validatePaymentRequest`
- **Before**: 30 lines of inline validation checks at the top of `processPayment`
- **After**: Dedicated function with early returns and clear error messages
- **Why**: Single responsibility — validation logic is now testable in isolation

### 2. Extracted retry logic into `withRetry` utility
- **Before**: Nested try/catch with manual retry counter and sleep
- **After**: Generic `withRetry(fn, { maxAttempts: 3, backoff: 'exponential' })`
- **Why**: Same pattern was duplicated in RefundService — now shared

### 3. Replaced nested if/else with strategy map
- **Before**: 5-level deep if/else chain for payment method routing
- **After**: `const handlers: Record<PaymentMethod, PaymentHandler> = { ... }`
- **Why**: Adding a new payment method now requires one line, not restructuring the chain

## Tests Updated
- Added tests for `validatePaymentRequest`
- Updated `processPayment` tests to use new function signatures
- All 23 existing tests still pass
```

## Example: React component simplification

**Input**: `Refactor src/components/Dashboard.tsx — too much logic in the component`

**Output**:

```
## Changes Made

### 1. Extracted data fetching into `useDashboardData` hook
- **Before**: 45 lines of useState + useEffect + error handling inline
- **After**: `const { data, isLoading, error } = useDashboardData(userId)`
- **Why**: Component now focuses on rendering; data logic is testable independently

### 2. Replaced inline filter logic with `filterActiveTasks`
- **Before**: Complex `.filter().map().sort()` chain repeated twice in render
- **After**: Single `filterActiveTasks(tasks, criteria)` utility
- **Why**: Eliminates duplication and makes the filtering logic unit-testable

## Tests Updated
- Added `useDashboardData.test.ts` with 6 tests
- Updated `Dashboard.test.tsx` to mock the new hook
- All 12 existing tests still pass
```
