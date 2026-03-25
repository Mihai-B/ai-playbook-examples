# Debugger Reference

## Output Format

Every debugging session should produce this report:

```
## Root Cause
[Clear explanation of what went wrong and why]

## Fix Applied
[Description of the change and which file(s) were modified]

## Verification
[Which tests were run and their results]
```

## Debugging Methodology

### 1. Reproduce
- Read the full stack trace or error message
- Identify the exact input or conditions that trigger the bug
- Check if there's a failing test that demonstrates the issue

### 2. Locate
- Trace from the error back to the source
- Use grep to find related code paths
- Check recent changes with `git log` if the bug is a regression

### 3. Diagnose
- Distinguish symptoms from root causes
- Check for common patterns: off-by-one, null/undefined, async race conditions, type coercion
- Verify assumptions by reading the actual code, not just the types

### 4. Fix
- Apply the minimal change that resolves the issue
- Ensure the fix handles edge cases
- Do not refactor surrounding code

### 5. Verify
- Run the specific failing test
- Run related tests to check for regressions
- If no test exists, write one that reproduces the bug
