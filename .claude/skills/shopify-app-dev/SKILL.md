---
name: shopify-app-dev
description: Continue developing Shopify apps and extensions using this React Router v7 template. Covers adding routes, extensions, webhooks, database operations, and following Shopify development best practices.
---

# Shopify App Development

Guidance for developing Shopify apps using React Router v7, Polaris Web Components, and Prisma.

## When to Use

- Adding routes and pages to the app
- Creating UI extensions (Admin, Checkout, POS, Theme, Customer Account)
- Configuring `shopify.app.toml` for scopes or webhooks
- Working with Prisma database and sessions
- Implementing webhooks and GraphQL queries/mutations
- Preparing apps for Shopify App Store submission

## Project Stack

- **Framework**: React Router v7 + TypeScript
- **UI**: Polaris Web Components
- **Database**: Prisma ORM + SQLite
- **Auth**: Shopify App Bridge + OAuth

## Core Tasks

### Add New Route

Create file in `app/routes/` following flat routes convention:

```typescript
// app/routes/app.new-page.tsx
import { json, type LoaderFunctionArgs } from "react-router";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);
  return json({ shop: session.shop });
};

export default function NewPage() {
  return <s-page title="New Page">{/* UI here */}</s-page>;
}
```

**See:** [app_structure.md](references/app_structure.md) for routing patterns

### Add Extension

```bash
shopify app generate extension
# Select: checkout_ui, admin_ui, theme_app, pos_ui, customer_account, or function
```

**See:** [extension_types.md](references/extension_types.md) for all extension types

### Add Webhook

1. Add subscription in `shopify.app.toml`:
```toml
[[webhooks.subscriptions]]
topic = "orders/create"
uri = "/webhooks/orders/create"
```

2. Create handler route:
```typescript
// app/routes/webhooks.orders.create.tsx
export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, topic, payload } = await authenticate.webhook(request);
  // Process webhook...
  return new Response();
};
```

**See:** [code_patterns.md](references/code_patterns.md) for webhook patterns

### Update Database

```bash
# Edit prisma/schema.prisma, then:
npx prisma generate
npx prisma migrate dev --name <description>
```

**See:** [database_patterns.md](references/database_patterns.md) for Prisma operations

### Configure App

Edit `shopify.app.toml` for scopes, webhooks, and app settings.

**See:** [configuration.md](references/configuration.md) for all options

## Development Commands

```bash
npm run dev          # Start dev server
npm run build        # Build for production
npm run typecheck    # Type checking
npm run lint         # Linting
npm run deploy       # Deploy to Shopify
npx prisma studio    # Database GUI
```

## Key Reminders

**CRITICAL:**
- Always use `authenticate.admin()` or `authenticate.webhook()` for all routes
- For extensions, use `shopify.extension.toml` - NEVER `extension.toml`
- Theme App Extensions: schema goes in `{% schema %}` in `.liquid` files, NOT in locale files

**Navigation:**
- Use `<Link>` from `react-router` or `@shopify/polaris`, NOT `<a>` tags
- Use `redirect` from `authenticate.admin`, NOT from `react-router`

## Troubleshooting

### Theme Extension Locale Error

```
Extension must have only one default locale file.
Must have a valid locale file extension format.
```

**Fix:** Move schema to `{% schema %}` in `.liquid` file. Locale files are for translations only.

### Database Errors

```bash
npx prisma generate
npx prisma migrate dev --name init
```

## References

- [app_structure.md](references/app_structure.md) - Directory structure, routing patterns
- [configuration.md](references/configuration.md) - TOML configuration options
- [extension_types.md](references/extension_types.md) - All extension types and targets
- [code_patterns.md](references/code_patterns.md) - Authentication, routes, webhooks
- [graphql_patterns.md](references/graphql_patterns.md) - GraphQL queries and mutations
- [database_patterns.md](references/database_patterns.md) - Prisma database operations
- [extension_examples.md](references/extension_examples.md) - Extension code examples
- [best_practices.md](references/best_practices.md) - Performance, security, App Store

## External Resources

- [React Router docs](https://reactrouter.com/home)
- [Shopify App docs](https://shopify.dev/docs/api/shopify-app-react-router)
- [Polaris Web Components](https://shopify.dev/docs/api/app-home/polaris-web-components)
- [Prisma docs](https://www.prisma.io/docs)
- [GraphQL Admin API](https://shopify.dev/docs/api/admin-graphql)
