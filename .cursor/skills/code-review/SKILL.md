---
name: code-review
description: Review .NET / EF Core code for bugs, security issues, performance problems, and best practice violations. Use when reviewing code, preparing a PR, or auditing specific files.
disable-model-invocation: true
---

# Code Review

Review .NET code against a structured checklist covering bugs, security, performance, and best practices.

## When to Use

- When reviewing code before a pull request
- When auditing existing files for quality issues
- When onboarding to an unfamiliar area of the codebase

## Parameters

- **files** (required) — file paths or code snippets to review
- **focus** (optional) — specific category to prioritize: `bugs`, `security`, `performance`, or `best-practices`. If omitted, review all categories.

## Context

This project uses .NET 8+ with ASP.NET Core Minimal API and Entity Framework Core. Code follows clean architecture with layers at `src/Domain/`, `src/Application/`, `src/Infrastructure/`, and `src/Api/`. Testing uses xUnit + FluentAssertions + NSubstitute.

## Task

1. Read the provided files or code
2. Evaluate against each checklist category:
   - **Bugs** — Logic errors, null reference risks, race conditions, off-by-one errors
   - **Security** — SQL injection, missing input validation, exposed secrets, authorization gaps
   - **Performance** — N+1 queries, missing `AsNoTracking()`, unbounded result sets, unnecessary allocations
   - **Best Practices** — Naming conventions, missing `CancellationToken`, exception handling, SOLID violations, missing XML docs
3. Reference specific line numbers for each finding
4. Suggest concrete fixes with code snippets

## Constraints

- Evaluate against .NET 8+ / ASP.NET Core / EF Core conventions
- Reference specific line numbers when possible
- Suggest concrete fixes, not vague improvements
- Skip categories where no issues are found

## Examples

Input: `/code-review src/Infrastructure/Services/OrderService.cs`

Output issue format:

```
### [Performance] Missing AsNoTracking on read query
**Severity**: Warning
**Location**: OrderService.cs:42
**Issue**: GetByIdAsync fetches an entity for read-only mapping but uses tracking query
**Fix**: Add .AsNoTracking() before .FirstOrDefaultAsync()
```

## Output

For each issue, report: category, severity (Critical / Warning / Suggestion), location, description, and fix.

End with a summary and overall assessment: **Approve** / **Approve with suggestions** / **Request changes**.
