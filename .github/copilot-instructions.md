# TodoList App Development Guidelines

Auto-generated from all feature plans.  Last updated: 2026-01-18

## Active Technologies

- Node.js 20 LTS + TypeScript 5.3+
- Next.js 15 (App Router)
- Jest 29+ + React Testing Library
- SQLite 3 (better-sqlite3)
- Tailwind CSS 3.4+
- Zod (validation)

## Project Structure

```text
src/
  app/              # Next.js App Router pages & API routes
    api/todos/      # REST API endpoints
  components/       # React components
    ui/             # Reusable UI components (Button, Input, etc.)
    todo/           # Todo-specific components
  lib/              # Utilities, DB client, validation
    db/             # SQLite schema & queries
  hooks/            # Custom React hooks
  types/            # TypeScript type definitions
tests/
  unit/             # Jest unit tests
  integration/      # Integration tests
docs/               # Project documentation
public/             # Static assets
```

## Commands

| Command | Description |
|---------|-------------|
| `npm run dev` | Start dev server (http://localhost:3000) |
| `npm run build` | Production build |
| `npm run start` | Start production server |
| `npm test` | Run all tests |
| `npm run test:watch` | Run tests in watch mode |
| `npm run test:coverage` | Generate coverage report |
| `npm run lint` | Run ESLint |
| `npm run type-check` | Run TypeScript compiler check |
| `npm run db: migrate` | Run database migrations |
| `npm run db:seed` | Seed database with sample data |

## Code Style

- **TypeScript**:  Strict mode enabled, prefer explicit types
- **React**:  Functional components with hooks, no class components
- **Imports**: Use absolute imports with `@/` prefix
- **Naming**: camelCase for variables/functions, PascalCase for components
- **File names**: kebab-case for files, PascalCase for React components

## Database Rules

- **ORM禁止**: Direct SQL queries with better-sqlite3
- **Migration required**: Never modify schema without migration
- **Transactions**: Use transactions for multi-step operations
- **Type safety**: All DB queries must have TypeScript interfaces

## Testing Rules

- **Coverage target**: 80% minimum
- **Test file naming**: `*.test.ts` or `*.test.tsx`
- **Mock external dependencies**: Always mock DB and external APIs
- **Arrange-Act-Assert pattern**: Follow AAA pattern consistently

## Recent Changes

- 2026-01-18: Initial setup with Node.js 20 LTS + Next.js 15 + Jest + SQLite
- 2026-01-18: Added TypeScript strict mode configuration
- 2026-01-18: Configured Tailwind CSS with custom theme

<!-- MANUAL ADDITIONS START -->

## Security Rules

- **Input validation**: All user inputs must be validated with Zod
- **SQL injection prevention**: Use parameterized queries only
- **XSS prevention**: Sanitize all user-generated content
- **CORS**:  Restrict API endpoints to same-origin by default

## Performance Guidelines

- **Server Components**: Default to React Server Components
- **Client Components**: Only use `'use client'` when necessary
- **Database**: Create indexes for frequently queried columns
- **Images**: Use Next.js Image component for optimization

<!-- MANUAL ADDITIONS END -->
