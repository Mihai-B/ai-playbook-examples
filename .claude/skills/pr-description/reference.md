# PR Description Reference

## PR Format Template

```
## Title
[Imperative mood, under 70 characters]

## Description
### Summary
[2-5 bullet points of key changes]

### Why
[Motivation and context — what problem does this solve?]

### Breaking Changes
[List any breaking changes, or "None"]

### Related Issues
[Links to issues, or "None"]

## Test Plan
[Checkbox list of verification steps]
```

## Title Conventions

Good:
- `Add full-text search to user listing endpoint`
- `Fix race condition in profile cache invalidation`
- `Refactor payment processing to use strategy pattern`

Bad:
- `Updated files` (too vague)
- `Added user search functionality to the API endpoint` (too long)
- `fix bug` (not descriptive)

## Test Plan Guidelines

- Include steps a reviewer can follow to verify the change
- Cover the happy path and at least one edge case
- Reference specific commands if applicable (`npm run test -- UserService`)
- Include performance expectations if relevant
