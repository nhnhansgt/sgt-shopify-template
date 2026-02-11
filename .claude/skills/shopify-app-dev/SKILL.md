---
name: shopify-app-dev
description: Continue developing Shopify apps and extensions using this React Router v7 template. Covers adding routes, extensions, webhooks, database operations, and following Shopify development best practices.
---

# Shopify App & Extension Development

This skill provides guidance for continuing development on this Shopify app template using React Router v7, Polaris Web Components, and Prisma.

## When to Use This Skill

Use this skill when:
- Adding new routes and pages to the app
- Creating UI extensions (Admin, Checkout, POS, Theme, Customer Account)
- Configuring `shopify.app.toml` for new scopes or webhooks
- Working with Prisma database and handling sessions
- Implementing webhooks and GraphQL queries/mutations
- Following Shopify development best practices and security guidelines
- Preparing apps for Shopify App Store submission

## Project Overview

This is a **pre-configured Shopify App Template** that includes:
- **Framework**: React Router v7 with TypeScript
- **UI**: Polaris Web Components (Shopify's design system)
- **Database**: Prisma ORM with SQLite (configurable)
- **Authentication**: Shopify App Bridge with OAuth flow
- **Project Structure**: Already scaffolded and ready for development

## Core Tasks

### 1. Add New Route

**Create a new route in the app:**

```bash
# Navigate to app routes directory
cd app/routes

# Create a new route file following flat routes convention
# Examples:
# app._index.tsx → /app (authenticated index)
# app.products.tsx → /app/products
# app.orders.tsx → /app/orders
# webhooks.orders.create.tsx → /webhooks/orders/create
```

**Route structure:**
```typescript
// app/routes/app.new-page.tsx
import { json, type LoaderFunctionArgs } from "react-router";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);
  // Your data fetching logic here
  return json({ data: "value" });
};

export default function NewPage() {
  return (
    <s-page title="New Page">
      {/* Your UI here */}
    </s-page>
  );
}
```

**See:** [code_patterns.md](references/code_patterns.md) for implementation examples

### 2. Add Extension

**Generate new extension to existing app:**

```bash
# Navigate to project root
cd /path/to/app

# Generate extension with CLI
shopify app generate extension

# Choose extension type when prompted:
# - checkout_ui (Checkout UI Extension)
# - admin_ui (Admin UI Extension)
# - theme_app (Theme App Extension)
# - pos_ui (POS UI Extension)
# - customer_account (Customer Account UI Extension)
# - function (Shopify Function)
```

**See:** [extension_types.md](references/extension_types.md) for detailed guide

### 3. Update Database Schema

**Modify Prisma schema:**

1. Edit `prisma/schema.prisma` to add/modify models
2. Generate Prisma client:
```bash
npx prisma generate
```

3. Create and apply migration:
```bash
npx prisma migrate dev --name <migration-name>
```

4. Or reset database (development only):
```bash
npx prisma migrate reset --force
```

**See:** [app_structure.md](references/app_structure.md) for database patterns

### 4. Configure App

**Edit `shopify.app.toml` to add scopes or webhooks:**

```toml
name = "My App"
api_version = "October25"

# Add new scopes here
scopes = "read_products,write_products,read_orders,write_orders"

[[webhooks.subscriptions]]
topic = "orders/create"
uri = "/webhooks/orders/create"

[[webhooks.subscriptions]]
topic = "app/uninstalled"
uri = "/webhooks/app/uninstalled"
```

**See:** [configuration.md](references/configuration.md) for detailed options

### 5. Implement Webhook

**Create webhook handler:**

1. Add webhook subscription in `shopify.app.toml`
2. Create route file matching the URI pattern:
```typescript
// app/routes/webhooks.orders.create.tsx
import { authenticate } from "~/app/shopify.server";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, session, topic, payload } = await authenticate.webhook(request);
  
  // Process webhook payload
  console.log(`Webhook ${topic} received from ${shop}`);
  
  return new Response();
};
```

**See:** [code_patterns.md](references/code_patterns.md) for webhook patterns

### 6. Create Extension Component

**Extension component structure:**

```javascript
// extensions/checkout-ui/my-extension/src/client.jsx
import { extend } from '@shopify/checkout-ui-extensions-react';

export default extend((api) => {
  const { settings, i18n } = api;

  return (
    <Banner>
      Configure settings in Shopify admin: {JSON.stringify(settings)}
    </Banner>
  );
});
```

**See:** [extension_types.md](references/extension_types.md) for extension-specific patterns

### 7. Build & Deploy

**Build for production:**
```bash
npm run build
npm run typecheck
npm run lint
```

**Deploy to Shopify:**
```bash
npm run deploy
# Or
shopify app deploy
```

## Common Workflows

### Add New Feature to App

1. Create route: Add file to `app/routes/` following flat routes convention
2. Implement loader/action: Use `authenticate.admin(request)` for auth
3. Build UI: Use Polaris web components (`<s-page>`, `<s-button>`, etc.)
4. Test locally: Run `npm run dev`
5. Type check: Run `npm run typecheck`

### Add Extension to App

1. Generate extension: `shopify app generate extension`
2. Configure: Edit `extensions/*/shopify.extension.toml`
3. Implement component: Create extension UI in `src/` folder
4. Test: Run `npm run dev` (press `p` to preview)
5. Deploy: Run `npm run deploy`

### Add Webhook Handler

1. Update config: Add subscription in `shopify.app.toml`
2. Create handler: Add route file matching webhook URI
3. Authenticate: Use `authenticate.webhook(request)` in action
4. Process data: Handle webhook payload as needed
5. Test: Trigger event in Shopify Admin to verify

### Update Database

1. Modify schema: Edit `prisma/schema.prisma`
2. Generate client: Run `npx prisma generate`
3. Create migration: Run `npx prisma migrate dev --name <description>`
4. Test: Run `npm run dev`

## Essential Patterns

### Authentication

```typescript
// Always authenticate first in loaders/actions
const { admin, session } = await authenticate.admin(request);

// For webhooks
const { shop, session, topic, payload } = await authenticate.webhook(request);
```

### GraphQL Query

```typescript
// Use the admin context for GraphQL queries
const response = await admin.graphql(`
  query GetProducts($first: Int!) {
    products(first: $first) {
      nodes { id title }
    }
  }
`, { variables: { first: 25 } });

const { data } = await response.json();
```

### Database Operations

```typescript
import prisma from "~/app/db.server";

// Query
const sessions = await prisma.session.findMany();

// Create
await prisma.session.create({
  data: { shop: "test.myshopify.com", accessToken: "token" }
});

// Update
await prisma.session.update({
  where: { shop },
  data: { accessToken: "new-token" }
});
```

## Development Commands

```bash
# Start development server
npm run dev

# Type checking
npm run typecheck

# Linting
npm run lint

# GraphQL code generation
npm run graphql-codegen

# Build for production
npm run build

# Deploy to production
npm run deploy

# Database operations
npx prisma studio              # Open database GUI
npx prisma generate             # Generate Prisma client
npx prisma migrate dev          # Create and apply migration
npx prisma migrate reset        # Reset database (dev only)
```

## Project Structure Reference

```
app/
├── routes/              # File-based routing (flat routes)
│   ├── app._index.tsx  # Main app homepage (authenticated)
│   ├── app.tsx         # App layout wrapper
│   ├── auth.$.tsx      # Auth flow wrapper
│   └── webhooks.*.tsx  # Webhook handlers
├── shopify.server.ts   # Shopify app configuration & auth
├── db.server.ts        # Prisma client singleton
├── entry.server.tsx    # Server entry point
├── root.tsx            # Root HTML layout
└── routes.ts           # Route configuration (flatRoutes)

extensions/             # Shopify extensions
prisma/
├── schema.prisma       # Database schema
└── dev.sqlite          # SQLite database (development)

shopify.app.toml        # Shopify app configuration
vite.config.ts          # Vite configuration
```

**See:** [app_structure.md](references/app_structure.md) for detailed structure

## Resources

### References

Detailed documentation for specific topics:

- **[app_structure.md](references/app_structure.md)** - Complete app structure and file organization
- **[configuration.md](references/configuration.md)** - TOML configuration reference
- **[extension_types.md](references/extension_types.md)** - All extension types and when to use each
- **[best_practices.md](references/best_practices.md)** - Performance, security, and App Store guidelines
- **[code_patterns.md](references/code_patterns.md)** - Authentication, GraphQL, webhooks, database patterns

## Best Practices Quick Reference

### Performance

- Bundle size: <16KB JavaScript, <10KB entry point
- Web Vitals: LCP ≤2.5s, CLS ≤0.1, INP ≤200ms
- Use `defer`/`async` for scripts

**See:** [best_practices.md](references/best_practices.md) for details

### Security

- Use `authenticate.admin()` for all authenticated requests
- Never hardcode credentials
- Use session tokens for embedded apps
- Implement proper error handling

**See:** [best_practices.md](references/best_practices.md) for details

### Configuration

- Always use `shopify.extension.toml` for extensions (NEVER `extension.toml`)
- Request minimal scopes needed for your app
- Use webhook filters to reduce noise
- Keep configs in version control

**See:** [configuration.md](references/configuration.md) for details

### App Store

- App name: Start with brand, ≤30 chars
- Icon: 1200x1200px, bold colors
- Screenshots: 3-6 at 1600x900px
- Introduction: Focus on merchant benefits

**See:** [best_practices.md](references/best_practices.md) for details

## Troubleshooting

### Common Issues

**Database errors:**
```bash
# Regenerate Prisma client
npx prisma generate

# Create migration
npx prisma migrate dev --name init
```

**Build errors:**
```bash
# Type check
npm run typecheck

# Lint
npm run lint

# Fix issues and rebuild
npm run build
```

**Webhook testing:**
- Test manually by triggering events in Shopify Admin
- Use app-specific webhooks in TOML (not Admin-created)

## Key Reminders

⚠️ **CRITICAL:** Always use `authenticate.admin()` or `authenticate.webhook()` for all routes

⚠️ **CRITICAL:** For extensions, always use `shopify.extension.toml` - NEVER use `extension.toml`

✅ Use `<Link>` from `react-router` or `@shopify/polaris`, NOT `<a>` tags

✅ Use `redirect` from `authenticate.admin`, NOT from `react-router`

✅ Reference `references/` files for detailed information

✅ Follow [best practices](references/best_practices.md) for App Store approval

## Additional Resources

- **React Router docs**: https://reactrouter.com/home
- **Shopify App docs**: https://shopify.dev/docs/api/shopify-app-react-router
- **Polaris Web Components**: https://shopify.dev/docs/api/app-home/polaris-web-components
- **Prisma docs**: https://www.prisma.io/docs
- **GraphQL Admin API**: https://shopify.dev/docs/api/admin-graphql
