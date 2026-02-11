# Code Patterns Reference

## Authentication Patterns

### Admin Authentication

```typescript
// app/routes/app._index.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { useLoaderData } from "react-router";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);
  // admin - GraphQL client for Shopify Admin API
  // session - Contains shop, accessToken, etc.
  return json({ shop: session.shop });
};

export default function AppIndex() {
  const { shop } = useLoaderData<typeof loader>();
  return <s-page title="Dashboard"><s-text>{shop}</s-text></s-page>;
}
```

### Webhook Authentication

```typescript
// app/routes/webhooks.orders.create.tsx
import { ActionFunctionArgs, json } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, session, topic, payload } = await authenticate.webhook(request);
  // shop - Shop domain
  // topic - Webhook topic (e.g., "orders/create")
  // payload - Webhook payload data

  console.log(`Webhook ${topic} received from ${shop}`);
  return json({ success: true });
};
```

## Route Patterns

### Loader (Data Fetching)

```typescript
export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);

  const response = await admin.graphql(`
    query { shop { name } }
  `);
  const { data } = await response.json();

  return json({ shopName: data.shop.name });
};
```

### Action (Mutations)

```typescript
export const action = async ({ request }: ActionFunctionArgs) => {
  const { admin } = await authenticate.admin(request);
  const formData = await request.formData();
  const title = formData.get("title") as string;

  const response = await admin.graphql(`
    mutation CreateProduct($input: ProductInput!) {
      productCreate(input: $input) {
        product { id title }
        userErrors { field message }
      }
    }
  `, { variables: { input: { title } } });

  const { data } = await response.json();

  if (data.productCreate.userErrors.length > 0) {
    return json({ errors: data.productCreate.userErrors }, { status: 400 });
  }

  return json({ product: data.productCreate.product });
};
```

### Form Submission

```typescript
import { useSubmit } from "react-router";

export default function ProductForm() {
  const submit = useSubmit();

  const handleSubmit = (event: React.FormEvent) => {
    event.preventDefault();
    const formData = new FormData(event.target as HTMLFormElement);
    submit(formData, { method: "post" });
  };

  return (
    <form onSubmit={handleSubmit}>
      <s-text-field name="title" label="Title" />
      <s-button type="submit">Create</s-button>
    </form>
  );
}
```

## Webhook Handler Pattern

```typescript
// app/routes/webhooks.orders.create.tsx
import { ActionFunctionArgs, json } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";
import prisma from "~/app/db.server";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, topic, payload } = await authenticate.webhook(request);

  // Idempotent processing - check if already processed
  const existing = await prisma.processedWebhook.findUnique({
    where: { webhookId: payload.id }
  });

  if (existing) {
    return json({ status: "already_processed" });
  }

  // Process webhook
  await prisma.order.create({
    data: {
      orderId: payload.id,
      shop: shop,
      orderNumber: payload.order_number,
      totalPrice: payload.total_price
    }
  });

  // Mark as processed
  await prisma.processedWebhook.create({
    data: { webhookId: payload.id }
  });

  return json({ success: true });
};
```

## Error Handling Pattern

```typescript
import { json } from "@react-router/node";

class AppError extends Error {
  constructor(message: string, public statusCode: number = 500) {
    super(message);
    this.name = "AppError";
  }
}

export const loader = async ({ request }: LoaderFunctionArgs) => {
  try {
    const { admin } = await authenticate.admin(request);
    const data = await fetchData(admin);
    return json(data);
  } catch (error) {
    if (error instanceof AppError) {
      return json({ error: error.message }, { status: error.statusCode });
    }
    console.error("Unexpected error:", error);
    return json({ error: "Internal server error" }, { status: 500 });
  }
};
```

## API Integration Pattern

```typescript
// app/routes/app.proxy.tsx
export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { session } = await authenticate.admin(request);
  const sessionToken = request.headers.get("authorization")?.replace("Bearer ", "");

  const response = await fetch("https://your-backend.com/api/data", {
    method: "GET",
    headers: {
      "Authorization": `Bearer ${sessionToken}`,
      "Content-Type": "application/json"
    }
  });

  const data = await response.json();
  return json(data);
};
```

## Testing Pattern

```typescript
// __tests__/app.test.ts
import { loader } from "~/routes/app._index";

describe("App Index", () => {
  it("should return shop data", async () => {
    const request = new Request("http://localhost:3000/app", {
      headers: { Authorization: "Bearer test-token" }
    });

    const response = await loader({ request, params: {}, context: {} });
    const data = await response.json();

    expect(data.shop).toBeDefined();
  });
});
```

**See also:**
- [graphql_patterns.md](graphql_patterns.md) - GraphQL queries and mutations
- [database_patterns.md](database_patterns.md) - Prisma database operations
- [extension_examples.md](extension_examples.md) - Extension code examples
