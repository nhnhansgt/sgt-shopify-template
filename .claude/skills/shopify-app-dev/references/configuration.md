# Shopify Configuration Files Reference

## shopify.app.toml - Main App Configuration

Located at app root level. Contains app-level settings.

### Required Fields

```toml
name = "My App"                     # App display name
handle = "my-app"                   # URL-friendly identifier
client_id = "abc123"               # API key
application_url = "https://..."    # App URL
embedded = true                    # Run in Shopify admin
```

### Common Optional Fields

```toml
# API Version
api_version = "October25"

# Scopes (permissions)
scopes = "read_products,write_orders,read_customers"
optional_scopes = ["write_discounts", "read_inventory"]

# Direct API Access
direct_api_mode = "online"
embedded_app_direct_api_access = true

# Redirect URLs
redirect_urls = [
  "https://example.com/auth/callback"
]

# Webhooks
[[webhooks.subscriptions]]
topics = ["orders/create", "orders/updated"]
uri = "/webhooks/orders"
filter = "status:cancelled"
include_fields = ["id", "order_number"]

# App Proxy (optional)
[[app_proxy]]
url = "https://example.com/proxy"
prefix = "apps"
subpath = "my-proxy"
```

## shopify.extension.toml - Extension Configuration

Located **inside each extension directory**.

⚠️ **CRITICAL**: Use `shopify.extension.toml` ONLY. Never use `extension.toml`.

### Basic Structure

```toml
# === Basic Info ===
name = "Shipping Calculator"
description = "Calculate shipping rates"
type = "ui_extension"

# === Targeting (where extension appears) ===
[[extensions.targeting]]
target = "purchase.checkout.shipping-address.render-after"

# === Capabilities ===
[extensions.capabilities]
api_access = true          # Can call Shopify API
network_access = false     # Can call external APIs
block_progress = false     # Can block checkout

# === Settings (merchant config) ===
[[extensions.settings.fields]]
key = "enabled"
name = "Enable feature"
type = "boolean"
required = true
```

### Extension Types & Examples

**Checkout UI Extension:**
```toml
type = "ui_extension"

[[extensions.targeting]]
target = "purchase.checkout.header.render-before"

[[extensions.targeting.metafields]]
namespace = "custom"
key = "offer_settings"
```

**Theme App Extension:**
```toml
type = "theme_extension"
# No targeting - uses blocks instead

[[extensions.settings.fields]]
key = "banner_text"
name = "Banner text"
type = "text"
required = true
```

**POS UI Extension:**
```toml
type = "pos_ui_extension"

[[extensions.targeting]]
target = "pos.cart.render-after"
```

## shopify.web.toml - Web Process Configuration

Configures web app processes.

### Single Process App

```toml
commands.dev = "npm run dev"
commands.build = "npm run build"
auth_callback_path = "/auth/callback"
webhooks_path = "/api/webhooks"
port = 3000
```

### Multi-Process App

```toml
# Frontend (root)
roles = ["frontend"]
commands.dev = "npm run dev:frontend"
port = 3000

# Backend (/api directory)
roles = ["backend"]
commands.dev = "npm run dev:backend"
port = 3001

# Background worker (/worker directory)
roles = ["background"]
commands.dev = "npm run worker"
port = 3002
```

## Environment-Specific Configs

Override settings per environment:

```toml
# shopify.app.local.toml (development)
[development]
automatically_update_urls_on_dev = true
dev_store_url = "https://dev-store.myshopify.com"

# shopify.app.staging.toml (staging)
[development]
automatically_update_urls_on_dev = false
dev_store_url = "https://staging.myshopify.com"

# shopify.app.production.toml (production)
[development]
automatically_update_urls_on_dev = false
```

## Legacy File Migration

**If you see these old files**, rename them:

| Old Name (DO NOT USE) | New Name (USE THIS) |
|------------------------|---------------------|
| `shopify.ui.extension.toml` | `shopify.extension.toml` |
| `shopify.theme.extension.toml` | `shopify.extension.toml` |
| `extension.toml` | `shopify.extension.toml` |

**Migration:**
```bash
cd extensions/checkout-ui/my-extension
mv shopify.ui.extension.toml shopify.extension.toml
```

⚠️ After renaming, update file structure to match current format.

## Configuration Best Practices

1. **Use environment files** for sensitive data (never hardcode secrets)
2. **Request minimal scopes** - only what you need
3. **Use specific webhook filters** to reduce noise
4. **Keep configs in version control** (except .env files)
5. **Test locally** before deploying with `npm run dev`

See [extension_types.md](extension_types.md) for extension-specific configs.
