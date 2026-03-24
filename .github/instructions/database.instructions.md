---
description: "Use when writing database queries, migrations, schema changes, Prisma models, or data transformations"
applyTo: "prisma/**,src/services/**/*.ts"
---
# Database Guidelines

## Migrations

- Always create reversible migrations
- Test rollback before merging
- Never drop columns in the same release as code removal (two-phase approach)
- Name migrations descriptively: `add-user-email-index`, not `update-schema`

## Prisma Conventions

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

## Query Patterns

- Use Prisma's `select` to fetch only needed fields — avoid fetching entire records
- Use transactions for multi-step operations: `prisma.$transaction([...])`
- Always handle unique constraint violations gracefully
- Paginate list queries — never return unbounded results
- Use `findUnique` for single-record lookups, `findMany` for lists
