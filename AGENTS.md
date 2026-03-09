# Agent Guidelines for tsig-prints

## Project Overview

This is a monorepo using Turborepo with Bun as the package manager. It includes:

- **apps/web**: Next.js 16 frontend
- **apps/server**: Hono + ORPC API server
- **packages/ui**: Shared shadcn/ui components
- **packages/api**: ORPC procedures and routers
- **packages/auth**: Better-Auth configuration
- **packages/db**: Drizzle ORM schema

## Build, Lint & Test Commands

### Root Commands (run from project root)

```bash
bun install          # Install dependencies
bun run dev          # Start all dev servers (web:3001, server:3000)
bun run build        # Build all apps
bun run check        # Run oxlint + oxfmt (auto-fix)
bun run check-types  # TypeScript type checking
bun run prepare      # Initialize husky git hooks
```

### App-Specific Commands

```bash
# Web (Next.js on port 3001)
bun run dev:web      # Start web dev server

# Server (Hono on port 3000)
bun run dev:server   # Start server dev server (hot reload)

# Database (Drizzle)
bun run db:push          # Push schema to database
bun run db:generate      # Generate Drizzle types
bun run db:migrate       # Run migrations
bun run db:studio       # Open Drizzle Studio
```

### Running a Single Test

**No tests currently exist in this project.** If tests are added later, they likely use Vitest. To run a single test file:

```bash
bun test <path-to-test-file>
```

## Code Style Guidelines

### General Principles

- Use **Bun** as the runtime (not Node.js)
- Use **TypeScript** for all files (no .js)
- Use **Zod** for runtime validation
- Prefer **WASM/ESM** modules (this is ESM-only project)

### Imports

**Order imports by category** (oxlint enforces this automatically):

1. Node.js built-ins (`node:` prefix)
2. External packages (`react`, `hono`, etc.)
3. Workspace packages (`@tsig-prints/*`)
4. Relative imports (`./*`, `../*`)
5. Absolute imports (`@/` for app-specific paths)

```typescript
// Good
import { useState } from "react";
import { db } from "@tsig-prints/db";
import { auth } from "@tsig-prints/auth";
import { o, protectedProcedure } from "@tsig-prints/api";
import Header from "@/components/header";
import styles from "./app.module.css";

// Bad
import Header from "@/components/header";
import { useState } from "react";
```

### Formatting

- **Use oxfmt** for formatting (runs via `bun run check`)
- 2 spaces for indentation
- Single quotes for strings
- Trailing commas everywhere
- No semicolons
- Print width: 80 characters

```typescript
// Good
export async function createContext({ context }: CreateContextOptions) {
  const session = await auth.api.getSession({
    headers: context.req.raw.headers,
  });
  return { session };
}

// Bad
export async function createContext({ context }: CreateContextOptions) {
  const session = await auth.api.getSession({ headers: context.req.raw.headers });
  return { session };
}
```

### Types

- Always use explicit return types for functions:

```typescript
// Good
export async function createContext({ context }: CreateContextOptions): Promise<Context> {
  // ...
}

// For components
export default function Providers({ children }: { children: React.ReactNode }) {
  // ...
}
```

- Use `type` for unions, intersections, and type aliases
- Use `interface` for object shapes that may be extended

### Naming Conventions

- **Files**: kebab-case (`user-menu.tsx`, `create-context.ts`)
- **Components**: PascalCase (`UserMenu.tsx`, `SignUpForm.tsx`)
- **Hooks**: camelCase with `use` prefix (`useAuth`, `useUser`)
- **Utilities**: PascalCase for factory functions, camelCase otherwise
- **Constants**: SCREAMING_SNAKE_CASE for config values
- **React Components**: Export default, file name matches component name

### Error Handling

- Use **ORPCError** for API errors:

```typescript
import { ORPCError } from "@orpc/server";

throw new ORPCError("UNAUTHORIZED");
```

- Use **try/catch** for async operations in components
- Use **zod** for input validation in API procedures
- Never expose raw error messages to clients

### API Patterns (ORPC)

- Define procedures in `packages/api/src/`
- Use `publicProcedure` for public endpoints
- Use `protectedProcedure` for authenticated endpoints
- Always validate inputs with zod schemas

```typescript
import { z } from "zod";
import { publicProcedure, protectedProcedure } from "../index";

export const appRouter = {
  createPost: protectedProcedure
    .input(z.object({ title: z.string(), content: z.string() }))
    .handler(async ({ input, context }) => {
      // implementation
    }),
};
```

### Database (Drizzle)

- Schema lives in `packages/db/src/schema.ts`
- Use neon serverless driver for PostgreSQL
- Always use parameterized queries (drizzle handles this)

### React/Next.js Guidelines

- Mark client components with `"use client"` directive
- Use Server Components by default (Next.js App Router)
- Use `next-themes` for dark mode
- Use `@tanstack/react-query` for data fetching
- Import shared UI from `@tsig-prints/ui`:

```typescript
import { Button } from "@tsig-prints/ui/components/button";
```

### Git Hooks

- Husky runs `oxlint` and `oxfmt` on pre-commit
- Initialize with: `bun run prepare`

## Available Agent Skills

These are auto-loaded for relevant tasks:

- `better-auth-best-practices` - Authentication config
- `hono` - Server framework
- `neon-postgres` - Database
- `next-best-practices` - Next.js
- `next-cache-components` - Next.js 16 caching
- `shadcn` - UI components
- `turborepo` - Monorepo
- `vercel-react-best-practices` - React performance
- `web-design-guidelines` - UI/UX

## Environment Variables

Required env files:

- `apps/server/.env` - Server configuration
- Database uses `DATABASE_URL` from `@tsig-prints/env`

## Key File Locations

- API routes: `packages/api/src/routers/index.ts`
- Auth config: `packages/auth/src/index.ts`
- DB schema: `packages/db/src/schema.ts`
- Web app: `apps/web/src/app/`
- Shared UI: `packages/ui/src/components/`
