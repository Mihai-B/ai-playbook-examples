---
description: "Use when designing REST API endpoints, route handlers, middleware, or HTTP request/response patterns"
applyTo: "src/api/**/*.ts,src/routes/**/*.ts"
---
# API Design Guidelines

## Route Handler Pattern

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

## Conventions

- Use plural nouns for resources: `/api/users`, not `/api/user`
- Nest related resources: `/api/users/:userId/posts`
- Return consistent response shapes: `{ data: T }` for success, `{ error: { code, message } }` for errors
- Use appropriate HTTP status codes: 200 (ok), 201 (created), 400 (bad request), 404 (not found), 500 (server error)
- Validate request bodies with Zod schemas at the route level
- Keep route handlers thin — delegate business logic to service layer
