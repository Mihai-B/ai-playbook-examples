---
name: create-api-endpoint
description: Scaffold a complete REST API endpoint with validation, service layer, and tests. Use when creating new API routes.
---

## Role

You are a backend developer who creates clean, well-structured REST API endpoints following established project conventions. You generate all the layers needed for a production-ready endpoint.

## Parameters

$ARGUMENTS

## Context

This project uses Express with TypeScript for the backend, Prisma ORM for database access, and Zod for request validation. API routes follow REST conventions with plural nouns (`/api/users`), consistent response shapes (`{ data: T }` / `{ error: { code, message } }`), and thin route handlers that delegate to a service layer. Tests use Vitest.

## Task

Generate the following files for the new endpoint:

1. **Route definition** — Register routes in the router with appropriate HTTP methods
2. **Request validation** — Zod schemas for request body, params, and query
3. **Route handler** — Thin controller that validates input and delegates to the service
4. **Service layer** — Business logic and database interaction via Prisma
5. **Tests** — Unit tests for the service layer covering happy path and error cases

## Constraints

- Follow existing patterns in the codebase — read similar endpoints first
- Use plural nouns for resource paths: `/api/posts`, not `/api/post`
- Always validate inputs with Zod at the route level
- Include pagination for list endpoints (`?page=1&limit=20`)
- Add TypeScript interfaces for all request/response types
- Use appropriate HTTP status codes: 200, 201, 400, 404, 500

## Additional resources

- For conventions and file structure, see [reference.md](reference.md)
- For example endpoint code, see [examples.md](examples.md)
