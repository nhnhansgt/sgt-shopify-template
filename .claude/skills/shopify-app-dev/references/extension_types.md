# Shopify Extension Types Guide

## Available Extension Types

| Type | Description | Use Cases | Targeting |
|------|-------------|-----------|-----------|
| **Admin UI** | Add cards/modals to admin | Custom admin interfaces, order/product management | Resource pages |
| **Checkout UI** | Add to checkout flow | Custom checkout experiences, upsells | Checkout stages |
| **Theme App** | Add to themes | Dynamic theme elements without editing Liquid | Theme blocks |
| **POS UI** | Point of Sale customization | Payment processing, order modifications | POS screens |
| **Customer Account** | Enhanced customer accounts | Order management, profile customization | Customer account pages |
| **Functions** | Custom backend logic | Cart validation, discounts, fulfillment | Backend processing |
| **Flow** | Connect to Shopify Flow | Triggers, actions, templates | Workflow automation |

## Extension Type Details

### 1. Admin UI Extensions

**Target:** Shopify Admin resource pages

**Use Cases:**
- Custom order management
- Product bulk actions
- Customer insights

**Configuration:**
```toml
type = "ui_extension"

[[extensions.targeting]]
target = "admin.product-list.action-menu-item"
# or: "admin.order-index.action-menu-item"
# or: "admin.customer-index.action-menu-item"
```

### 2. Checkout UI Extensions

**Target:** Checkout flow stages

**Use Cases:**
- Custom shipping calculators
- Product recommendations
- Payment method modifications

**Configuration:**
```toml
type = "ui_extension"

[[extensions.targeting]]
target = "purchase.checkout.shipping-address.render-after"
# or: "purchase.checkout.header.render-before"
# or: "purchase.checkout.payment-authorization.render-before"
```

**Available Targets:**
- `purchase.checkout.header.render-before`
- `purchase.checkout.shipping-address.render-before`
- `purchase.checkout.shipping-address.render-after`
- `purchase.checkout.payment-authorization.render-before`
- `purchase.checkout.delivery-address.render-before`
- `purchase.checkout.delivery-address.render-after`
- `purchase.checkout.reduction.render-after`

### 3. Theme App Extensions

**Target:** Online Store 2.0 themes

**Use Cases:**
- Product badges
- Custom sections
- App embed blocks

**Configuration:**
```toml
type = "theme_extension"
# No targeting - uses block definitions

[[extensions.settings.fields]]
key = "display_text"
name = "Display text"
type = "text"
```

**Block Structure:**
```
extensions/theme/product-banner/
├── shopify.extension.toml
├── src/
│   └── Banner.jsx
└── blocks/
    └── banner.json
```

### 4. POS UI Extensions

**Target:** Point of Sale interface

**Use Cases:**
- Quick discounts
- Custom actions
- Smart grid tiles

**Configuration:**
```toml
type = "pos_ui_extension"

[[extensions.targeting]]
target = "pos.cart.render-after"
# or: "pos.product-detail.render-before"
# or: "pos.smart-grid"
```

### 5. Customer Account Extensions

**Target:** Customer account UI (new)

**Use Cases:**
- Order history enhancements
- Profile customization
- Loyalty programs

**Configuration:**
```toml
type = "customer_account_ui_extension"

[[extensions.targeting]]
target = "customer-account.order-index.render-before"
# or: "customer-account.profile.render-after"
```

### 6. Shopify Functions

**Target:** Backend processing

**Use Cases:**
- Cart validation
- Custom discounts
- Delivery customization
- Fulfillment constraints

**Types:**
- `cart_validation` - Validate cart before checkout
- `cart_transform` - Modify cart contents
- `discount` - Apply custom discounts
- `fulfillment_constraints` - Restrict fulfillment options
- `delivery_customization` - Customize delivery options
- `payment_customization` - Modify payment methods

### 7. Flow Extensions

**Target:** Shopify Flow workflows

**Use Cases:**
- Connect app to Flow
- Trigger workflows from app events
- Provide actions for Flow

**Types:**
- `flow_action` - Action that can be used in Flow
- `flow_trigger` - Trigger that starts Flow workflows
- `flow_template` - Pre-built workflow template

## Extension Selection Guide

**Choose based on your goal:**

| Goal | Best Extension Type |
|------|-------------------|
| Modify admin experience | Admin UI |
| Customize checkout | Checkout UI |
| Add to storefront | Theme App |
| POS enhancements | POS UI |
| Customer account features | Customer Account |
| Backend logic | Functions |
| Workflow automation | Flow |

## Creating Extensions

```bash
# Navigate to app root
cd /path/to/app

# Generate extension with CLI
shopify app generate extension

# Select type when prompted from available options
```

See [code_patterns.md](code_patterns.md) for implementation examples.
