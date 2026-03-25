# Generate Tests Reference

## Test Structure

```ts
import { describe, it, expect, vi, beforeEach } from "vitest";

describe("ModuleName", () => {
  // Setup shared across tests
  let service: MyService;

  beforeEach(() => {
    service = new MyService(mockDependency);
  });

  describe("methodName", () => {
    it("should [expected behavior] when [condition]", async () => {
      // Arrange
      const input = createTestInput();

      // Act
      const result = await service.method(input);

      // Assert
      expect(result).toEqual(expectedOutput);
    });
  });
});
```

## Naming Conventions

Test names follow the pattern: `should [expected behavior] when [condition]`

Good:
- `should return user by id`
- `should throw NotFoundError when user does not exist`
- `should paginate results when limit is specified`

Bad:
- `test user service`
- `works correctly`
- `handles the error case`

## What to Mock

**DO mock**:
- Database calls (Prisma)
- HTTP requests to external services
- File system operations
- Time-sensitive operations (`vi.useFakeTimers()`)

**DO NOT mock**:
- Internal modules and utilities
- Pure functions
- Type validations (Zod)

## Test Data

Prefer test factories over raw object literals:

```ts
function createTestUser(overrides?: Partial<User>): User {
  return {
    id: "test-user-1",
    email: "test@example.com",
    name: "Test User",
    role: "user",
    ...overrides,
  };
}
```
