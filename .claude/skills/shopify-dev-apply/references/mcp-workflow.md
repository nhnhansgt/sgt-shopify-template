# MCP Workflow for Shopify Development

## shopify-dev-mcp Tools

### 1. Initialize API Context (REQUIRED FIRST)

```bash
# Must call BEFORE any other Shopify MCP tools
learn_shopify_api(api: "admin")

# Returns conversationId - SAVE THIS!
# Example: de7b4a77-c2f3-46f2-bc25-8d0db0a559e0
```

### Available APIs

| API | When to Use |
|-----|--------------|
| `admin` | App config, Admin GraphQL queries |
| `liquid` | Theme extensions, Liquid templates |
| `functions` | Discount/validation functions |
| `storefront-graphql` | Storefront data |
| `polaris-app-home` | App home UI |
| `polaris-admin-extensions` | Admin UI extensions |
| `polaris-checkout-extensions` | Checkout UI extensions |
| `pos-ui` | POS UI extensions |
| `hydrogen` | Hydrogen storefronts |

### 2. Use conversationId in ALL Calls

```bash
# ALL subsequent calls require conversationId
search_docs_chunks(conversationId: "de7b4a77...", prompt: "checkout targets")
introspect_graphql_schema(conversationId: "de7b4a77...", query: "product")
```

### 3. Query Documentation

```bash
# Search for specific topics
search_docs_chunks(conversationId: "ID", prompt: "shopify.extension.toml")

# Fetch full documentation pages
fetch_full_docs(conversationId: "ID", paths: [
  "/docs/apps/build/app-extensions/configure-app-extensions"
])
```

### 4. Introspect GraphQL Schema

```bash
# Explore Admin API schema
introspect_graphql_schema(conversationId: "ID", query: "Product")

# Filter by type
introspect_graphql_schema(conversationId: "ID", filter: ["mutations"], query: "productCreate")
```

## Workflow Examples

### Find Checkout Extension Targets

```bash
# 1. Initialize
learn_shopify_api(api: "polaris-checkout-extensions")
# → conversationId: "abc-123"

# 2. Search docs
search_docs_chunks(
  conversationId: "abc-123",
  prompt: "checkout ui extension block render targets"
)

# 3. Fetch full config docs
fetch_full_docs(
  conversationId: "abc-123",
  paths: ["/docs/api/checkout-ui-extensions/latest/configuration"]
)
```

### Find Admin Block Targets

```bash
# 1. Already have context? Use existing conversationId
learn_shopify_api(api: "polaris-admin-extensions", conversationId: "abc-123")

# 2. Search for targets
search_docs_chunks(
  conversationId: "abc-123",
  prompt: "admin product details block render target"
)
```

### Validate GraphQL Query

```bash
# After generating query, validate it
validate_graphql_codeblocks(
  conversationId: "abc-123",
  api: "admin",
  codeblocks: [{ content: "query { products { nodes { title } }" }]
)
```

## Best Practices

1. **Always init first** - Call `learn_shopify_api` before other tools
2. **Reuse conversationId** - Same ID across multiple API contexts
3. **Validate everything** - Use `validate_*` tools before finalizing code
4. **Search then fetch** - Search chunks first, fetch full docs only when needed
