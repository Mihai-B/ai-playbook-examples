Generate comprehensive test cases for the provided code:

- Analyze the code and identify all logical branches and edge cases
- Follow existing test patterns in the codebase (look at nearby `.test.ts` files)
- Use descriptive test names with the pattern: `should [expected behavior] when [condition]`
- Include tests for:
  - Happy path scenarios
  - Edge cases (empty inputs, boundary values)
  - Error scenarios (invalid inputs, thrown exceptions)
  - Async behavior if applicable
- Use test factories or builders for complex test data
- Mock external dependencies but not internal modules

## Code to Test

$ARGUMENTS
