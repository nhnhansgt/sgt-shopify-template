# Best Practices Guide

## Performance Requirements

### Bundle Size Limits

| Resource | Limit | Best Practice |
|----------|-------|---------------|
| JavaScript bundle | <16KB (ideal) | Code splitting, lazy loading |
| App entry point | <10KB JS | Minimize dependencies |
| App entry point | <50KB CSS | Remove unused styles |

### Web Vitals (Built for Shopify)

| Metric | Threshold |
|--------|-----------|
| LCP (Largest Contentful Paint) | ≤2.5s |
| CLS (Cumulative Layout Shift) | ≤0.1 |
| INP (Interaction to Next Paint) | ≤200ms |

### Loading Performance

```html
<!-- Use defer or async -->
<script defer src="main.js"></script>
<script async src="analytics.js"></script>

<!-- Viewport meta tag -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

## Security Best Practices

### API Usage

- ✅ Use only supported APIs
- ✅ Session token authentication for embedded apps
- ✅ Shopify App Bridge for admin integration
- ❌ No third-party cookies in embedded contexts
- ❌ No local storage in embedded contexts

### Data Protection

```typescript
// ❌ BAD - Hardcoded secrets
const apiKey = "shpat_xxxxx";

// ✅ GOOD - Environment variables
const apiKey = process.env.SHOPIFY_API_KEY;
```

### Authentication

```typescript
// Always authenticate first
const { admin, session } = await authenticate.admin(request);

// Use GraphQL through authenticated client
const response = await admin.graphql(`...`);
```

## App Store Best Practices

### App Listing

**App Name:**
- Start with brand name, ≤30 characters
- ❌ Bad: "Announcement Bar - QTeck"
- ✅ Good: "QTeck - Announcement Bar"

**App Icon:**
- 1200x1200px
- Bold colors, simple pattern
- JPEG or PNG

**Screenshots:**
- 3-6 desktop screenshots at 1600x900px
- Include mobile/POS if applicable

### Content Guidelines

**App Introduction (100 chars):**
- ✅ Good: "Create print-on-demand custom puzzles. More customization options can help increase product sales."
- ❌ Bad: "Custom puzzles. A creative solution to your print-on-demand needs."

**Features:**
- Keep under 80 chars each
- Scannable format
- Focus on benefits, not features

### Pricing

- Free trials: 14 days recommended
- Allow plan switching without reinstall
- Enterprise plans: Reference in "Description of additional charges"

## Design Guidelines

### UI/UX Principles

1. **Consistency** - Match Shopify admin look
2. **Mobile-first** - Design for mobile first
3. **Accessibility** - WCAG 2.1 AA compliance
4. **Predictability** - Use familiar patterns

### Polaris Components

```jsx
// Navigation
<s-app-nav>
  <s-app-nav-item url="/app/products">Products</s-app-nav-item>
  <s-app-nav-item url="/app/settings">Settings</s-app-nav-item>
</s-app-nav>

// Cards
<s-card>
  <s-text-field name="field" label="Field"></s-text-field>
</s-card>

// Buttons
<s-button variant="primary">Save</s-button>
<s-button variant="secondary">Cancel</s-button>
```

## Webhook Best Practices

### Idempotent Processing

```javascript
async function handleWebhook(webhook) {
  const webhookId = webhook.id;

  // Check if already processed
  const existing = await db.processedWebhook.findUnique({
    where: { webhookId }
  });

  if (existing) {
    return { status: 'already_processed' };
  }

  // Process and mark as done
  await processWebhook(webhook);
  await db.processedWebhook.create({ data: { webhookId } });
}
```

### Debounce Handling

```toml
# Include unique fields to prevent duplicates
[[webhooks.subscriptions]]
topics = ["products/update"]
uri = "/webhooks/products"
include_fields = ["id", "updated_at"]
```

## Built for Shopify Requirements

### Quality Thresholds

| Metric | Requirement |
|--------|-------------|
| Installs | ≥50 net installs (paid plans) |
| Reviews | ≥5 reviews |
| Rating | Meet minimum threshold |
| Partner standing | No infractions |

### Performance Metrics

| Metric | Requirement |
|--------|-------------|
| Carrier rates | p95 ≤500ms, 99.9% success |
| Lighthouse score | ≤10 point reduction |
| Admin Web Vitals | Meet all targets |

## Common Pitfalls

### Performance Issues

- ❌ Large bundles (>16KB)
- ❌ Parser-blocking scripts
- ❌ Missing viewport meta tag
- ❌ Excessive animations

### Design Mistakes

- ❌ Inconsistent UI (not using Polaris)
- ❌ Poor mobile design
- ❌ Dark patterns (hidden charges)
- ❌ False claims about outcomes

### Functionality Issues

- ❌ Manual integrations (use extensions)
- ❌ External dependencies
- ❌ Poor onboarding
- ❌ Missing error handling

### Security Violations

- ❌ Hardcoded credentials
- ❌ Improper API usage
- ❌ Data leakage in UI
- ❌ Weak authentication

## Development Workflow

```bash
npm run dev          # Start dev server
npm run typecheck    # Type checking
npm run lint         # Linting
npm run build        # Build
npm run deploy       # Deploy
```

**See also:**
- [code_patterns.md](code_patterns.md) - Implementation patterns
- [configuration.md](configuration.md) - Configuration options
