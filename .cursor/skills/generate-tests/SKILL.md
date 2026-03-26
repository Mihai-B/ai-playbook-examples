---
name: generate-tests
description: Generate comprehensive xUnit test cases with FluentAssertions and NSubstitute for .NET code. Use when adding test coverage, writing unit tests, or testing service layers.
disable-model-invocation: true
---

# Generate Unit Tests

Generate comprehensive xUnit test cases for the provided .NET code.

## When to Use

- When adding test coverage to existing code
- When creating tests alongside new features
- When verifying edge cases and error paths

## Parameters

- **code** (required) — file paths or code to generate tests for
- **scope** (optional) — `unit` (default) or `integration`. Unit tests mock dependencies; integration tests use real infrastructure.

## Context

This project uses:
- **xUnit** as the test framework
- **FluentAssertions** for readable assertions
- **NSubstitute** for mocking interfaces

Test files mirror the source structure: `src/Application/UserService.cs` → `tests/UnitTests/Application/UserServiceTests.cs`.

## Task

1. Read the provided code and identify the class under test
2. Identify all public methods, logical branches, and edge cases
3. Check nearby test files for existing patterns and naming conventions
4. Generate a complete test class covering:
   - Happy path scenarios
   - Edge cases (empty inputs, boundary values, null arguments)
   - Error scenarios (exceptions, validation failures)
   - Async behavior with `CancellationToken` if applicable
5. Mock all interface dependencies using `Substitute.For<T>()`

## Constraints

- Follow the naming pattern: `MethodName_Scenario_ExpectedResult`
- Use Arrange/Act/Assert structure
- Name the system under test `sut`
- One logical assertion per test
- Use `Substitute.For<T>()` for mocking — never mock concrete classes
- Use test builders or factories for complex object creation
- Mirror the source file path for test file location

## Examples

Input: `/generate-tests src/Infrastructure/Services/UserService.cs`

Given a `UserService` with `GetByIdAsync` and `CreateAsync` methods:

```csharp
public class UserServiceTests
{
    private readonly IUserRepository _repository = Substitute.For<IUserRepository>();
    private readonly UserService _sut;

    public UserServiceTests() => _sut = new UserService(_repository);

    [Fact]
    public async Task GetByIdAsync_UserExists_ReturnsUser()
    {
        // Arrange
        var user = new User { Id = 1, Name = "Jane", Email = "jane@example.com" };
        _repository.GetByIdAsync(1, Arg.Any<CancellationToken>()).Returns(user);

        // Act
        var result = await _sut.GetByIdAsync(1, CancellationToken.None);

        // Assert
        result.Should().NotBeNull();
        result!.Name.Should().Be("Jane");
    }

    [Fact]
    public async Task GetByIdAsync_UserNotFound_ReturnsNull()
    {
        _repository.GetByIdAsync(99, Arg.Any<CancellationToken>()).Returns((User?)null);

        var result = await _sut.GetByIdAsync(99, CancellationToken.None);

        result.Should().BeNull();
    }
}
```

## Output

A complete test class file with:
- All dependencies mocked in the constructor
- Tests for every public method covering happy paths, edge cases, and error scenarios
- Proper file path following the mirroring convention
