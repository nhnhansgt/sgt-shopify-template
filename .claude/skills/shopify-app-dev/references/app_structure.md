# App Structure Reference

## Directory Structure

```
my-shopify-app/
├── shopify.app.toml              # Main app config (REQUIRED)
├── shopify.app.local.toml        # Local environment config
├── package.json                  # Dependencies & scripts
├── app/
│   ├── entry.server.tsx          # Server entry point
│   ├── root.tsx                  # Root HTML layout
│   ├── routes/                   # File-based routing
│   │   ├── app._index.tsx        # /app (authenticated index)
│   │   ├── app.tsx               # /app (layout wrapper)
│   │   ├── auth.$.tsx            # /auth/* (auth flow)
│   │   ├── auth.login/           # Login pages
│   │   └── webhooks.*.tsx        # Webhook handlers
│   ├── shopify.server.ts         # Shopify auth & config
│   ├── db.server.ts              # Prisma client singleton
│   └── routes.ts                 # Route config (flatRoutes)
├── extensions/                   # App extensions
│   └── {extension-type}/
│       └── {extension-name}/
│           ├── shopify.extension.toml
│           ├── src/
│           └── package.json
├── prisma/
│   ├── schema.prisma             # Database schema
│   └── dev.sqlite                # SQLite database (dev)
└── public/                       # Static assets
```

## Key Files

### shopify.app.toml (REQUIRED)

Main app configuration.

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

### File-Based Routing (app/routes/)

Flat routes convention (React Router v7):

| File | Route |
|------|-------|
| `app._index.tsx` | `/app` |
| `app.products.tsx` | `/app/products` |
| `app.products.$id.tsx` | `/app/products/:id` |
| `auth.$.tsx` | `/auth/*` |
| `webhooks.orders.create.tsx` | `/webhooks/orders/create` |

### Extension Directory

**CRITICAL:** Always use `shopify.extension.toml` (NEVER `extension.toml`)

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

## Route Patterns

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

**See also:**
- [configuration.md](configuration.md) - Detailed config options
- [code_patterns.md](code_patterns.md) - Implementation patterns
