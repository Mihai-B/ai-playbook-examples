# Explain Code Examples

## Example 1: Middleware explanation

**Input**: `Explain the authentication middleware in src/middleware/auth.ts`

**Output**:

```
## How the Auth Middleware Works

**Overview**: This middleware runs before protected route handlers to verify the caller's identity and attach their profile to the request.

### Flow

1. **Extract token**: Reads the `Authorization` header and strips the "Bearer " prefix.
   If missing, returns 401 immediately — no token means no access.

2. **Verify JWT**: Uses `jsonwebtoken.verify()` with the secret from `config.JWT_SECRET`.
   This checks both the signature (was it issued by us?) and expiry (is it still valid?).

3. **Attach user to request**: On success, decodes the token payload and sets `req.user`
   with `{ id, email, role }`. This is how downstream handlers know who's calling.

4. **Error handling**: Catches two specific JWT errors:
   - `TokenExpiredError` → 401 with code `TOKEN_EXPIRED` (client should refresh)
   - `JsonWebTokenError` → 401 with code `INVALID_TOKEN` (token was tampered with)

### Why This Pattern
The middleware separates authentication (who are you?) from authorization (what can you do?).
Route handlers never need to touch JWTs directly — they just read `req.user`. Think of it
like an airport security checkpoint: you show your boarding pass once, then move freely
through the terminal.

### Dependencies
- `config.JWT_SECRET` — loaded from environment variables via `src/config.ts`
- `authorize()` middleware — often chained after this to check roles
- `req.user` — consumed by route handlers and the `authorize()` middleware
```

## Example 2: Hook explanation

**Input**: `Explain the useDebounce hook in src/hooks/useDebounce.ts`

**Output**:

```
## How useDebounce Works

**Overview**: Delays updating a value until a specified period of inactivity, commonly used to avoid firing API calls on every keystroke.

### Flow

1. **Accept input**: Takes a `value` and `delay` in milliseconds
2. **Store debounced value**: Uses `useState` to hold the "settled" value
3. **Set timer on change**: When `value` changes, starts a `setTimeout` for `delay` ms
4. **Reset on rapid changes**: If `value` changes again before the timer fires, the
   `useEffect` cleanup cancels the old timer and starts a new one
5. **Return settled value**: Only updates the returned value once the user stops typing

### Why This Pattern
Without debouncing, a search input making API calls would fire a request for every
character typed: "r", "re", "rea", "reac", "react" = 5 requests. With a 300ms debounce,
it waits until the user pauses, then fires once for "react". Think of it like an elevator
door — it keeps resetting its close timer every time someone walks in.

### Dependencies
- None — this is a standalone utility hook
- Used by: `SearchBar`, `UserFilter`, and `AutocompleteInput` components
```
