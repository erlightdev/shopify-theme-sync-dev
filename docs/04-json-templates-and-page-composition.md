# Module 04: JSON Templates & Page Composition

## What you'll learn
- JSON templates vs Liquid templates
- How `templates/index.json` composes a page
- Creating a custom page template (`page.pokedex.json`)

## Why it matters
JSON templates make pages composable in the editor. Merchants can add/remove/reorder sections without touching code. Liquid templates are legacy — every new template should be JSON.

## Concepts

### Anatomy of a JSON template

Look at the existing [templates/index.json](../templates/index.json):

```json
{
  "sections": {
    "image_banner": { "type": "image-banner", "settings": {...}, "blocks": {...} },
    "featured_collection": { "type": "featured-collection", "settings": {...} }
  },
  "order": ["image_banner", "featured_collection"]
}
```

- `sections` — keyed map of section instances. The key is an arbitrary ID; `type` matches a file in `sections/`.
- `order` — array of those keys, in render order.
- Each section can have its own `settings` and `blocks` (matching its schema).

### Template naming and routing

| File | URL pattern |
|------|-------------|
| `templates/index.json` | `/` |
| `templates/product.json` | `/products/<handle>` |
| `templates/collection.json` | `/collections/<handle>` |
| `templates/page.json` | `/pages/<handle>` |
| `templates/page.pokedex.json` | `/pages/<handle>` if "Pokedex" template assigned in admin |
| `templates/cart.json` | `/cart` |
| `templates/article.json` | `/blogs/<blog>/<article>` |

The `.alternate` suffix (`page.pokedex.json`) creates a **template variant** assignable per-page in admin (Online Store → Pages → Edit → Theme template).

### Section groups

`sections/header-group.json` and `sections/footer-group.json` are special — they define the global header/footer composable in the editor. They're already wired into `layout/theme.liquid`.

### Static section call

You can also render a section from Liquid:
```liquid
{% section 'announcement-bar' %}
```
But JSON template wins for composability.

## Prompt-driven build

```prompt
Create templates/page.pokedex.json that:
- Renders one section of type "pokedex" (which we'll build in module 08 — for now scaffold a placeholder section if "pokedex" doesn't exist yet, with a heading "Pokédex coming soon")
- Plus the existing rich-text section type if it exists in this Dawn theme
- Has "order" listing them in that sequence

Then explain in 3 lines what I need to do in Shopify admin to actually use this template on a page.
```

## Debug callouts

- **"My new template doesn't show up in admin"** → not pushed yet, or syntax error in JSON. Check `View logs` after pushing.
- **"Section throws error in editor"** → `type` in JSON doesn't match a file in `sections/`. Filenames are kebab-case; `type` is kebab-case (no `.liquid`).
- **"Settings don't apply"** → setting `id` in JSON doesn't match any `id` in the section's schema. Editor will accept unknown ids but they're inert.

## Acceptance checklist
- [ ] You can describe what `templates/index.json` does to someone non-technical
- [ ] You created `templates/page.pokedex.json` and it appears in admin's template dropdown
- [ ] You created a Page in admin assigned to this template

## Further reading
- https://shopify.dev/docs/themes/architecture/templates/json-templates
- https://shopify.dev/docs/themes/architecture/section-groups
