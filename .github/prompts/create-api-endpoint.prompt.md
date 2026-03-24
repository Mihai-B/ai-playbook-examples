---
description: "Scaffold a new REST API endpoint with route, handler, service, validation, and tests"
agent: "agent"
argument-hint: "Describe the resource and operations (e.g., 'CRUD for blog posts')"
---
Create a new REST API endpoint based on the description provided. Follow the project's existing patterns.

## Generate These Files

1. **Route definition** — Register routes in the router
2. **Request validation** — Zod schemas for request body/params
3. **Route handler** — Thin controller that delegates to service
4. **Service layer** — Business logic and database interaction
5. **Tests** — Unit tests for the service layer

## Conventions to Follow

- Use plural nouns for resource paths: `/api/posts`
- Response format: `{ data: T }` for success, `{ error: { code, message } }` for errors
- Validate inputs with Zod at the route level
- Use appropriate HTTP methods and status codes
- Include pagination for list endpoints (`?page=1&limit=20`)
- Add TypeScript interfaces for all request/response types
