# 📚 TÀI LIỆU NGHIÊN CỨU: SHOPIFY APP & EXTENSION DEVELOPMENT

## 📋 MỤC LỤC

1. [Getting Started - Tạo App Mới](#1-getting-started---tạo-app-mới)
2. [App Structure - Cấu Trúc Project](#2-app-structure---cấu-trúc-project)
3. [App Extensions - Các Loại Extension](#3-app-extensions---các-loại-extension)
4. [Configuration Files - Cấu Hình](#4-configuration-files---cấu-hình)
5. [Best Practices - Thực Hành Tốt Nhất](#5-best-practices---thực-hành-tốt-nhất)
6. [Code Examples - Ví Dụ Mã](#6-code-examples---ví-dụ-mã)
7. [Development Workflow - Quy Trình Phát Triển](#7-development-workflow---quy-trình-phát-triển)

---

## 1. GETTING STARTED - TẠO APP MỚI

### 🎯 Yêu Cầu Đầu Vào

- **Shopify CLI**: Phiên bản mới nhất
- **Developer permissions**: Quyền phát triển app
- **Dev store**: Partner account hoặc merchant account
- **Browser**: Chrome hoặc Firefox mới nhất

### 📝 Step-by-Step Guide

#### Bước 1: Tạo App Mới

```bash
# Điều hướng đến thư mục dự án
cd /path/to/your/projects

# Tạo Shopify app mới
shopify app create

# Khi được hỏi:
# - Nhập tên app (ví dụ: "my-shopify-app")
# - Chọn "Build a React Router app" cho app đầy đủ
# - HOẶC "Build an extension-only app" cho app chỉ có extension
```

#### Bước 2: Khởi động Development Server

```bash
# Điều hướng vào thư mục app
cd my-shopify-app

# Khởi động server
npm run dev
# HOẶC
shopify app dev
```

**CLI sẽ tự động:**
- Hướng dẫn đăng nhập vào developer account
- Tạo app trong Dev Dashboard và kết nối code
- Tạo database Prisma SQLite
- Tạo tunnel giữa máy local và dev store
- Cung cấp URL HTTPS thông qua Cloudflare tunnel

#### Bước 3: Cài App

1. Với server đang chạy, nhấn `p` để mở URL preview
2. Click "Install app" để cài trên dev store
3. Test chức năng app

---

## 2. APP STRUCTURE - CẤU TRÚC PROJECT

### 📁 Cấu Trúc Thư Mục Đầy Đủ

```
my-shopify-app/
├── shopify.app.toml              # Cấu hình app chính (BẮT BUỘC)
├── shopify.app.{config-name}.toml # File cấu hình bổ sung
├── package.json                  # Dependencies và scripts
├── app/                          # Entry points, routes, webhooks
│   ├── entry.server.[jsx|tsx]    # Entry point chính
│   ├── root.[jsx|tsx]           # Root layout cho React Router
│   ├── routes/                  # File-based routing
│   │   ├── app._index.tsx       # Homepage (authenticated)
│   │   ├── app.tsx              # App layout wrapper
│   │   ├── auth.$.tsx           # Auth flow wrapper
│   │   ├── auth.login/          # Login pages
│   │   └── webhooks.*.tsx        # Webhook handlers
│   └── db.server.ts             # Prisma client singleton
├── extensions/                   # App extensions (tùy chọn)
│   ├── {extension-id}/          # Mỗi extension một thư mục
│   │   ├── shopify.extension.toml # Cấu hình extension
│   │   ├── src/
│   │   │   ├── index.ts
│   │   │   └── client.jsx       # UI component
│   │   └── package.json
│   └── ...
├── prisma/                      # Database schema
│   ├── schema.prisma            # Database schema
│   └── dev.sqlite              # SQLite database (development)
├── env                          # Environment variables (UUIDs)
└── shopify.web.toml             # Web configuration (nếu embedded app)
```

### 🔑 Các File Quan Trọng

#### `shopify.app.toml` - Cấu hình chính

```toml
# Metadata cơ bản
name = "My App"
handle = "my-app-handle"
client_id = "abc123"
application_url = "https://example.com"
embedded = true

# API version
api_version = "October25"

# Scopes và permissions
scopes = "write_products,read_customers"

# Webhooks
[[webhooks.subscriptions]]
topic = "app/uninstalled"
uri = "/webhooks/app/uninstalled"

# Extension directories
extension_directories = ["extensions"]
```

#### `app/routes/` - File-based routing

```
app._index.tsx         → /app (authenticated index)
app.additional.tsx     → /app/additional
auth.$.tsx             → /auth/* (auth flow catch-all)
webhooks.orders.create.tsx → /webhooks/orders/create
```

---

## 3. APP EXTENSIONS - CÁC LOẠI EXTENSION

### 🎨 Các Loại Extension Có Sẵn

| Loại Extension | Mô Tả | Use Cases |
|----------------|-------|-----------|
| **Admin UI Extensions** | Add cards/modals vào admin | Custom admin interfaces, order/product management |
| **Checkout UI Extensions** | Add functionality vào checkout | Custom checkout experiences, upsells |
| **Theme App Extensions** | Add elements vào theme | Dynamic theme elements without editing Liquid |
| **POS UI Extensions** | Point of Sale customization | Payment processing, order modifications |
| **Customer Account Extensions** | Enhanced customer accounts | Order management, profile customization |
| **Shopify Functions** | Custom backend logic | Cart validation, discounts, fulfillment |
| **Flow Extensions** | Connect to Shopify Flow | Triggers, actions, lifecycle events, templates |

### 🔧 Tạo Extension Mới

```bash
# Điều hướng vào thư mục app
cd /path/to/your/app

# Generate extension
shopify app generate extension

# Chọn loại extension và cấu hình
```

### 📂 Cấu Trúc Extension

```
extensions/
├── admin/
│   └── [extension-name]/
│       ├── shopify.extension.toml  ✅ CHÍNH XÁC - File config cho extension
│       ├── src/
│       │   ├── index.ts
│       │   └── client.jsx
│       └── package.json
├── pos/
│   └── [extension-name]/
│       ├── shopify.extension.toml  ✅ CHÍNH XÁC
│       ├── src/
│       │   ├── index.ts
│       │   └── client.jsx
│       └── package.json
└── theme/
    └── [extension-name]/
        ├── shopify.extension.toml  ✅ CHÍNH XÁC
        ├── src/
        │   ├── index.ts
        │   └── client.jsx
        ├── block/
        │   └── [block-name].json
        └── package.json
```

### ⚠️ QUAN TRỌNG: Naming Convention

**Chỉ sử dụng `shopify.extension.toml`** - Đây là file configuration chính thức và duy nhất cho mỗi extension.

| File | Status | Ghi chú |
|------|--------|---------|
| `shopify.extension.toml` | ✅ CHÍNH XÁC | File config chính thức cho extensions |
| `extension.toml` | ❌ KHÔNG ĐÚNG | Không phải file chính thức của Shopify |
| `shopify.ui.extension.toml` | ⚠️ ĐÃ CŨ | Legacy (trước July 2023) |
| `shopify.theme.extension.toml` | ⚠️ ĐÃ CŨ | Legacy (trước July 2023) |

### 🆚 Extension-Only vs Full App

| Tính Năng | Extension-Only | Full App (UI + Extensions) |
|-----------|----------------|---------------------------|
| Embedded pages | ❌ | ✅ |
| Web server | ❌ | ✅ |
| Hosting | Shopify servers | Self-hosted |
| Custom app only | ✅ | ❌ |
| All extension types | ✅ | ✅ |

---

## 4. CONFIGURATION FILES - CẤU HÌNH

### 📄 shopify.app.toml Structure

```toml
# === Basic Metadata ===
name = "My App"
handle = "my-app-handle"
client_id = "your_client_id"
application_url = "https://your-app.com"
embedded = true

# === API Version ===
api_version = "October25"

# === Authentication & Scopes ===
scopes = "read_products,write_orders,read_customers"
optional_scopes = ["write_discounts", "read_inventory"]

# === Direct API Configuration ===
direct_api_mode = "online"
embedded_app_direct_api_access = true

# === Redirect URLs ===
redirect_urls = [
  "https://example.com/auth/callback",
  "https://example.com/auth/callback2"
]

# === Webhook Subscriptions ===
[[webhooks.subscriptions]]
topics = ["orders/create", "orders/updated"]
uri = "/webhooks/orders"
filter = "status:cancelled"
include_fields = ["id", "order_number"]

[[webhooks.subscriptions]]
topics = ["app/uninstalled"]
uri = "https://example.com/webhooks/uninstall"

# === App Proxy Configuration ===
[[app_proxy]]
url = "https://example.com/proxy"
prefix = "apps"
subpath = "my-proxy"

# === Development Settings ===
[development]
automatically_update_urls_on_dev = true
dev_store_url = "https://dev-store.myshopify.com"
```

### 📄 shopify.extension.toml Structure

⚠️ **Lưu ý quan trọng:** File `shopify.extension.toml` nằm **trong từng thư mục extension riêng lẻ**, không phải ở root level.

#### Cấu trúc file cho một extension (trong thư mục extension cụ thể):

```toml
# === Basic Extension Configuration ===
#_extensions/checkout-ui/shipping-calculator/shopify.extension.toml

name = "Shipping Calculator"
description = "Calculate shipping rates for orders"
type = "ui_extension"

# === Extension Targeting ===
# Nơi extension sẽ được hiển thị trong Shopify
[[extensions.targeting]]
target = "purchase.checkout.shipping-address.render-after"

# === Capabilities ===
# Quyền và khả năng của extension
[extensions.capabilities]
api_access = true          # Cho phép gọi Shopify API
network_access = false     # Cho phép gọi external APIs
block_progress = false     # Có thể block checkout flow không

# === Settings Configuration ===
# Cài đặt mà merchant có thể cấu hình
[[extensions.settings.fields]]
key = "enabled"
name = "Enable feature"
type = "boolean"
required = true

[[extensions.settings.fields]]
key = "max_weight"
name = "Maximum weight (kg)"
type = "number"
description = "Maximum package weight for calculation"
```

#### Ví dụ thực tế cho các loại extensions khác nhau:

**Checkout UI Extension:**
```toml
# extensions/checkout-ui/custom-offer/shopify.extension.toml
name = "Custom Offer"
description = "Display custom offers at checkout"
type = "ui_extension"

[[extensions.targeting]]
target = "purchase.checkout.header.render-before"

[extensions.capabilities]
api_access = true
network_access = true

# Access to metafields
[[extensions.targeting.metafields]]
namespace = "custom"
key = "offer_settings"
```

**Theme App Extension:**
```toml
# extensions/theme/product-banner/shopify.extension.toml
name = "Product Banner"
description = "Add promotional banners to product pages"
type = "theme_extension"

# Theme extensions don't use targeting
# They use block definitions instead

[[extensions.settings.fields]]
key = "banner_text"
name = "Banner text"
type = "text"
required = true
```

**POS UI Extension:**
```toml
# extensions/pos/quick-discount/shopify.extension.toml
name = "Quick Discount"
description = "Apply quick discounts at POS"
type = "pos_ui_extension"

[[extensions.targeting]]
target = "pos.cart.render-after"

[extensions.capabilities]
api_access = true
```

#### 📍 Ví dụ cấu trúc thư mục đầy đủ:

```
my-shopify-app/
├── shopify.app.toml                    # App configuration
├── extensions/
│   ├── checkout-ui/
│   │   └── shipping-calculator/
│   │       ├── shopify.extension.toml  ✅ Config cho extension này
│   │       ├── src/
│   │       │   └── ShippingCalculator.jsx
│   │       └── package.json
│   ├── theme/
│   │   └── product-banner/
│   │       ├── shopify.extension.toml  ✅ Config cho extension này
│   │       ├── src/
│   │       │   └── ProductBanner.jsx
│   │       ├── blocks/
│   │       │   └── banner.json
│   │       └── package.json
│   └── pos/
│       └── quick-action/
│           ├── shopify.extension.toml  ✅ Config cho extension này
│           ├── src/
│           │   └── QuickAction.jsx
│           └── package.json
```

#### 🔄 Migration từ Legacy Configs (Trước July 2023)

Nếu bạn đang làm với code cũ hoặc thấy references đến các file sau, đây là cách migrate:

| Legacy File (Đã cũ) | Current File (Hiện tại) | Action |
|---------------------|-------------------------|--------|
| `shopify.ui.extension.toml` | `shopify.extension.toml` | Đổi tên file |
| `shopify.theme.extension.toml` | `shopify.extension.toml` | Đổi tên file |
| `extension.toml` | `shopify.extension.toml` | Đổi tên file |

**Cách migrate:**
```bash
# Ví dụ: đổi tên legacy file
cd extensions/checkout-ui/my-extension/
mv shopify.ui.extension.toml shopify.extension.toml

# Hoặc nếu tên cũ khác
mv extension.toml shopify.extension.toml
```

⚠️ **Lưu ý:** Cấu trúc nội dung file có thể cần điều chỉnh sau khi đổi tên. Tham khảo ví dụ ở trên để đảm bảo format đúng.

### 📄 shopify.web.toml Structure

```toml
# === Single Process App ===
commands.dev = "npm run dev"
commands.build = "npm run build"
auth_callback_path = "/auth/callback"
webhooks_path = "/api/webhooks"
port = 3000

# === Multi-Process App ===
# Frontend (at root)
roles = ["frontend"]
commands.dev = "npm run dev:frontend"
commands.build = "npm run build:frontend"
port = 3000

# Backend (in /api directory)
roles = ["backend"]
commands.dev = "npm run dev:backend"
commands.build = "npm run build:backend"
port = 3001

# Background service (in /worker directory)
roles = ["background"]
commands.dev = "npm run worker"
commands.build = "npm run build:worker"
port = 3002
```

### 🌍 Environment-Specific Configs

```toml
# shopify.app.toml (main)
api_version = "October25"

# shopify.app.local.toml (development)
[development]
automatically_update_urls_on_dev = true
dev_store_url = "https://dev-store.myshopify.com"

# shopify.app.staging.toml (staging)
[development]
automatically_update_urls_on_dev = false
dev_store_url = "https://staging-store.myshopify.com"

# shopify.app.production.toml (production)
[development]
automatically_update_urls_on_dev = false
```

---

## 5. BEST PRACTICES - THỰC HÀNH TỐT NHẤT

### ⚡ Performance Best Practices

#### Bundle Size Optimization
- **JavaScript bundle**: Giữ dưới 16KB (ideal)
- **App entry point**: <10KB JS, <50KB CSS
- **Minify code** để giảm kích thước
- **Avoid polyfills** cho browsers với <1% market share

#### Loading Performance
```html
<!-- Sử dụng defer hoặc async -->
<script defer src="main.js"></script>
<script async src="analytics.js"></script>

<!-- Viewport meta tag -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

#### Web Vitals Requirements (Built for Shopify)
- **LCP** (Largest Contentful Paint): ≤2.5 giây
- **CLS** (Cumulative Layout Shift): ≤0.1
- **INP** (Interaction to Next Paint): ≤200ms

### 🏪 App Store Best Practices

#### App Listing Requirements

**App Name:**
- Bắt đầu với brand name
- Tối đa 30 characters
- ❌ Bad: "Announcement Bar - QTeck"
- ✅ Good: "QTeck - Announcement Bar"

**App Icon:**
- 1200x1200px, JPEG/PNG
- Màu sắc đậm, pattern đơn giản

**Screenshots:**
- 3-6 desktop screenshots
- 1600x900px
- Bao gồm mobile/POS nếu applicable

**Content Guidelines:**

*App introduction (100 chars):*
- ✅ Good: "Create print-on-demand custom puzzles. More customization options can help increase product sales."
- ❌ Bad: "Custom puzzles. A creative solution to your print-on-demand needs."

*Features:*
- Giữ dưới 80 chars mỗi feature
- Format dễ đọc

### 🎨 Design Guidelines

#### UI/UX Principles
- **Consistency**: Match Shopify admin look and feel
- **Mobile-first**: Design cho mobile trước
- **Accessibility**: Meet WCAG 2.1 AA contrast requirements
- **Predictability**: Use familiar patterns

#### Polaris Web Components Usage
```jsx
// Navigation
<s-app-nav>
  <s-app-nav-item url="/app/products">Products</s-app-nav-item>
  <s-app-nav-item url="/app/settings">Settings</s-app-nav-item>
</s-app-nav>

// Forms with Contextual Save Bar
<s-card>
  <s-text-field name="product_name" label="Product Name"></s-text-field>
  <s-button-group>
    <s-button submit>Save</s-button>
    <s-button variant="secondary">Cancel</s-button>
  </s-button-group>
</s-card>
```

#### Mobile Responsiveness
- Không có horizontal scrolling trên mobile
- Stack columns trên screens nhỏ hơn
- Expandable content với clear expand mechanisms

### 🔐 Security Best Practices

#### API Usage
- **Use supported APIs only** - deprecated APIs trong 90 days không thể dùng
- **Session token authentication** cho embedded apps
- **Shopify App Bridge** cho secure admin integration
- **No third-party cookies** hoặc local storage trong embedded contexts

#### Data Handling
- **Privacy policy**: Required trong app listing
- **No personal data on-chain** cho blockchain apps
- **Customer data sync**: Required cho marketing apps
- **PII protection**: Remove sensitive information khỏi screenshots

### 📊 Built for Shopify Requirements

#### Quality Thresholds
- **Installs**: Tối thiểu 50 net installs từ active shops trên paid plans
- **Reviews**: Tối thiểu 5 reviews
- **Rating**: Meet minimum recent app rating threshold
- **Partner standing**: No active hoặc outstanding infractions

### 🔔 Webhook Best Practices

#### Delivery Reliability
```javascript
// Build idempotent processing
const processedWebhooks = new Set();

async function handleWebhook(webhook) {
  const webhookId = webhook.id;

  // Skip if already processed
  if (processedWebhooks.has(webhookId)) {
    return { status: 'already_processed' };
  }

  processedWebhooks.add(webhookId);
  // Process webhook...
}
```

#### Debounce Handling
```javascript
// Use fields with unique values
[[webhooks.subscriptions]]
topics = ["products/update"]
uri = "/webhooks/products"
include_fields = ["id", "updated_at"]  // Include unique field
```

---

## 6. CODE EXAMPLES - VÍ DỤ MÃ

### 🚀 Getting Started Example

#### Creating a New App
```bash
# Create app
shopify app create
# Select "Build a React Router app"

# Navigate to app
cd my-shopify-app

# Start development
npm run dev
```

### 🎯 Admin UI Extension Example

```javascript
// extensions/admin/product-action/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation, useTriggerModal } from '@shopify/app-extensions-react';
import { Text, Button, Modal, FormLayout } from '@shopify/polaris';

export default function Extension() {
  const applyRemoteOperation = useApplyRemoteOperation();
  const triggerModal = useTriggerModal();
  const [isLoading, setIsLoading] = React.useState(false);

  const handleAction = async () => {
    setIsLoading(true);
    try {
      await applyRemoteOperation('process-order', { orderId: '123' });
      triggerModal('success', { message: 'Order processed!' });
    } catch (error) {
      triggerModal('error', { message: 'Failed to process order' });
    }
    setIsLoading(false);
  };

  return (
    <FormLayout>
      <Text>Process Order</Text>
      <Button onClick={handleAction} loading={isLoading}>
        Process Order
      </Button>
    </FormLayout>
  );
}
```

### 🛒 POS UI Extension Example

```javascript
// extensions/pos/pos-discount/src/client.jsx
import React from 'react';
import { useApplyRemoteOperation } from '@shopify/retail-ui-extensions';
import { Text, Button, TextField, Stack } from '@shopify/polaris';

export default function Extension() {
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
      <Text>Apply Discount</Text>
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

### 🎨 Theme App Extension Example

```javascript
// extensions/theme/product-rating/src/client.jsx
import React from 'react';
import { useMetafield } from '@shopify/app-extensions-react';
import { Text, StarRating } from '@shopify/polaris';

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

// extensions/theme/product-rating/block/product-rating.json
{
  "name": "Product Rating",
  "settings": [
    {
      "type": "text",
      "name": "title",
      "label": "Title",
      "default": "Product Rating"
    }
  ]
}
```

### 🔐 Authentication Pattern

```typescript
// app/routes/app._index.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { useLoaderData } from "react-router";

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
  return json({ products: data.products.nodes });
};

export default function AppIndex() {
  const { products } = useLoaderData<typeof loader>();

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

### 📡 Webhook Handler Example

```typescript
// app/routes/webhooks.app.uninstalled.tsx
import { ActionFunctionArgs } from "@react-router/node";
import { authenticate } from "~/app/shopify.server";

export const action = async ({ request }: ActionFunctionArgs) => {
  const { shop, session, topic } = await authenticate.webhook(request);

  // Clean up data when app is uninstalled
  await prisma.session.deleteMany({ where: { shop } });
  await prisma.settings.deleteMany({ where: { shop } });

  return new Response(JSON.stringify({ success: true }), {
    status: 200,
    headers: { "Content-Type": "application/json" }
  });
};
```

### 🗄️ Database Operations with Prisma

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

```typescript
// Usage in routes
import prisma from "~/app/db.server";

// Query
const sessions = await prisma.session.findMany();

// Create
await prisma.session.create({
  data: {
    shop: "test-shop.myshopify.com",
    accessToken: "shpat_xxxxx"
  }
});

// Update
await prisma.session.update({
  where: { shop },
  data: { accessToken: "new_token" }
});

// Delete
await prisma.session.delete({ where: { shop } });
```

---

## 7. DEVELOPMENT WORKFLOW - QUY TRÌNH PHÁT TRIỂN

### 🔄 Common Development Commands

```bash
# === Development ===
npm run dev              # Start development server
shopify app dev          # Alternative command

# === Building ===
npm run build            # Build for production
shopify app build        # Alternative command

# === Deployment ===
npm run deploy           # Deploy to production
shopify app deploy       # Alternative command

# === Database ===
npm run prisma studio    # Open database GUI
npx prisma generate      # Generate Prisma client
npx prisma migrate dev   # Create and apply migration
npx prisma migrate reset # Reset database (dev only)

# === Code Generation ===
npm run generate         # Generate routes/extensions
shopify app generate     # Alternative command

# === Type Checking ===
npm run typecheck        # Run TypeScript type check

# === Linting ===
npm run lint             # Run ESLint

# === Environment ===
npm run env              # Manage environment variables
shopify app env          # Alternative command

# === Config ===
npm run config:link      # Link to existing app
shopify app config link  # Alternative command
```

### 🎯 Development Workflow

#### 1. Scaffolding
```bash
shopify app create
cd my-app
npm install
```

#### 2. Database Setup
```bash
npx prisma generate
npx prisma migrate dev --name init
npx prisma studio  # Optional: View data
```

#### 3. Development
```bash
npm run dev
# Make changes with hot reload
```

#### 4. Testing
```bash
# Press 'p' to open preview
# Test in dev store
```

#### 5. Building
```bash
npm run build
npm run typecheck
npm run lint
```

#### 6. Deployment
```bash
npm run deploy
```

### 🔧 Adding Extensions to Existing App

```bash
# Navigate to existing app
cd /path/to/existing/app

# Generate new extension
shopify app generate extension

# Select extension type and configure
# Extension will be added to extensions/ directory

# Install dependencies
npm install

# Restart dev server
npm run dev
```

### 📝 Creating a New Route

```bash
# Generate new route
npm run generate
# Select "Route" and follow prompts

# Or manually create
touch app/routes/app.new-page.tsx
```

```typescript
// app/routes/app.new-page.tsx
import { LoaderFunctionArgs, json } from "@react-router/node";
import { useLoaderData } from "react-router";

export const loader = async ({ request }: LoaderFunctionArgs) => {
  const { admin, session } = await authenticate.admin(request);
  return json({ shop: session.shop });
};

export default function NewPage() {
  const { shop } = useLoaderData<typeof loader>();
  return (
    <s-page>
      <s-header title="New Page">
        <s-button slot="actions">Action</s-button>
      </s-header>
      <s-card>
        <s-text-block>Shop: {shop}</s-text-block>
      </s-card>
    </s-page>
  );
}
```

---

## 📚 NGUỒN TÀI LIỆU CHÍNH THỐNG

### Shopify Dev Docs - Getting Started
- [Scaffold an app](https://shopify.dev/docs/apps/build/scaffold-app)
- [App structure](https://shopify.dev/docs/apps/build/cli-for-apps/app-structure)
- [Build a Shopify app using React Router](https://shopify.dev/docs/apps/build/build)

### Shopify Dev Docs - App Extensions
- [App extensions overview](https://shopify.dev/docs/apps/build/app-extensions)
- [Build an extension-only app](https://shopify.dev/docs/apps/build/app-extensions/build-extension-only-app)
- [Getting started with POS UI extensions](https://shopify.dev/docs/apps/build/pos/getting-started)
- [Build theme app extensions](https://shopify.dev/docs/apps/build/online-store/theme-app-extensions/build)
- [Configure app extensions](https://shopify.dev/docs/apps/build/app-extensions/configure-app-extensions)

### Shopify Dev Docs - Configuration
- [App configuration](https://shopify.dev/docs/apps/build/cli-for-apps/app-configuration)
- [Manage app config files](https://shopify.dev/docs/apps/build/cli-for-apps/manage-app-config-files)

### Shopify Dev Docs - Best Practices
- [Best practices for apps in the Shopify App Store](https://shopify.dev/docs/apps/launch/shopify-app-store/best-practices)
- [General best practices for app performance](https://shopify.dev/docs/apps/build/performance/general-best-practices)
- [App Design Guidelines](https://shopify.dev/docs/apps/design)
- [Best practices for webhooks](https://shopify.dev/docs/apps/build/webhooks/best-practices)
- [Built for Shopify requirements](https://shopify.dev/docs/apps/launch/built-for-shopify/requirements)

---

## 🎯 KEY TAKEAWAYS

1. **Shopify CLI là công cụ chính thức** để tạo và quản lý apps
2. **React Router template** được khuyến nghị cho hầu hết apps
3. **Extension-only apps** cho apps không cần UI
4. **shopify.app.toml** là file cấu hình chính cho app (ở root level)
5. **shopify.extension.toml** là file config chính thức cho MỖI extension (trong thư mục extension riêng)
6. ❌ **KHÔNG bao giờ dùng `extension.toml`** - không phải file chính thức
7. **Polaris Web Components** cho UI/UX nhất quán
8. **Prisma** cho database và session management
9. **App Bridge** cho embedded app integration
10. **Webhooks** cho real-time events
11. **Performance limits**: <10KB JS, <50KB CSS
12. **Built for Shopify** là badge quality quan trọng

### ⚠️ Tóm Tắt Quan Trọng Về Extension Config Files

| Vị trí | File | Mục đích |
|--------|------|----------|
| **Root app** | `shopify.app.toml` | Cấu hình toàn bộ app |
| **Root app** | `shopify.web.toml` | Cấu hình web processes |
| **extensions/type/name/** | `shopify.extension.toml` | Cấu hình extension cụ thể |
| Bất kỳ đâu | `extension.toml` | ❌ KHÔNG ĐÚNG - Không dùng |
