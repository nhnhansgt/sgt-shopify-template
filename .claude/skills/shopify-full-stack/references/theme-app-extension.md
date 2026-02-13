# Shopify Theme App Extension Reference

Complete guide for building Theme App Extensions without syntax errors.

## Overview

Theme App Extensions allow merchants to easily add dynamic elements to their themes without editing theme code. Apps built using this framework don't modify theme code directly, reducing breaking changes and improving iteration speed.

### Benefits

- **Theme editor integration**: Automatically exposes your app in the theme editor
- **No theme code modification**: Decreases risk of introducing breaking changes
- **Single deployment**: Deploy once to all online stores using your app
- **Asset hosting**: Automatic CDN hosting for fast, reliable asset delivery
- **Versioning**: Built-in version control support

## Directory Structure

```
extensions/my-theme-app-extension/
├── assets/                    # CSS, JavaScript, images, fonts
├── blocks/                    # App blocks (section target) and app embed blocks
├── snippets/                   # Reusable Liquid components
├── locales/                    # Translation files
│   ├── en.default.json          # English translations (required)
│   ├── en.default.schema.json   # Schema translations
│   ├── fr.json                  # French translations
│   └── fr.schema.json           # French schema translations
├── package.json                # Node.js dependencies
└── shopify.extension.toml       # Extension configuration
```

### Subdirectory Descriptions

| Directory | Description |
|-----------|-------------|
| `assets/` | CSS, JavaScript, images, fonts. Loaded via schema attributes or Liquid filters |
| `blocks/` | App blocks (`target: "section"`) and app embed blocks (`target: "head"`, `"body"`, `"compliance_head"`) |
| `snippets/` | Reusable Liquid code referenced in blocks using `{% render %}` |
| `locales/` | JSON translation files for merchant-facing and customer-facing strings |
| `shopify.extension.toml` | TOML configuration file with extension metadata |

## TOML Configuration

### Basic Structure

```toml
# extensions/my-extension/shopify.extension.toml
name = "my-extension"
type = "theme"
uid = "unique-identifier-generated-by-shopify"
```

### Properties

| Property | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Extension handle (filename without extension) |
| `type` | string | Yes | Must be `"theme"` for theme app extensions |
| `uid` | string | Yes | Unique identifier (auto-generated) |

## Block Types

### App Blocks (Section Target)

App blocks inject inline content on a page. Merchants add them to theme sections.

**Target value**: `"section"`

```liquid
{% schema %}
{
  "name": "My App Block",
  "target": "section",
  "stylesheet": "app-block.css",
  "javascript": "app-block.js",
  "settings": [
    { "type": "text", "id": "title", "label": "Title", "default": "Hello" }
  ]
}
{% endschema %}
```

**Use cases**:
- Product reviews and ratings
- Dynamic content pointing to products/collections
- Full-width page components

### App Embed Blocks

App embed blocks inject content before `</head>` or `</body>` tags. Used for floating/overlaid elements.

**Target values**: `"head"`, `"body"`, `"compliance_head"`

```liquid
{% schema %}
{
  "name": "App Embed",
  "target": "body",
  "settings": []
}
{% endschema %}
```

**Use cases**:
- Floating chat bubbles
- SEO meta tags and analytics
- Tracking pixels

**Target meanings**:
| Target | Description | Use when |
|--------|-------------|----------|
| `head` | Injected before `</head>` | Scripts, styles, meta tags |
| `body` | Injected before `</body>` | Floating UI, overlays |
| `compliance_head` | Injected first in `<head>` | Cookie consent banners (critical) |

## Schema Attributes

### Required Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Title in theme editor (max 25 characters) |
| `target` | string | Where block renders: `section`, `head`, `body`, `compliance_head` |

### Optional Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `javascript` | string | JS file from assets/ to auto-load in `<head>` |
| `stylesheet` | string | CSS file from assets/ to auto-load in `<head>` |
| `enabled_on` | object | Limit to specific templates/section groups |
| `disabled_on` | object | Prevent use in specific templates |
| `class` | string | Additional CSS classes (always includes `shopify-block`) |
| `tag` | string | Wrapping HTML tag (default: `div`) |
| `settings` | array | Merchant-configurable settings |
| `default` | object | Default setting configuration |
| `available_if` | string | Conditional visibility based on app metafield |

### Example Schema with Attributes

```liquid
{% schema %}
{
  "name": "Product Reviews",
  "target": "section",
  "stylesheet": "reviews.css",
  "javascript": "reviews.js",
  "class": "custom-reviews-block",
  "tag": "section",
  "enabled_on": {
    "templates": ["product", "index"]
  },
  "settings": [
    {
      "type": "product",
      "id": "product",
      "label": "Product",
      "autofill": true
    },
    {
      "type": "color",
      "id": "text_color",
      "label": "Text Color",
      "default": "#000000"
    }
  ]
}
{% endschema %}
```

## Setting Types

### Basic Input Settings

| Type | Description | Example Value |
|------|-------------|--------------|
| `checkbox` | Toggle boolean | `true`, `false` |
| `number` | Numeric input | `20` |
| `radio` | Single selection | `"option_a"` |
| `range` | Slider with min/max | `50` |
| `select` | Dropdown selection | `"option_a"` |
| `text` | Single line text | `"Hello"` |
| `textarea` | Multi-line text | `"Line 1\nLine 2"` |

### Specialized Input Settings

| Type | Description |
|------|-------------|
| `article` | Blog article selector |
| `blog` | Blog selector |
| `collection` | Collection selector |
| `collection_list` | Multiple collections |
| `color` | Color picker |
| `color_scheme` | Theme color scheme |
| `font_picker` | Font selector |
| `html` | HTML content editor |
| `image_picker` | Image selector |
| `product` | Product selector |
| `product_list` | Multiple products |
| `richtext` | Rich text editor |
| `text_alignment` | Text alignment (left/center/right) |
| `url` | URL input |
| `video` | Video URL input |
| `video_url` | YouTube/Vimeo URL |

### Setting Example

```json
{
  "type": "number",
  "id": "products_count",
  "label": "Products to show",
  "default": 4,
  "min": 1,
  "max": 20,
  "step": 1,
  "unit": "products",
  "info": "Choose how many products to display"
}
```

## Autofill Settings

Autofill automatically links resource settings to parent section's dynamic source.

```liquid
{% schema %}
{
  "name": "Featured Product Reviews",
  "target": "section",
  "settings": [
    {
      "type": "product",
      "id": "product",
      "label": "Product",
      "autofill": true
    }
  ]
}
{% endschema %}
```

When merchant adds this block to a "Featured product" section, `block.settings.product` automatically points to the section's product.

## Conditional Blocks

Control block visibility using app metafields:

```liquid
{% schema %}
{
  "name": "Premium Feature",
  "target": "section",
  "available_if": "{{ app.metafields.features.premium_enabled }}",
  "settings": [
    { "type": "text", "id": "content", "label": "Content" }
  ]
}
{% endschema %}
```

The metafield must be a boolean type in the `app` ownership namespace.

## Locales

### File Structure

```
locales/
├── en.default.json          # English (required)
├── en.default.schema.json   # Schema translations
├── fr.json                  # French
└── fr.schema.json           # French schema
```

### Locale File Types

**Schema locale files** (`*.schema.json`): Merchant-facing translations for theme editor settings

**Storefront locale files** (`*.json`): Customer-facing translations displayed in storefront

### Example Locale Files

**en.default.json** (Storefront):
```json
{
  "reviews": {
    "title": "Customer Reviews",
    "no_reviews": "No reviews yet",
    "write_review": "Write a review"
  }
}
```

**en.default.schema.json** (Schema):
```json
{
  "reviews": {
    "title": "Reviews",
    "settings": {
      "show_rating": "Show rating",
      "max_rating": "Maximum rating"
    }
  }
}
```

### Using Translations in Liquid

```liquid
<h2>{{ 'reviews.title' | t }}</h2>
<p>{{ 'reviews.no_reviews' | t }}</p>
```

## Assets

### Loading Assets

**Via schema attributes** (auto-loaded):
```json
{
  "javascript": "app-block.js",
  "stylesheet": "app-block.css"
}
```

**Via Liquid filters** (manual):
```liquid
<link rel="stylesheet" href="{{ 'styles.css' | asset_url }}">
<script src="{{ 'script.js' | asset_url }}" defer></script>

{{ 'image.jpg' | asset_img_url: '500x' }}
```

### Asset Filters

| Filter | Description | Example |
|--------|-------------|--------|
| `asset_url` | CDN URL for any asset | `{{ 'file.js' | asset_url }}` |
| `asset_img_url` | CDN URL for images with size | `{{ 'img.jpg' | asset_img_url: 'large' }}` |

### Image Sizes for asset_img_url

| Value | Dimensions |
|-------|------------|
| `small` | 100 x 100 px |
| `medium` | 240 x 240 px |
| `large` | 480 x 480 px |
| `grande` | 600 x 600 px |
| `1024x1024` | Custom dimensions |

## File and Content Size Limits

| Content | Limit | Type |
|---------|-------|------|
| All files in extension | 10 MB | Enforced |
| Number of blocks | 30 | Enforced |
| Locale files | 100 | Enforced |
| Each locale file | 15 KB | Enforced |
| Liquid across all files | 100 KB | Enforced |
| CSS (compressed) | 100 KB | Suggested |
| JS (compressed) | 10 KB | Suggested |

## Restrictions

### Pages Not Supported

App blocks and app embed blocks **cannot** be rendered on checkout pages:
- Contact information
- Shipping method
- Payment method
- Order status

### Liquid Objects Not Available

- `content_for_header`
- `content_for_index`
- `content_for_layout`
- Parent `section` object properties (except `id`)

### JSON Not Supported

Theme app extensions **do not** support:
- Comments in JSON files
- Trailing commas in JSON

## Deep Linking

### Add App Block to Template

```
https://<shopDomain>/admin/themes/current/editor?template=<template>&addAppBlockId=<api_key>/<handle>&target=newAppsSection
```

Parameters:
- `template`: JSON template (default: `index.json`)
- `api_key`: Your app's API key (client_id from app.toml)
- `handle`: Block's filename without `.liquid`

### Add App Block to Section Group

```
target=sectionGroup:<header|footer|aside>
```

### Add App Block to Main Section

```
target=mainSection
```

### Activate App Embed Block

```
https://<shopDomain>/admin/themes/current/editor?context=apps&template=<template>&activateAppId=<api_key>/<handle>
```

## Template Reference

Use working template at `templates/theme-extension/`:

- `shopify.extension.toml` - Valid TOML config
- `blocks/star_rating.liquid` - Example block with metafield
- `snippets/stars.liquid` - Reusable star component
- `locales/en.default.json` - English translations
- `assets/thumbs-up.png` - Example asset

## Common Commands

```bash
# Validate extension
shopify extension check
```
