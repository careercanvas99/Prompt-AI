# PromptAI Gallery

A premium AI Prompt Gallery where visitors discover and copy the exact prompts behind stunning AI-generated images. Admins can manage the gallery via a protected dashboard.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm --filter @workspace/promptai run dev` — run the frontend
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS (glassmorphism dark theme)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM (tables: prompts, admins)
- Auth: JWT (jsonwebtoken) + bcryptjs for admin login
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth)
- `lib/db/src/schema/prompts.ts` — DB schema for prompts + admins tables
- `artifacts/api-server/src/routes/prompts.ts` — Prompt CRUD routes
- `artifacts/api-server/src/routes/admin.ts` — Admin login route
- `artifacts/promptai/src/pages/home.tsx` — Public gallery + modal
- `artifacts/promptai/src/pages/admin/` — Admin login, dashboard, prompt form
- `artifacts/promptai/src/components/layout.tsx` — Shared layout component

## Architecture decisions

- Admin auth uses JWT stored in localStorage (key: `promptai_token`). Token passed as `Authorization: Bearer <token>` header on protected routes.
- Admin credentials (username/password) are stored in the `admins` table with bcrypt-hashed passwords — NOT hardcoded.
- The frontend uses Orval-generated React Query hooks from `@workspace/api-client-react` for all API calls.
- Dark mode is forced by adding `dark` class to `<html>` on mount.
- Gallery data is stored in Replit's built-in PostgreSQL (not Supabase), even though Supabase keys were provided — the built-in DB was already provisioned and is preferred per Replit best practices.

## Product

- **Public gallery** (`/`): Responsive card grid of AI prompts with glassmorphism cards, hover lift effects, staggered fade-in animations. Click any card to open a modal with the full prompt and a one-click copy button.
- **Admin login** (`/admin/login`): Username + password form. Credentials: Hari / Divyabasam.
- **Admin dashboard** (`/admin`): View all prompts with stats (total, categories, last added). Add, edit, delete prompts.
- **Prompt form** (`/admin/prompts/new`, `/admin/prompts/:id/edit`): Form with live image preview.

## User preferences

- Admin credentials: Username = Hari, Password = Divyabasam (stored hashed in DB)
- Premium glassmorphism dark UI with electric purple accent color
- Footer text: © 2026 PromptAI | AI Prompt Gallery

## Gotchas

- After changing the OpenAPI spec, always run `pnpm --filter @workspace/api-spec run codegen` before editing frontend code.
- The `admins` table password is bcrypt-hashed — never store plaintext.
- The `/prompts/stats/summary` route must be registered BEFORE `/prompts/:id` in Express, otherwise Express will try to parse "stats" as an ID.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
