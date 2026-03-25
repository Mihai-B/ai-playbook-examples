# Document Code Reference

## JSDoc Conventions

### Function Documentation

```ts
/**
 * Brief description of what the function does.
 *
 * @param paramName - Description of the parameter
 * @returns Description of the return value
 * @throws {ErrorType} When this error condition occurs
 *
 * @example
 * const result = myFunction("input");
 * console.log(result); // expected output
 */
```

### Class Documentation

```ts
/**
 * Brief description of the class responsibility.
 *
 * @example
 * const instance = new MyClass(config);
 * await instance.doWork();
 */
```

### What NOT to Document

- Trivial getters/setters (`getName()`, `setEmail()`)
- Self-explanatory one-liners
- TypeScript types that are already descriptive
- Implementation details that may change

## Module README Structure

```markdown
# Module Name

Brief description of what this module is responsible for.

## Key Exports

- `functionA` — What it does and when to use it
- `ClassB` — What it manages

## Dependencies

- `module-x` — Why this dependency exists

## Usage

\`\`\`ts
import { functionA } from "./module";
const result = await functionA(input);
\`\`\`
```
