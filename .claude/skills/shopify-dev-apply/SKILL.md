---
name: shopify-dev-apply
description: Apply Shopify app/extension configurations using MCP tools to query docs. Use when configuring shopify.app.toml, shopify.extension.toml for any extension type (UI/theme/POS/Functions), or need to query Shopify documentation for correct config syntax.
---

# Shopify Dev Apply

## Overview

Apply Shopify app and extension configurations using config templates and shopify-dev-mcp tools to query documentation when needed.

## When to Use

Use this skill when:
- Configuring `shopify.app.toml` (scopes, webhooks, API version)
- Creating `shopify.extension.toml` for any extension type
- Need to verify correct config syntax from Shopify docs
- Setting up extensions: UI, Theme, POS, Functions
- Validating extension configuration structure

## MCP Workflow

Before creating configs, query Shopify docs for current syntax:

```bash
# 1. First: Initialize API context (returns conversationId)
learn_shopify_api(api: "admin")           # For app config
learn_shopify_api(api: "liquid")            # For theme extensions
learn_shopify_api(api: "functions")         # For Functions

# 2. Then: Use conversationId to query docs
search_docs_chunks(prompt: "shopify.extension.toml configuration")
search_docs_chunks(prompt: "checkout ui extension targets")

# 3. Fetch full docs when needed
fetch_full_docs(paths: ["/docs/api/checkout-ui-extensions/latest/configuration"])
```

## Config Templates

Use templates from `assets/config-templates/`:

| Extension Type | Template File | Key Properties |
|----------------|----------------|-----------------|
| App Config | `shopify.app.toml.template` | scopes, webhooks, api_version |
| Checkout UI | `checkout-ui.toml.template` | `ui_extension`, `[[extensions.targeting]]` |
| Admin Block | `admin-block.toml.template` | `ui_extension`, admin target |
| Theme App | `theme-app.toml.template` | `theme_app_extension` - NO targeting in TOML |
| POS UI | `pos-ui.toml.template` | `ui_extension`, POS target |
| Function | `function.toml.template` | `function`, API-specific targets |

## Quick References

- **[mcp-workflow.md](references/mcp-workflow.md)** - MCP tools usage guide
- **[config-guide.md](references/config-guide.md)** - Config patterns and gotchas

## Critical Gotchas

### Theme App Extensions
```toml
# CORRECT - Minimal TOML for theme app extensions
type = "theme_app_extension"
api_version = "unstable"

# WRONG - Do NOT include for theme_app_extension:
# [[extensions.targeting]]  ← NO! Target in block schema
# [extensions.capabilities]  ← NO! Not supported
```

Target goes in `{% schema %}` of block, NOT in TOML.

### UI Extensions (Checkout, Admin, POS, Customer Account)
```toml
# Required for UI extensions
[[extensions.targeting]]
module = "./src/Extension.jsx"
target = "purchase.checkout.block.render"  # varies by type
```

### Always Use
- `shopify.extension.toml` (NOT `extension.toml`)
- `api_version` (NOT `runtime_version` for theme apps)
