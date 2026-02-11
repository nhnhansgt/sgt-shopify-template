# GraphQL Patterns Reference

## Basic Query

```typescript
const { admin } = await authenticate.admin(request);

const response = await admin.graphql(`
  query GetProducts($first: Int!) {
    products(first: $first) {
      nodes { id title handle }
    }
  }
`, { variables: { first: 25 } });

const { data } = await response.json();
```

## Basic Mutation

```typescript
const response = await admin.graphql(`
  mutation CreateProduct($input: ProductInput!) {
    productCreate(input: $input) {
      product { id title }
      userErrors { field message }
    }
  }
`, {
  variables: {
    input: { title: "New Product", descriptionHtml: "<p>Description</p>" }
  }
});

const { data } = await response.json();

// Always check for userErrors
if (data.productCreate.userErrors.length > 0) {
  console.error("Errors:", data.productCreate.userErrors);
}
```

## Pagination

```typescript
const response = await admin.graphql(`
  query GetProducts($first: Int!, $after: String) {
    products(first: $first, after: $after) {
      pageInfo { hasNextPage endCursor }
      nodes { id title }
    }
  }
`, { variables: { first: 50, after: cursor } });
// Use data.products.pageInfo.endCursor for next page
```

## Query with Filters

```typescript
const response = await admin.graphql(`
  query GetProducts($query: String!) {
    products(first: 25, query: $query) {
      nodes { id title status }
    }
  }
`, { variables: { query: "status:active AND created_at:>2024-01-01" } });
```

## Query with Fragments

```typescript
const response = await admin.graphql(`
  query GetProduct($id: ID!) {
    product(id: $id) { ...ProductFields }
  }

  fragment ProductFields on Product {
    id title handle
    variants(first: 10) { nodes { id price } }
  }
`, { variables: { id: "gid://shopify/Product/123" } });
```

## Update Mutation

```typescript
const response = await admin.graphql(`
  mutation UpdateProduct($input: ProductInput!) {
    productUpdate(input: $input) {
      product { id title }
      userErrors { field message }
    }
  }
`, {
  variables: { input: { id: "gid://shopify/Product/123", title: "Updated" } }
});
```

## Delete Mutation

```typescript
const response = await admin.graphql(`
  mutation DeleteProduct($input: ProductDeleteInput!) {
    productDelete(input: $input) {
      deletedProductId
      userErrors { field message }
    }
  }
`, { variables: { input: { id: "gid://shopify/Product/123" } } });
```

## Common Queries

### Get Shop Info

```typescript
const response = await admin.graphql(`
  query { shop { name email myshopifyDomain plan { displayName } } }
`);
```

### Get Orders

```typescript
const response = await admin.graphql(`
  query GetOrders($first: Int!) {
    orders(first: $first) {
      nodes {
        id name
        totalPriceSet { shopMoney { amount currencyCode } }
        customer { displayName email }
      }
    }
  }
`, { variables: { first: 25 } });
```

### Get Customers

```typescript
const response = await admin.graphql(`
  query GetCustomers($first: Int!) {
    customers(first: $first) {
      nodes { id displayName email ordersCount }
    }
  }
`, { variables: { first: 25 } });
```

## Error Handling

```typescript
const response = await admin.graphql(`...`);
const { data, errors } = await response.json();

// Check for GraphQL errors
if (errors) {
  console.error("GraphQL errors:", errors);
  throw new Error(errors[0].message);
}

// Check for user errors (business logic)
if (data.productCreate?.userErrors?.length > 0) {
  throw new Error(data.productCreate.userErrors.map(e => e.message).join(", "));
}
```

**See also:**
- [code_patterns.md](code_patterns.md) - Authentication, routes
- [database_patterns.md](database_patterns.md) - Prisma operations
