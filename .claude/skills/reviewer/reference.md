# Reviewer Reference

## Severity Levels

- **CRITICAL**: Bugs, security vulnerabilities, data loss risks — must be fixed before merge
- **WARNING**: Performance issues, missing error handling, race conditions — should be fixed
- **SUGGESTION**: Naming improvements, minor simplifications, documentation gaps — nice to have

## Output Format

Organize findings by file. For each issue:

```
### [SEVERITY] Brief title
**Location**: file:line
**Issue**: What's wrong and why it matters
**Fix**: How to resolve it
```

End with a summary verdict:
- **Approve**: No issues or only suggestions
- **Request changes**: Has critical or warning-level issues
- **Needs discussion**: Architectural concerns that need team alignment
