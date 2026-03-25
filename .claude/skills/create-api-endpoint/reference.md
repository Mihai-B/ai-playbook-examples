# Create API Endpoint Reference

## File Structure

For a resource called `comments`:

```
src/
├── api/
│   ├── routes/comments.ts       # Route definitions
│   ├── schemas/comments.ts      # Zod validation schemas
│   └── handlers/comments.ts     # Route handlers
├── services/
│   ├── CommentService.ts        # Business logic
│   └── CommentService.test.ts   # Unit tests
```

## REST Conventions

| Operation | Method | Path | Status |
|-----------|--------|------|--------|
| List | GET | `/api/comments` | 200 |
| Get one | GET | `/api/comments/:id` | 200 / 404 |
| Create | POST | `/api/comments` | 201 |
| Update | PATCH | `/api/comments/:id` | 200 / 404 |
| Delete | DELETE | `/api/comments/:id` | 204 / 404 |

## Response Shapes

**Success**: `{ data: T }`
**Success (list)**: `{ data: T[], meta: { page, limit, total } }`
**Error**: `{ error: { code: string, message: string } }`

## Nested Resources

Nest related resources under their parent:
- `GET /api/posts/:postId/comments`
- `POST /api/posts/:postId/comments`

## Pagination

List endpoints must support pagination:
```ts
const PaginationSchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
});
```
