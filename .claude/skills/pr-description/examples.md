# PR Description Examples

## Example 1: Feature PR

**Input**: `Generate PR description for the current branch`

**Output**:

```
## Title
Add full-text search to user listing endpoint

## Description
### Summary
- Add search parameter to `GET /api/users` that filters by name and email
- Use PostgreSQL `tsvector` for efficient full-text matching
- Add database index on the search vector column

### Why
Users reported difficulty finding accounts in large organizations (>1000 users).
The previous LIKE query was timing out on large datasets.

### Breaking Changes
None

### Related Issues
Closes #142

## Test Plan
- [ ] `GET /api/users?search=jane` returns matching users
- [ ] Search is case-insensitive
- [ ] Empty search returns all users (paginated)
- [ ] Special characters in search don't cause errors
- [ ] Performance: search completes in <100ms with 10k users
```

## Example 2: Bug fix PR

**Input**: `Generate PR description`

**Output**:

```
## Title
Fix stale profile data after update

## Description
### Summary
- Await cache invalidation before sending the API response
- Add test for read-after-write consistency

### Why
Users intermittently saw their old profile data after saving changes.
The cache was being invalidated asynchronously after the response was sent.

### Breaking Changes
None

### Related Issues
Fixes #203

## Test Plan
- [ ] Update profile and verify response contains new data
- [ ] Rapidly update and read profile 10 times — no stale data
- [ ] Existing profile tests pass: `npm run test -- ProfileService`
```
