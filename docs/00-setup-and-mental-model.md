# Module 00: Setup & Mental Model

## What you'll learn
- How a Shopify theme is structured and served
- The three dev loops (GitHub sync, Shopify CLI, theme editor) and when to use each

## Why it matters
Production bugs are usually caused by misunderstanding *which file controls what*. If you can trace a URL → template → sections → snippets in your head, you'll debug in seconds instead of hours.

## Concepts

### Shopify themes are static files

Shopify serves Liquid + JSON + CSS + JS directly. **There is no build step on Shopify's side.** That's why we built Tailwind locally and committed `assets/tailwind.css`.

### File anatomy

```
layout/      ← page wrappers (theme.liquid, password.liquid). Contain <html>, <head>, <body>.
templates/   ← one per URL type (product, collection, page, index, cart, ...). JSON > Liquid.
sections/    ← reusable composable blocks. Editable in the theme editor.
snippets/    ← partials included from sections via {% render %}.
assets/      ← CSS, JS, images, fonts. Served at /assets/<filename>.
config/      ← settings_schema.json (defines), settings_data.json (current values).
locales/     ← translations. en.default.json is the source.
```

### Request flow

```
Browser hits /products/pikachu
        ↓
Shopify picks templates/product.json (or .liquid)
        ↓
JSON template lists sections to render in order
        ↓
Each section's Liquid runs, may {% render %} snippets
        ↓
All wrapped by layout/theme.liquid
        ↓
HTML response
```

### Three dev loops

| Loop | Speed | Use when |
|------|-------|----------|
| **GitHub sync** (this repo) | medium | Daily work — version controlled, rollback-friendly |
| **Shopify CLI** (`shopify theme dev`) | fastest | Local hot reload while iterating on a section |
| **Theme editor** (admin) | slow | Non-devs editing content; section reordering |

## Prompt-driven build

```prompt
Read layout/theme.liquid and tell me, in 5 bullets:
1. Where the <head> opens
2. Where global CSS is loaded
3. Where {{ content_for_layout }} renders
4. Where the cart drawer is included
5. Any conditional that depends on a theme setting
```

## Debug callouts

- **"My change doesn't show up"** → wrong dev loop. If you used the theme editor it'll commit back to GitHub; if you used GitHub it syncs to Shopify in ~30s. Don't mix mid-task.
- **"Where does this HTML come from?"** → View page source, find a class name, grep `sections/` and `snippets/` for it.
- **"Liquid is rendering but content is empty"** → object scope. `product` only exists on product templates, `cart` everywhere, `collection` only on collection templates.

## Acceptance checklist
- [ ] You can name what each top-level folder does without looking
- [ ] You can explain the request → template → section flow
- [ ] You ran the prompt and got 5 accurate bullets about `theme.liquid`

## Further reading
- https://shopify.dev/docs/themes/architecture
- https://shopify.dev/docs/themes/architecture/templates
