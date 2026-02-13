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

## Workflow: Creating Theme App Extensions

**CRITICAL** - Always copy from template, never create from scratch:

### Step 1: Copy Template Structure

```bash
# Copy entire template directory
cp -r .claude/skills/shopify-full-stack/templates/theme-extension extensions/my-new-extension
```

Template includes:
- `shopify.extension.toml` - Valid TOML configuration
- `blocks/*.liquid` - App block examples
- `snippets/*.liquid` - Reusable Liquid snippets
- `locales/*.json` - English translations + schema
- `assets/*` - CSS, JS, images

### Step 2: Modify Copied Files

1. **Rename extension directory** to match your feature
2. **Edit TOML** - Update `name` only (keep `uid` and structure)
3. **Modify blocks** - Edit `.liquid` files for your functionality
4. **Update locales** - Add your translation keys
5. **Add/modify assets** - CSS, JS as needed

### Step 3: Validate Before Running

```bash
# Always validate before shopify app dev
cd extensions/my-new-extension
shopify extension validate
```

### MCP Usage for TOML

When editing `shopify.extension.toml`:
1. Call `mcp__shopify-dev-mcp__search_docs_chunks` with conversationId
2. Prompt: `"toml configuration api_version type targeting settings"`
3. Compare with template structure
4. Validate with `shopify extension validate`

## Liquid Validation Workflow (CRITICAL)

**MANDATORY** for ALL `.liquid` file creation/modification:

### Validation Loop

```
Create/Edit .liquid → Validate → Fix Errors → Re-validate → Done
```

### Step-by-Step Validation

**Step 1: After creating/editing ANY .liquid file**

```bash
# Get conversationId first (do this once per session)
mcp__shopify-dev-mcp__learn_shopify_api(api: "liquid")
```

**Step 2: Validate Liquid syntax with MCP**

Use `mcp__shopify-dev-mcp__validate_theme` tool:

```
Parameters:
- conversationId: from learn_shopify_api
- absoluteThemePath: "/path/to/extension/directory"
- filesCreatedOrUpdated: [
    { "path": "blocks/my-block.liquid" },
    { "path": "snippets/my-snippet.liquid" }
  ]
```

**Step 3: Fix all validation errors**

Common errors and fixes:
- Missing closing tag → Add `{% endif %}`, `{% endfor %}`, etc.
- Invalid JSON in schema → Fix commas, quotes, brackets
- Wrong filter syntax → Check `{{ }}` vs `{% %}`
- Invalid tag → Check against allowed tags list

**Step 4: Re-validate until clean**

```bash
# Only run shopify app dev AFTER validation passes
shopify app dev
```

### Quick Validation Checklist

Before marking task complete, verify:

- [ ] All `{% if %}` have `{% endif %}`
- [ ] All `{% for %}` have `{% endfor %}`
- [ ] All `{% schema %}` have valid JSON
- [ ] All `{% stylesheet %}` have valid CSS
- [ ] All `{% javascript %}` have valid JS
- [ ] No `{% content_for_header %}` in app blocks
- [ ] MCP validation passes with no errors
- [ ] `shopify app dev` starts without Liquid errors

### When to Use Which Validation Tool

| Scenario | Tool |
|----------|------|
| Working in actual extension directory | `validate_theme` |
| Creating code blocks without directory | `validate_theme_codeblocks` |
| Validating GraphQL in Liquid | `validate_graphql_codeblocks` |

### Common Liquid Syntax Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `Unexpected end of file` | Missing `{% endif %}` or `{% endfor %}` | Add closing tag |
| `Invalid JSON` in schema | Missing comma, quote, or bracket | Fix JSON syntax |
| `Unknown tag` | Using forbidden tag like `content_for_header` | Remove or replace |
| `Filter not found` | Typo in filter name | Check filter spelling |
| `Undefined variable` | Variable not assigned or out of scope | Check variable scope |

## Common Tasks

### Creating a New App Block

1. Copy `templates/theme-extension/blocks/star_rating.liquid`
2. Rename to your block name (e.g., `my_feature.liquid`)
3. Edit schema: change `name`, `target`, add your `settings`
4. Update Liquid logic for your feature
5. Add translation keys to `locales/en.default.json`
6. Add CSS/JS to `assets/` if needed

### Creating an App Embed Block

1. Use `target: "head"`, `"body"`, or `"compliance_head"`
2. Add `enabled_on` to limit page loading
3. Copy template block and modify for embed use

### Adding Translations

1. Copy structure from `locales/en.default.json`
2. Use hierarchical keys: `"feature_name": { "title": "...", "settings": {...} }`
3. Use `{{ 'key' | t }}` in Liquid files

## Troubleshooting

### "Invalid extension configuration" Error

1. Check TOML syntax matches template structure
2. Call MCP to get current valid TOML format
3. Verify `name` matches directory name
4. Run `shopify extension validate`

### Extension Not Appearing in Theme Editor

1. Check block has valid `target` in schema
2. Verify `shopify.extension.toml` `type = "theme"`
3. Restart `shopify app dev`
4. Check browser console for errors

### MCP

- Use `shopify-dev-mcp` mcp server for Shopify development
