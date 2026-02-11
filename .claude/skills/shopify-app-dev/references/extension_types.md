# Extension Types Guide

## Quick Reference

| Type | Target | Use Cases |
|------|--------|-----------|
| **Admin UI** | Admin resource pages | Order/product management |
| **Checkout UI** | Checkout flow | Custom checkout experiences |
| **Theme App** | Online Store 2.0 themes | Dynamic theme elements |
| **POS UI** | Point of Sale interface | Payment, order modifications |
| **Customer Account** | Customer account UI | Order history, profile |
| **Functions** | Backend processing | Cart validation, discounts |
| **Flow** | Shopify Flow workflows | Triggers, actions, templates |

## Extension Selection

| Goal | Best Extension Type |
|------|-------------------|
| Modify admin experience | Admin UI |
| Customize checkout | Checkout UI |
| Add to storefront | Theme App |
| POS enhancements | POS UI |
| Customer account features | Customer Account |
| Backend logic | Functions |
| Workflow automation | Flow |

## Type Details

### 1. Admin UI Extensions

**Target:** Shopify Admin resource pages

**Configuration:**
```toml
type = "ui_extension"
[[extensions.targeting]]
target = "admin.product-list.action-menu-item"
```

**Common Targets:**
- `admin.product-list.action-menu-item`
- `admin.order-index.action-menu-item`
- `admin.customer-index.action-menu-item`

### 2. Checkout UI Extensions

**Target:** Checkout flow stages

**Configuration:**
```toml
type = "ui_extension"
[[extensions.targeting]]
target = "purchase.checkout.shipping-address.render-after"
```

**Common Targets:**
- `purchase.checkout.header.render-before`
- `purchase.checkout.shipping-address.render-before`
- `purchase.checkout.shipping-address.render-after`
- `purchase.checkout.payment-authorization.render-before`
- `purchase.checkout.delivery-address.render-before`
- `purchase.checkout.delivery-address.render-after`
- `purchase.checkout.reduction.render-after`

### 3. Theme App Extensions

**Target:** Online Store 2.0 themes

**Configuration:**
```toml
type = "theme_app_extension"
name = "My Theme Extension"
runtime_version = "unstable"
api_version = "2025-01"
```

**Directory Structure:**
```
extensions/my-theme-extension/
├── shopify.extension.toml
├── blocks/
│   └── my-block.liquid        # Schema in {% schema %}
├── assets/
│   └── script.js
├── locales/
│   └── en.default.schema.json # Translations only!
└── snippets/
    └── snippet.liquid
```

**CRITICAL: Schema Locale Files**

| File | Purpose |
|------|---------|
| `blocks/*.liquid` | **Schema definition** in `{% schema %}...{% endschema %}` |
| `locales/en.default.schema.json` | **Translation strings** only (optional) |

**Common Error:**
```
Extension must have only one default locale file.
Must have a valid locale file extension format.
```

**Fix:** Move schema definition from locale file to `.liquid` file.

### 4. POS UI Extensions

**Target:** Point of Sale interface

**Configuration:**
```toml
type = "pos_ui_extension"
[[extensions.targeting]]
target = "pos.cart.render-after"
```

**Common Targets:**
- `pos.cart.render-after`
- `pos.product-detail.render-before`
- `pos.smart-grid`

### 5. Customer Account Extensions

**Target:** Customer account UI (new)

**Configuration:**
```toml
type = "customer_account_ui_extension"
[[extensions.targeting]]
target = "customer-account.order-index.render-before"
```

**Common Targets:**
- `customer-account.order-index.render-before`
- `customer-account.profile.render-after`

### 6. Shopify Functions

**Target:** Backend processing

**Types:**
- `cart_validation` - Validate cart before checkout
- `cart_transform` - Modify cart contents
- `discount` - Apply custom discounts
- `fulfillment_constraints` - Restrict fulfillment options
- `delivery_customization` - Customize delivery options
- `payment_customization` - Modify payment methods

### 7. Flow Extensions

**Target:** Shopify Flow workflows

**Types:**
- `flow_action` - Action for Flow workflows
- `flow_trigger` - Trigger for Flow workflows
- `flow_template` - Pre-built workflow template

## Creating Extensions

```bash
cd /path/to/app
shopify app generate extension
# Select type when prompted
```

**See also:**
- [extension_examples.md](extension_examples.md) - Code examples
- [configuration.md](configuration.md) - TOML configuration
