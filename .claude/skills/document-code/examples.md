# Document Code Examples

## Example 1: Service class documentation

**Input**: `Document src/services/UserService.ts`

**Output**:

```ts
/**
 * Manages user lifecycle operations including creation, lookup, and deactivation.
 * Enforces email uniqueness and handles password hashing.
 *
 * @example
 * const userService = new UserService(prisma);
 * const user = await userService.create({
 *   email: "jane@example.com",
 *   name: "Jane Doe",
 *   role: "user",
 * });
 */
export class UserService {
  /**
   * Find a user by their unique ID.
   *
   * @param id - The user's CUID identifier
   * @returns The user record, or null if not found
   * @throws {PrismaClientKnownRequestError} If the database connection fails
   */
  async findById(id: string): Promise<User | null> { ... }

  /**
   * Create a new user with a hashed password.
   * Throws if the email is already registered.
   *
   * @param input - User creation fields (email, name, password, role)
   * @returns The created user without the password hash
   * @throws {ConflictError} If a user with this email already exists
   */
  async create(input: CreateUserInput): Promise<SafeUser> { ... }
}
```

## Example 2: Module README

**Input**: `Document the src/middleware/ directory`

**Output**:

```markdown
# Middleware

Request middleware for the Express API. Runs before route handlers to handle
cross-cutting concerns like authentication, authorization, and error handling.

## Key Exports

- `authenticate` — Verifies JWT tokens and attaches `req.user`
- `authorize(...roles)` — Checks that `req.user` has one of the specified roles
- `errorHandler` — Global error handler that converts exceptions to API error responses
- `requestLogger` — Logs request method, path, status, and duration

## Usage

```ts
import { authenticate, authorize } from "./middleware";

router.get("/admin/users", authenticate, authorize("admin"), listUsers);
```
```
