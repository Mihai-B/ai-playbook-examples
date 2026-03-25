# Refactor Reference

## Common Refactoring Patterns

### Extract Function
Break a long function into smaller, named functions.
- **When**: Function exceeds ~30 lines or has multiple responsibilities
- **How**: Identify a cohesive block, extract it, name it after what it does

### Replace Conditional with Strategy
Replace long if/else or switch chains with a lookup object.
- **When**: 3+ branches that follow the same pattern
- **How**: Create a `Record<Key, Handler>` map

### Simplify Conditionals
Reduce nesting with early returns and guard clauses.
- **When**: 3+ levels of nesting
- **How**: Invert conditions and return early

### Extract Hook (React)
Move complex component logic into a custom hook.
- **When**: Component has >30 lines of hook/effect logic
- **How**: Move to `useFeatureName()` in the same directory

### Consolidate Duplicates
Merge repeated code into a shared function.
- **When**: Same logic appears 3+ times
- **How**: Extract to a utility, keep it simple

## Change Documentation Format

For each change made:

```
### [Number]. Brief description
- **Before**: What the code looked like
- **After**: What it looks like now
- **Why**: What problem this solves
```

End with:
- Tests updated (if any)
- Confirmation that all existing tests pass
