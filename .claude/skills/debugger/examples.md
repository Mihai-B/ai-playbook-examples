# Debugger Examples

## Example 1: Validation bug

**Input**: `The POST /api/users endpoint returns 500 when email contains a plus sign`

**Output**:

```
## Root Cause
The Zod email validation schema uses a custom regex that rejects `+` characters
in the local part of the email address (e.g., `user+tag@example.com`). The built-in
`z.string().email()` validator handles this correctly.

## Fix Applied
- **File**: src/api/routes/users.ts (line 12)
- **Change**: Replaced custom email regex with `z.string().email().max(255)`

## Verification
- `npm run test -- UserRoutes` — 14 tests passed (including new test for plus-sign emails)
- Manual test: POST /api/users with `user+test@example.com` now returns 201
```

## Example 2: Race condition

**Input**: `Users intermittently see stale data after updating their profile`

**Output**:

```
## Root Cause
The `updateProfile` handler writes to the database but returns the cached version
from the in-memory store. The cache invalidation runs asynchronously after the
response is sent, creating a window where the client receives stale data.

## Fix Applied
- **File**: src/services/ProfileService.ts (line 67)
- **Change**: Moved cache invalidation before the response. Used `await cache.del(userId)`
  instead of the fire-and-forget `cache.del(userId)` (no await).

## Verification
- `npm run test -- ProfileService` — 8 tests passed
- Added new test: `should return updated data immediately after save` — passes
```
