# Project Guidelines

## Code Style

- Use TypeScript with strict mode enabled
- Prefer functional components and hooks over class components
- Use named exports over default exports
- Follow the project structure in `src/` — components, hooks, utils, services

## Architecture

- **Frontend**: React + TypeScript with Vite
- **Backend**: Node.js + Express with TypeScript
- **Database**: PostgreSQL with Prisma ORM
- **Testing**: Vitest for unit tests, Playwright for E2E

## Build and Test

```bash
npm install          # Install dependencies
npm run dev          # Start dev server
npm run build        # Production build
npm run test         # Run unit tests
npm run test:e2e     # Run E2E tests
npm run lint         # Lint and format check
```

## Conventions

- API routes follow REST conventions: `GET /api/users`, `POST /api/users`, `GET /api/users/:id`
- Error responses use the format: `{ error: { code: string, message: string } }`
- Environment variables are validated at startup via `src/config.ts`
- Database migrations live in `prisma/migrations/` — never edit generated files
- All components have co-located test files: `Button.tsx` → `Button.test.tsx`

---

## API Design Guidelines

Apply when working in `src/api/` or `src/routes/`.

### Route Handler Pattern

```ts
export async function getUser(req: Request, res: Response) {
  const { id } = req.params;

  const user = await userService.findById(id);
  if (!user) {
    return res.status(404).json({
      error: { code: "USER_NOT_FOUND", message: `User ${id} not found` }
    });
  }

  return res.json({ data: user });
}
```

### API Conventions

- Use plural nouns for resources: `/api/users`, not `/api/user`
- Nest related resources: `/api/users/:userId/posts`
- Return consistent response shapes: `{ data: T }` for success, `{ error: { code, message } }` for errors
- Use appropriate HTTP status codes: 200 (ok), 201 (created), 400 (bad request), 404 (not found), 500 (server error)
- Validate request bodies with Zod schemas at the route level
- Keep route handlers thin — delegate business logic to service layer

---

## Database Guidelines

Apply when working in `prisma/` or `src/services/`.

### Migrations

- Always create reversible migrations
- Test rollback before merging
- Never drop columns in the same release as code removal (two-phase approach)
- Name migrations descriptively: `add-user-email-index`, not `update-schema`

### Prisma Conventions

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@index([email])
}
```

### Query Patterns

- Use Prisma's `select` to fetch only needed fields — avoid fetching entire records
- Use transactions for multi-step operations: `prisma.$transaction([...])`
- Always handle unique constraint violations gracefully
- Paginate list queries — never return unbounded results
- Use `findUnique` for single-record lookups, `findMany` for lists

---

## React Component Guidelines

Apply when working in `src/components/` or `src/hooks/`.

### Component Structure

Follow this order within components:

```tsx
export function UserProfile({ userId }: UserProfileProps) {
  // 1. Hooks
  const { data, isLoading } = useUser(userId);
  const [isEditing, setIsEditing] = useState(false);

  // 2. Derived state
  const fullName = `${data?.firstName} ${data?.lastName}`;

  // 3. Effects
  useEffect(() => { ... }, [userId]);

  // 4. Handlers
  const handleSave = () => { ... };

  // 5. Early returns
  if (isLoading) return <Skeleton />;

  // 6. Render
  return <div>...</div>;
}
```

### Rules

- Prefer `interface` over `type` for component props
- Extract hooks when component logic exceeds ~30 lines
- Use `React.memo()` only when profiling shows a performance need
- Colocate styles: use CSS modules (`Component.module.css`)
- Never use `any` — prefer `unknown` with type guards

---

## Security Guidelines

Apply when working on authentication, authorization, input validation, or secrets.

### Input Validation

- Validate ALL user input at the API boundary using Zod schemas
- Never trust client-side validation alone
- Sanitize strings that will be rendered as HTML to prevent XSS

```ts
const CreateUserSchema = z.object({
  email: z.string().email().max(255),
  name: z.string().min(1).max(100),
  role: z.enum(["user", "admin"]),
});
```

### Authentication

- Use JWT tokens with short expiry (15 min access, 7 day refresh)
- Store refresh tokens server-side, not in localStorage
- Hash passwords with bcrypt (cost factor ≥ 12)
- Rate-limit login endpoints

### Authorization

- Check permissions in middleware, not in route handlers
- Use role-based access control (RBAC) with the `authorize()` middleware
- Never expose internal IDs in error messages

### Secrets

- Never hardcode secrets — use environment variables
- Never log sensitive data (tokens, passwords, PII)
- Use `src/config.ts` for validated environment variable access

---

## Testing Guidelines

Apply when working with test files (`*.test.ts`, `*.test.tsx`, `*.spec.ts`).

### Unit Tests (Vitest)

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

### Rules

- Test behavior, not implementation — assert outcomes, not internal calls
- Use descriptive test names: `should return 404 when user not found`
- One assertion per test when possible
- Prefer test factories over raw object literals for test data
- Mock external services (HTTP, database) but not internal modules
- Place test files next to source: `UserService.ts` → `UserService.test.ts`

### E2E Tests (Playwright)

- Test critical user flows, not every edge case
- Use data-testid attributes for selectors: `[data-testid="submit-button"]`
- Clean up test data after each test run
