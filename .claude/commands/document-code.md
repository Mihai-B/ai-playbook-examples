Generate documentation for the provided code:

## For Functions/Classes

Add JSDoc comments including:
- Brief description of purpose
- `@param` for each parameter with type and description
- `@returns` description of return value
- `@throws` for known error cases
- `@example` with a realistic usage example

## For Modules/Directories

Generate a README.md including:
- Module purpose and responsibility
- Key exports and their usage
- Dependencies and relationships
- Setup or configuration requirements (if any)

## Rules

- Match existing documentation style in the codebase
- Be concise — don't repeat what the types already express
- Focus on _why_ and _how to use_, not _what_ (the code shows what)

## Code to Document

$ARGUMENTS
