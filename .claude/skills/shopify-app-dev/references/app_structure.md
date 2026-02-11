# Shopify App Structure Reference

## Complete Directory Structure

```
my-shopify-app/
├── shopify.app.toml              # Main app config (REQUIRED)
├── shopify.app.local.toml        # Local environment config
├── package.json                  # Dependencies and scripts
├── app/
│   ├── entry.server.tsx          # Server entry point
│   ├── root.tsx                 # Root layout
│   ├── routes/                  # File-based routing
│   │   ├── app._index.tsx       # /app (authenticated index)
│   │   ├── app.tsx              # /app (layout wrapper)
│   │   ├── auth.$.tsx           # /auth/* (auth flow)
│   │   ├── auth.login/          # Login pages
│   │   └── webhooks.*.tsx       # Webhook handlers
│   └── db.server.ts             # Prisma client singleton
├── extensions/                   # App extensions (optional)
│   └── {extension-type}/
│       └── {extension-name}/
│           ├── shopify.extension.toml  # Extension config
│           ├── src/
│           │   ├── index.ts
│           │   └── client.jsx
│           └── package.json
├── prisma/
│   ├── schema.prisma            # Database schema
│   └── dev.sqlite               # SQLite database (dev)
└── env                          # Environment variables (UUIDs)
```

## Key Files Explained

### shopify.app.toml (REQUIRED)

Main app configuration at root level.

```toml
name = "My App"
handle = "my-app"
client_id = "abc123"
application_url = "https://example.com"
embedded = true
api_version = "October25"
scopes = "write_products,read_customers"

[[webhooks.subscriptions]]
topic = "app/uninstalled"
uri = "/webhooks/app/uninstalled"
```

### app/routes/ - File-Based Routing

Flat routes convention (React Router v7):

```
app._index.tsx              → /app
app.products.tsx            → /app/products
app.products.$id.tsx        → /app/products/:id
auth.$.tsx                  → /auth/*
webhooks.orders.create.tsx → /webhooks/orders/create
```

### extensions/ - Extension Directory

Each extension has its own subdirectory with shopify.extension.toml.

⚠️ **IMPORTANT**: Always use `shopify.extension.toml` (NOT `extension.toml`)

```
extensions/
├── checkout-ui/
│   └── shipping-calculator/
│       └── shopify.extension.toml  ✅ CORRECT
├── theme/
│   └── product-banner/
│       └── shopify.extension.toml  ✅ CORRECT
└── pos/
    └── quick-action/
        └── shopify.extension.toml  ✅ CORRECT
```

### prisma/schema.prisma

Database schema definition.

```prisma
model Session {
  shop        String   @id
  state       String
  accessToken String?
}
```

## Routing Patterns

### Authenticated Routes

```typescript
// app/routes/app._index.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);
  return json({ shop: session.shop });
};
```

### Webhook Handlers

```typescript
// app/routes/webhooks.app.uninstalled.tsx
import { ActionFunctionArgs } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, session, topic } = await authenticate.webhook(request);
  // Handle webhook...
  return new Response(JSON.stringify({ success: true }));
};
```

## Common Patterns

### GraphQL Queries

```typescript
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
  data: { accessToken: "new_token" }
});

// Delete
await prisma.session.delete({ where: { shop } });
```

See main SKILL.md file for common workflows and [configuration.md](configuration.md) for detailed config options.
