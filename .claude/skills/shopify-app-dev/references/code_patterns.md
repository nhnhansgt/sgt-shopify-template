# Shopify App Code Patterns

## Authentication Pattern

### Admin Authentication

```typescript
// app/routes/app._index.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { useLoaderData } from "react-router";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);

  // GraphQL query
  const response = await admin.graphql(`
    query GetProducts($first: Int!) {
      products(first: $first) {
        nodes {
          id
          title
        }
      }
    }
  `, { variables: { first: 25 } });

  const { data } = await response.json();
  return json({ products: data.products.nodes, shop: session.shop });
};

export default function AppIndex() {
  const { products, shop } = useLoaderData<typeof loader>();

  return (
    <s-page>
      <s-header title="Products">
        <s-button slot="actions">Add Product</s-button>
      </s-header>
      <s-card>
        {products.map(product => (
          <s-text-block key={product.id}>{product.title}</s-text-block>
        ))}
      </s-card>
    </s-page>
  );
}
```

## Extension Patterns

### Admin UI Extension

```javascript
// extensions/admin/product-action/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation } from '@shopify/app-extensions-react';
import { Button, Text } from '@shopify/polaris';

export default function Extension() {
  const applyRemoteOperation = useApplyRemoteOperation();
  const [isLoading, setIsLoading] = React.useState(false);

  const handleAction = async () => {
    setIsLoading(true);
    try {
      await applyRemoteOperation('process-order', { orderId: '123' });
    } catch (error) {
      console.error('Error:', error);
    }
    setIsLoading(false);
  };

  return (
    <Button onClick={handleAction} loading={isLoading}>
      Process Order
    </Button>
  );
}
```

### Checkout UI Extension

```javascript
// extensions/checkout-ui/shipping-calculator/src/Calculator.jsx
import React from 'react';
import { useExtensionApi, useApplyAttributeChange } from '@shopify/checkout-ui-extensions-react';

export default function ShippingCalculator() {
  const api = useExtensionApi();
  const applyAttributeChange = useApplyAttributeChange();

  const [rates, setRates] = React.useState([]);

  React.useEffect(() => {
    // Calculate shipping rates
    const calculateRates = async () => {
      // Call your API to get rates
      const response = await fetch('/api/shipping-rates');
      const data = await response.json();
      setRates(data.rates);
    };

    calculateRates();
  }, []);

  return (
    <Banner>
      {rates.map(rate => (
        <BlockStack key={rate.id}>
          <Text>{rate.name}: ${rate.price}</Text>
        </BlockStack>
      ))}
    </Banner>
  );
}
```

### Theme App Extension

```javascript
// extensions/theme/product-rating/src/Rating.jsx
import React from 'react';
import { useMetafield } from '@shopify/app-extensions-react';
import { StarRating, Text } from '@shopify/polaris';

export default function ProductRating() {
  const { metafield, loading } = useMetafield({
    namespace: 'demo',
    key: 'avg_rating',
    type: 'integer'
  });

  if (loading) return <Text>Loading...</Text>;

  return (
    <div>
      <Text>Product Rating</Text>
      <StarRating rating={metafield?.value || 0} />
    </div>
  );
}
```

### POS UI Extension

```javascript
// extensions/pos/quick-discount/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation } from '@shopify/retail-ui-extensions';
import { Button, TextField, Stack } from '@shopify/polaris';

export default function QuickDiscount() {
  const applyRemoteOperation = useApplyRemoteOperation();
  const [discountAmount, setDiscountAmount] = React.useState('10');

  const applyDiscount = async () => {
    await applyRemoteOperation('apply-discount', {
      amount: parseFloat(discountAmount),
      type: 'percentage'
    });
  };

  return (
    <Stack>
      <TextField
        label="Discount (%)"
        value={discountAmount}
        onChange={(value) => setDiscountAmount(value)}
        type="number"
      />
      <Button onClick={applyDiscount}>Apply Discount</Button>
    </Stack>
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
  const { shop, session, topic, payload } = await authenticate.webhook(request);

  // Process order creation
  if (topic === 'orders/create') {
    // Save order data to database
    await prisma.order.create({
      data: {
        orderId: payload.id,
        shop: shop,
        orderNumber: payload.order_number,
        totalPrice: payload.total_price
      }
    });
  }

  return json({ success: true }, { status: 200 });
};
```

## Database Pattern

### Prisma Client Singleton

```typescript
// app/db.server.ts
import { PrismaClient } from "@prisma/client";

let prisma: PrismaClient;

declare global {
  var __db__: PrismaClient;
}

if (process.env.NODE_ENV === "production") {
  prisma = new PrismaClient();
} else {
  if (!global.__db__) {
    global.__db__ = new PrismaClient();
  }
  prisma = global.__db__;
}

export default prisma;
```

### Database Operations

```typescript
// Query
const sessions = await prisma.session.findMany({
  where: { shop: "test.myshopify.com" }
});

// Create
await prisma.session.create({
  data: {
    shop: "test.myshopify.com",
    state: "active",
    accessToken: "shpat_xxxxx"
  }
});

// Update
await prisma.session.update({
  where: { shop: "test.myshopify.com" },
  data: { accessToken: "new_token" }
});

// Delete
await prisma.session.delete({
  where: { shop: "test.myshopify.com" }
});

// Upsert
await prisma.settings.upsert({
  where: { shop: "test.myshopify.com" },
  update: { value: "new_value" },
  create: { shop: "test.myshopify.com", value: "new_value" }
});
```

## GraphQL Query Patterns

### Basic Query

```typescript
const response = await admin.graphql(`
  query GetProducts($first: Int!) {
    products(first: $first) {
      nodes {
        id
        title
        handle
      }
    }
  }
`, { variables: { first: 25 } });

const { data } = await response.json();
```

### Mutation

```typescript
const response = await admin.graphql(`
  mutation CreateProduct($input: ProductInput!) {
    productCreate(input: $input) {
      product {
        id
        title
      }
      userErrors {
        field
        message
      }
    }
  }
`, {
  variables: {
    input: {
      title: "New Product",
      descriptionHtml: "<p>Description</p>"
    }
  }
});
```

### With Fragments

```typescript
const response = await admin.graphql(`
  query GetProduct($id: ID!) {
    product(id: $id) {
      ...ProductFields
    }
  }

  fragment ProductFields on Product {
    id
    title
    handle
    variants(first: 10) {
      nodes {
        id
        price
      }
    }
  }
`, { variables: { id: "gid://shopify/Product/123" } });
```

## API Integration Pattern

### Fetch with Session Token

```typescript
// app/routes/app.proxy.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { session } = await authenticate.admin(request);

  // Get session token for embedded apps
  const sessionToken = request.headers.get("authorization")?.replace("Bearer ", "");

  // Make API call to your backend
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

    // Your logic here
    const data = await fetchData(admin);

    return json(data);
  } catch (error) {
    if (error instanceof AppError) {
      return json({ error: error.message }, { status: error.statusCode });
    }
    return json({ error: "Internal server error" }, { status: 500 });
  }
};
```

## Testing Pattern

### Unit Test Example

```typescript
// __tests__/app.test.ts
import { loader } from "~/routes/app._index";

describe("App Index", () => {
  it("should return products", async () => {
    const request = new Request("http://localhost:3000/app", {
      headers: {
        Authorization: "Bearer test-token"
      }
    });

    const response = await loader({ request, params: {}, context: {} });
    const data = await response.json();

    expect(data.products).toBeDefined();
    expect(Array.isArray(data.products)).toBe(true);
  });
});
```

See the main SKILL.md file for common workflows and development patterns.
