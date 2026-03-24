---
description: "Use when writing tests, test utilities, mocks, fixtures, or working with Vitest or Playwright"
applyTo: "**/*.test.ts,**/*.test.tsx,**/*.spec.ts"
---
# Testing Guidelines

## Unit Tests (Vitest)

```ts
describe("UserService", () => {
  it("should return user by id", async () => {
    const user = await userService.findById("123");

    expect(user).toEqual({
      id: "123",
      name: "Jane Doe",
      email: "jane@example.com",
    });
  });

  it("should throw NotFoundError for missing user", async () => {
    await expect(userService.findById("nonexistent"))
      .rejects.toThrow(NotFoundError);
  });
});
```

## Rules

- Test behavior, not implementation — assert outcomes, not internal calls
- Use descriptive test names: `should return 404 when user not found`
- One assertion per test when possible
- Prefer test factories over raw object literals for test data
- Mock external services (HTTP, database) but not internal modules
- Place test files next to source: `UserService.ts` → `UserService.test.ts`

## E2E Tests (Playwright)

- Test critical user flows, not every edge case
- Use data-testid attributes for selectors: `[data-testid="submit-button"]`
- Clean up test data after each test run
