# Generate Unit Tests

Generate comprehensive xUnit test cases for the provided code.

## Context

This project uses:
- **xUnit** as the test framework
- **FluentAssertions** for readable assertions
- **NSubstitute** for mocking interfaces

## Task

1. Analyze the code and identify all logical branches and edge cases
2. Look at nearby test files for existing patterns and naming conventions
3. Generate a complete test class covering:
   - Happy path scenarios
   - Edge cases (empty inputs, boundary values, null arguments)
   - Error scenarios (exceptions, validation failures)
   - Async behavior with `CancellationToken` if applicable

## Constraints

- Follow the naming pattern: `MethodName_Scenario_ExpectedResult`
- Use Arrange/Act/Assert structure
- Name the system under test `sut`
- One logical assertion per test
- Use `Substitute.For<T>()` for mocking — never mock concrete classes
- Use test builders or factories for complex object creation
- Mirror the source file path: `src/Application/UserService.cs` → `tests/UnitTests/Application/UserServiceTests.cs`

## Example

Input: a `UserService` class with `GetByIdAsync` and `CreateAsync` methods

Output test names:
```
GetByIdAsync_UserExists_ReturnsUser
GetByIdAsync_UserNotFound_ReturnsNull
CreateAsync_ValidInput_ReturnsCreatedUser
CreateAsync_DuplicateEmail_ThrowsConflictException
CreateAsync_CancellationRequested_ThrowsOperationCancelledException
```

## Code to Test

$ARGUMENTS
