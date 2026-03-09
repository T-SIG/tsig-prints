# tsig-prints

A 3D print e-commerce platform for a college club, built with modern TypeScript tools. This project is designed for students to learn real-world development skills.

## Features

- **TypeScript** - For type safety and improved developer experience
- **Next.js 16** - Full-stack React framework with App Router
- **Hono** - Lightweight, performant API server
- **oRPC** - End-to-end type-safe APIs with OpenAPI integration
- **Bun** - Fast JavaScript runtime and package manager
- **Drizzle ORM** - TypeScript-first database ORM
- **PostgreSQL** - Database engine (via Neon)
- **Better-Auth** - Authentication with email/password and sessions
- **Turborepo** - Optimized monorepo build system
- **shadcn/ui** - Beautiful, accessible UI components
- **Oxlint + Oxfmt** - Linting and formatting

---

## Quick Start

```bash
# 1. Install dependencies
bun install

# 2. Set up environment variables (see below)

# 3. Push database schema
bun run db:push

# 4. Start development servers
bun run dev
```

- **Web App**: http://localhost:3001
- **API Server**: http://localhost:3000

---

## Environment Setup

### Required Variables

Create or update the following files:

**apps/server/.env**

```bash
# Generate a secure secret: run `openssl rand -base64 32`
BETTER_AUTH_SECRET=your-secret-key-here
BETTER_AUTH_URL=http://localhost:3000
CORS_ORIGIN=http://localhost:3001
DATABASE_URL=postgresql://user:password@host/neondb?sslmode=require
```

**apps/web/.env**

```bash
NEXT_PUBLIC_SERVER_URL=http://localhost:3000
```

### Database Options

This project uses Neon PostgreSQL. To set up your own database:

1. Create a free account at [neon.tech](https://neon.tech)
2. Create a new project
3. Copy the connection string to your `.env` file

Or use the provided development database (check `apps/server/.env`).

---

## Available Commands

| Command               | Description                               |
| --------------------- | ----------------------------------------- |
| `bun run dev`         | Start all dev servers (web + server)      |
| `bun run dev:web`     | Start only Next.js web app (port 3001)    |
| `bun run dev:server`  | Start only API server (port 3000)         |
| `bun run build`       | Build all apps for production             |
| `bun run check`       | Run linter (oxlint) and formatter (oxfmt) |
| `bun run check-types` | Check TypeScript types                    |
| `bun run db:push`     | Push schema changes to database           |
| `bun run db:generate` | Generate Drizzle types                    |
| `bun run db:studio`   | Open Drizzle Studio (database GUI)        |
| `bun run prepare`     | Initialize Git hooks (run once)           |

---

## Project Structure

```
tsig-prints/
├── apps/
│   ├── web/              # Next.js frontend (port 3001)
│   │   ├── src/app/      # App Router pages
│   │   └── src/components/  # Page-specific components
│   └── server/           # Hono API server (port 3000)
│       └── src/          # Server source
├── packages/
│   ├── ui/               # Shared shadcn/ui components
│   ├── api/              # ORPC procedures and routers
│   ├── auth/             # Better-Auth configuration
│   ├── db/               # Drizzle schema and queries
│   └── env/              # Environment variable types
```

---

## How to Contribute

### Finding an Issue

Check the [GitHub Issues](https://github.com/T-SIG/tsig-prints/issues) page for tasks. Issues are labeled by difficulty:

- **beginner** / **good-first-issue**: Great for first-time contributors
- **advanced**: More complex features
- **frontend**: UI and Next.js tasks
- **backend**: API and business logic
- **database**: Schema and data tasks
- **admin**: Admin panel features
- **ui**: Design and styling

### Contribution Workflow

1. **Find an issue** on GitHub that interests you

2. **Claim the issue** by commenting on it

3. **Create a branch**

   ```bash
   git checkout -b feature/issue-description
   ```

4. **Make your changes** following the code style:

   ```bash
   bun run check  # Format and lint before committing
   ```

5. **Test your changes**

   ```bash
   bun run dev  # Start servers and test manually
   bun run check-types  # Verify types
   ```

6. **Commit and push**

   ```bash
   git add .
   git commit -m "feat: description of changes"
   git push -u origin feature/issue-description
   ```

7. **Create a Pull Request** on GitHub

---

## Code Style Guidelines

This project uses automated tooling to maintain consistency:

- **oxlint** - Catches code issues (runs on every commit)
- **oxfmt** - Formats code automatically
- **TypeScript** - Strict type checking

Run `bun run check` before committing to catch issues early.

### Import Order

Imports are automatically sorted by category:

1. Node.js built-ins (`node:` prefix)
2. External packages (`react`, `hono`, etc.)
3. Workspace packages (`@tsig-prints/*`)
4. Relative imports (`./*`, `../*`)
5. Absolute imports (`@/` for app-specific paths)

### Naming Conventions

- **Files**: kebab-case (`user-menu.tsx`)
- **Components**: PascalCase (`UserMenu.tsx`)
- **Hooks**: camelCase with `use` prefix (`useAuth`)
- **Constants**: SCREAMING_SNAKE_CASE

---

## Key Technologies

### Authentication (Better-Auth)

Handled automatically - no need to build auth from scratch. Use the client:

```tsx
import { useSession, signIn, signOut } from "better-auth/react";

const { data: session } = useSession();
```

### API (oRPC)

Endpoints are defined in `packages/api/src/routers/`. Use procedures:

```tsx
import { publicProcedure, protectedProcedure } from "@tsig-prints/api";

// Public endpoint
export const appRouter = {
  getProducts: publicProcedure.handler(async () => {
    return db.query.products.findMany();
  }),
};

// Protected endpoint (requires login)
export const appRouter = {
  createOrder: protectedProcedure.handler(async ({ input, context }) => {
    // context.session contains user data
  }),
};
```

### Database (Drizzle)

Schema is in `packages/db/src/schema.ts`:

```ts
import { pgTable, serial, text } from "drizzle-orm/pg-core";

export const products = pgTable("products", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
  price: real("price").notNull(),
});
```

---

## UI Components

Shared components live in `packages/ui/components/`. Import like this:

```tsx
import { Button } from "@tsig-prints/ui/components/button";
import { Card } from "@tsig-prints/ui/components/card";
```

To add new components:

```bash
# Add to shared UI package
npx shadcn@latest add button dialog -c packages/ui

# Or add to web app only
npx shadcn@latest add button dialog -c apps/web
```

---

## Getting Help

- **GitHub Issues**: For bugs and feature requests
- **Codebase**: Explore `packages/api/src/routers/` for API examples
- **Database**: Run `bun run db:studio` to explore data visually

---

## License

MIT
