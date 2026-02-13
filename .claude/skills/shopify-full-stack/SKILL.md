---
name: shopify-fullstack
description: Build Shopify apps and extensions (React Router v7, Polaris, Prisma). Covers app routes, UI/theme/checkout extensions, webhooks, GraphQL, database, TOML config, and best practices. Use when creating routes, extensions, fixing TOML errors, or running shopify app dev.
---

# Shopify Full-Stack Development

Build Shopify apps and extensions using React Router v7 template.

## When to Use

- Creating app routes, loaders, actions with React Router v7
- Building extensions: Theme, Admin UI, Checkout, POS, Customer Account
- Fixing TOML or extension configuration errors
- Configuring `shopify.app.toml` scopes, webhooks
- Implementing webhooks with `authenticate.webhook()`
- Working with Prisma database, sessions, or GraphQL API

## Skill Files

| File                                                                   | Purpose                                                      |
| ---------------------------------------------------------------------- | ------------------------------------------------------------ |
| [references/theme-app-extension.md](references/theme-app-extension.md) | Theme extension structure, schema, settings, assets, locales |
| [rules/theme-app-extension.md](rules/theme-app-extension.md)           | Validation rules, DO/DON'T checklist, common errors          |
| [templates/theme-extension/](templates/theme-extension/)               | Working template - always copy, never create from scratch    |

## MCP Tools

Use `shopify-dev-mcp` for:

- `learn_shopify_api` - Load API context (call first, get conversationId)
- `search_docs_chunks` - Search Shopify documentation
- `validate_theme` - Validate Liquid syntax (MANDATORY for .liquid files)
- `validate_graphql_codeblocks` - Validate GraphQL queries
- `introspect_graphql_schema` - Explore GraphQL schema

## Troubleshooting

| Problem                         | Solution                                                                                |
| ------------------------------- | --------------------------------------------------------------------------------------- |
| Invalid extension configuration | Check TOML matches template, call MCP for current syntax, run `shopify extension check` |
| Extension not in theme editor   | Verify `target` in schema, `type = "theme"` in TOML, restart `shopify app dev`          |
| Liquid syntax errors            | Run MCP `validate_theme`, check closing tags, verify JSON in schema                     |
