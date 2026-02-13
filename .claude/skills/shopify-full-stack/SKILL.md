---
name: shopify-fullstack
description: Build Shopify apps and extensions (React Router v7, Polaris, Prisma). Covers app routes, UI/theme/checkout extensions, webhooks, GraphQL, database, TOML config, and best practices. Use when creating routes, extensions, fixing TOML errors, or running shopify app dev.
---

# Shopify Full-Stack Development

Build Shopify apps and extensions using React Router v7 template. Covers app development, all extension types, and common pitfalls.

## When to Use This Skill

Use when:

- Creating app routes, loaders, actions with React Router v7
- Building extensions: Theme, Admin UI, Checkout, POS, Customer Account
- Fixing "Invalid extension configuration" TOML errors
- Configuring `shopify.app.toml` scopes, webhooks
- Implementing webhooks with `authenticate.webhook()`
- Running `shopify app dev` fails with extension errors
- Working with Prisma database and sessions
- Querying Shopify Admin GraphQL API

## References

### Core Documentation

- **[references/theme-app-extension.md](references/theme-app-extension.md)** - Theme extension structure, TOML, blocks, snippets, locales, assets, common errors
- **[rules/theme-app-extension.md](rules/theme-app-extension.md)** - Validation checklist before running `shopify app dev`

### Templates

- **[templates/theme-extension/](templates/theme-extension/)** - Working theme extension template with blocks, snippets, locales, assets

### MCP

- Use `shopify-dev-mcp` mcp server for Shopify development
