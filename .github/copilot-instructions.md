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
