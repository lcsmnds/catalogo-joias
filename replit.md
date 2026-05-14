# Lumière Joias

Premium digital jewelry catalog with multi-role access: Customer (browse/cart/WhatsApp checkout), Seller (price/description/image editing), Admin (full control + site settings).

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5 (artifacts/api-server, port 8080)
- DB: PostgreSQL + Drizzle ORM (lib/db)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec → lib/api-client-react, lib/api-zod)
- Build: esbuild (CJS bundle)
- Frontend: React + Vite + Tailwind + shadcn/ui + Framer Motion (artifacts/joalheria)
- Routing: wouter

## Where things live

- `lib/api-spec/openapi.yaml` — source of truth for all API contracts
- `lib/db/src/schema/` — DB schema (products, categories, settings, product_images, users, sessions, cart_items)
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/joalheria/src/pages/` — React pages (home, catalog, product-detail, admin/*, seller/*)
- `artifacts/joalheria/src/components/` — shared components (layout, cart-drawer, product-card)
- `artifacts/joalheria/src/contexts/` — auth-context, cart-context

## Architecture decisions

- Auth: in-memory sessions Map in `artifacts/api-server/src/middlewares/auth.ts`, Bearer token in localStorage
- Settings stored as a single row in `store_settings` table — PATCH /settings accepts any subset of fields
- Product images stored in `product_images` table (separate from `products.image` main image)
- Gallery uses Framer Motion `AnimatePresence` with `mode="wait"` for smooth opacity/scale crossfade
- Category CRUD is admin-only; GET /categories is public

## Product

- Homepage: hero section, promo banner, featured products, categories grid — all content driven by settings
- Catalog: filterable product grid by category
- Product detail: multi-image gallery with prev/next arrows and thumbnail strip
- Cart: slide-out drawer with WhatsApp checkout using configurable message template
- Admin settings: 9-tab sidebar panel (Identidade, Homepage, Banner, WhatsApp, Aparência, Categorias, Social, Rodapé, Catálogo)
- Seller dashboard: edit product price, description, image, options

## Credentials

- Admin: `ADMIN` / `#ADM1234!`
- Seller: `VENDAS` / `#VEND1234!`

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Always run `pnpm --filter @workspace/api-spec run codegen` after changing openapi.yaml
- Always run `pnpm --filter @workspace/db run push` after changing DB schema
- Hooks return `{ data: T }` — destructure with `const { data: X } = useGetX(...)`
- Framer Motion `ease` in `Variants` only accepts specific string names (e.g. "easeOut"), not arrays or plain strings
- `react-icons/si` exports: `SiInstagram`, `SiFacebook`, `SiX` (X/Twitter — NOT SiTwitter)

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
