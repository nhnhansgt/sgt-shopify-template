# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Shopify App Template** built with **React Router v7** (formerly Remix). It's a full-stack TypeScript application that integrates with Shopify's Admin API using GraphQL and uses Prisma with SQLite for session storage.

- **Framework**: React Router v7 with TypeScript
- **UI**: Polaris Web Components (Shopify's design system)
- **Database**: Prisma ORM with SQLite (configurable for other databases)
- **API**: Shopify Admin GraphQL API
- **Authentication**: Shopify App Bridge with OAuth flow
- **Testing**: No test framework configured

## Claude Code Skills

This project includes Claude Code skills for enhanced development:

- **shopify-full-stack** (`/.claude/skills/shopify-full-stack/SKILL.md`): Build Shopify apps and extensions with React Router v7, Polaris, Prisma. Use when creating routes, extensions, fixing TOML errors, or running `shopify app dev`.
- **react-best-practices** (`/.claude/skills/react-best-practices/SKILL.md`): React/Next.js performance optimization guidelines from Vercel Engineering.

### Shopify Dev MCP

This project is configured with **Shopify Dev MCP** for enhanced API development:

- `learn_shopify_api` - Load API context (call first, get conversationId)
- `search_docs_chunks` - Search Shopify documentation
- `validate_theme` - Validate Liquid syntax (MANDATORY for .liquid files)
- `validate_graphql_codeblocks` - Validate GraphQL queries
- `introspect_graphql_schema` - Explore GraphQL schema

## Development Commands

### Primary Development Workflow
```bash
# Start local development server (recommended)
npm run dev
# This runs: shopify app dev
# Handles: tunneling, env vars, hot reload, and dev server

# Build for production
npm run build
# This runs: react-router build

# Run production server locally
npm run start
# This runs: react-router-serve ./build/server/index.js

# Database setup (run after schema changes)
npm run setup
# This runs: prisma generate && prisma migrate deploy
```

### Code Quality
```bash
# Type checking
npm run typecheck
# This runs: react-router typegen && tsc --noEmit

# Linting
npm run lint
# This runs: eslint with cache

# GraphQL code generation
npm run graphql-codegen
# Generates TypeScript types from GraphQL queries
```

### Shopify CLI Commands
```bash
# Generate extensions, routes, etc.
npm run generate
shopify app generate

# Deploy to production
npm run deploy
shopify app deploy

# Manage environment variables
npm run env
shopify app env

# Link to existing Shopify app config
npm run config:link
shopify app config link
```

### Prisma Commands
```bash
# Generate Prisma client (after schema changes)
npx prisma generate

# Create and apply migration
npx prisma migrate dev --name <migration-name>

# Open Prisma Studio (database GUI)
npx prisma studio

# Reset database (development only)
npx prisma migrate reset
```

## Architecture

### Directory Structure
```
app/
├── routes/              # File-based routing (flat routes)
│   ├── app._index.tsx  # Main app homepage (authenticated)
│   ├── app.tsx         # App layout wrapper
│   ├── auth.$.tsx      # Auth flow wrapper
│   ├── auth.login/     # Login page
│   └── webhooks.*.tsx  # Webhook handlers
├── shopify.server.ts   # Shopify app configuration & auth
├── db.server.ts        # Prisma client singleton
├── entry.server.tsx    # Server entry point
├── root.tsx            # Root HTML layout
└── routes.ts           # Route configuration (flatRoutes)
extensions/             # Shopify extensions (UI extensions, Functions)
prisma/
├── schema.prisma       # Database schema
└── dev.sqlite          # SQLite database (development)
shopify.app.toml        # Shopify app configuration (scopes, webhooks)
vite.config.ts          # Vite configuration
```

### Key Architectural Patterns

#### 1. **Shopify Authentication Flow**
- **shopify.server.ts**: Central authentication configuration using `@shopify/shopify-app-react-router`
- **Three auth functions**:
  - `authenticate.admin(request)`: For authenticated admin requests, returns `{ admin, session }`
  - `authenticate.webhook(request)`: For webhook handlers, returns `{ shop, session, topic }`
  - `login(request)`: For OAuth login flow
- **Session Storage**: Uses Prisma with SQLite (see `db.server.ts`)

#### 2. **Routing with React Router v7**
- **File-based routing**: Uses `@react-router/fs-routes` with `flatRoutes()`
- **Route naming convention**:
  - `app._index.tsx` → `/app` (authenticated index)
  - `app.additional.tsx` → `/app/additional`
  - `auth.$.tsx` → `/auth/*` (catch-all for auth flow)
  - `webhooks.app.uninstalled.tsx` → `/webhooks/app/uninstalled`
- **Nested layouts**: Files create nested route segments

#### 3. **Shopify App Bridge Integration**
- **Embedded apps**: Run in Shopify Admin iframe
- **App Bridge**: Used for `shopify.toast.show()`, `shopify.intents.invoke()`, etc.
- **Important navigation rules**:
  - Use `<Link>` from `react-router` or `@shopify/polaris`, NOT `<a>` tags
  - Use `redirect` from `authenticate.admin`, NOT from `react-router`
  - Use `useSubmit` from `react-router` for form submissions

#### 4. **GraphQL API Usage**
```typescript
// In loaders/actions
const { admin } = await authenticate.admin(request);
const response = await admin.graphql(`
  query { products(first: 25) { nodes { title } } }
`);
const { data } = await response.json();
```
- **GraphQL code generation**: Run `npm run graphql-codegen` to generate TypeScript types
- **API Version**: Configured in `shopify.server.ts` (currently `October25`)
- **Schema**: Auto-completion via `.graphqlrc.ts` for Admin API

#### 5. **Webhook Handling**
- **Config**: Register webhooks in `shopify.app.toml`
- **Handlers**: Create routes matching webhook paths (e.g., `webhooks.app.uninstalled.tsx`)
- **Authentication**: Use `authenticate.webhook(request)` to verify HMAC
- **Example**: See `app/routes/webhooks.app.uninstalled.tsx` for uninstall handling

#### 6. **Database with Prisma**
- **Schema**: Define models in `prisma/schema.prisma`
- **Session model**: Pre-configured for Shopify session storage
- **Client**: Import from `app/db.server.ts` (singleton pattern)
- **Migrations**: Run `prisma migrate dev` after schema changes

### Important Configurations

#### **shopify.app.toml**
- `scopes`: Comma-separated API permissions
- `api_version`: GraphQL API version
- `[[webhooks.subscriptions]]`: Webhook registrations
- Supports webhook filters (e.g., price-based filtering)

#### **Environment Variables**
- `SHOPIFY_API_KEY`: App API key
- `SHOPIFY_API_SECRET`: App secret
- `SHOPIFY_APP_URL`: App URL (set by CLI in dev)
- `SCOPES`: API scopes (overrides toml)
- `SHOP_CUSTOM_DOMAIN`: Custom shop domain (optional)
- `NODE_ENV`: Set to `production` for production builds

#### **vite.config.ts**
- **HMR configuration**: Different for localhost vs tunnel
- **Port**: 3000 (or `PORT` env var)
- **CORS**: Enabled for Shopify tunneling

### TypeScript Configuration
- **Strict mode**: Enabled
- **Path aliases**: Not configured (uses relative imports)
- **React Router types**: Auto-generated in `.react-router/types`
- **GraphQL types**: Generated in `app/types` via codegen

### ESLint Configuration
- **React rules**: `eslint-plugin-react` with JSX runtime
- **TypeScript rules**: `@typescript-eslint/recommended`
- **Import rules**: `eslint-plugin-import` with TypeScript resolver
- **Special**: `shopify` global available (from Shopify integration)

## Common Patterns

### Creating a New Route
1. Create file in `app/routes/` following flat routes convention
2. Export `loader` for data fetching, `action` for mutations
3. Use `authenticate.admin(request)` in loaders/actions
4. Use `<s-page>`, `<s-button>`, etc. from Polaris Web Components

### Adding a Webhook
1. Add subscription in `shopify.app.toml`
2. Create route file matching the URI (e.g., `webhooks.products.update.tsx`)
3. Use `authenticate.webhook(request)` to verify
4. Process webhook payload and return `new Response()`

### GraphQL Query Best Practices
- Use `#graphql` template literal for syntax highlighting
- Run `npm run graphql-codegen` after adding queries
- Import generated types from `app/types`
- Use `admin.graphql()` with typed variables

### Database Operations
```typescript
import prisma from "~/app/db.server";

// Query
const sessions = await prisma.session.findMany();

// Create
await prisma.session.create({ data: { /* ... */ } });

// Update
await prisma.session.update({ where: { shop }, data: { /* ... */ } });

// Delete
await prisma.session.delete({ where: { shop } });
```

## Troubleshooting

### Database Issues
- **Table doesn't exist**: Run `npm run setup`
- **Schema changes**: Run `npx prisma migrate dev`
- **View data**: Run `npx prisma studio`

### Navigation Issues in Embedded App
- Use `Link` from `react-router`, not `<a>`
- Use `redirect` from `authenticate.admin`, not `react-router`
- Check App Bridge is initialized

### Webhook Testing
- **CLI webhooks**: Admin object will be `undefined` (expected)
- **Test manually**: Trigger events in Shopify Admin
- **HMAC errors**: Use app-specific webhooks in TOML, not Admin-created

### Type Errors
- Run `npm run typecheck` to see all errors
- Run `npm run graphql-codegen` after GraphQL changes
- Check `.react-router/types` for route types

### Tunneling Issues
- Default: Cloudflare tunnel (doesn't support streaming)
- For streaming: Use localhost-based development
- Port conflicts: Check `PORT` and `FRONTEND_PORT` env vars

## Additional Resources

- **React Router docs**: https://reactrouter.com/home
- **Shopify App docs**: https://shopify.dev/docs/api/shopify-app-react-router
- **Polaris Web Components**: https://shopify.dev/docs/api/app-home/polaris-web-components
- **Prisma docs**: https://www.prisma.io/docs
- **GraphQL Admin API**: https://shopify.dev/docs/api/admin-graphql
