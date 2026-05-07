# Module 08: Pokédex Section Build

## What you'll learn
- Building a custom section that loops products
- Client-side filtering and search with vanilla JS
- Wiring metafields into the UI

## Why it matters
This is the flagship feature. It pulls together everything so far — sections, schemas, metafields, Tailwind, JSON templates — into one user-facing page.

## Concepts

### What we're building

A `pokedex` section that:
1. Takes a collection setting (which products to show)
2. Renders each product as a card with image, name, Pokédex number, type badges
3. Has filter chips at the top (one per type)
4. Has a search input that filters by name
5. All filtering is client-side (no Shopify search API calls)

### Why client-side

For ≤500 products it's instant and stateless. For larger catalogs you'd use Shopify's search/filter API; we'll mention that path but not implement it.

### Architecture

```
sections/pokedex.liquid          ← markup, schema, includes inline JS at bottom
  ↳ {% render 'pokemon-card', product: p %}   ← from module 07 + new wrapper
snippets/pokemon-card.liquid     ← single card markup with data-name, data-types
assets/pokedex.js                ← (optional) extracted filter/search logic
```

We'll inline the JS in the section for simplicity, then extract if it grows.

### Data attributes drive the filter

```html
<article class="pokemon-card" data-name="pikachu" data-types="electric">
  ...
</article>
<button class="filter-chip" data-filter="fire">Fire</button>
<input class="pokedex-search" type="search">
```

JS reads `data-filter` from clicked chip and `data-types` from cards, toggles `hidden` class.

## Prompt-driven build

```prompt
Create sections/pokedex.liquid implementing the Pokédex described in docs/08.

Schema:
- collection (collection picker, required)
- columns_desktop (range 2-6, default 4)
- show_search (checkbox, default true)
- show_type_filters (checkbox, default true)
- preset name "Pokédex"

Markup:
- Outer <section> with Tailwind container
- Filter chips row (computed by collecting unique types across products in the collection)
- Search input
- Grid of cards using snippets/pokemon-card.liquid (create this too)

Card snippet:
- <article> with data-name="{{ product.title | downcase | escape }}" and data-types="{{ types | join: ' ' | downcase }}"
- Product image (use image_url + image_tag with proper width/height)
- Pokédex # formatted as #025
- Title
- Type badges (reuse the styling pattern from module 07)
- Link to product page

Filter/search JS at the bottom of pokedex.liquid:
- Click chip → toggle .active, filter cards by matching data-types containing the filter value
- Type in search → debounce 150ms, filter cards by data-name including the query
- Combining filters AND search both apply
- "All" chip clears type filter

Add the section to templates/page.pokedex.json (replace the placeholder from module 04).

Acceptance:
- Visit the Pokédex page → see all 5 starter Pokémon
- Click "Fire" → only Charizard visible
- Type "pi" → only Pikachu visible
- Clear search + click "All" → all visible again
```

## Debug callouts

- **"Cards don't filter"** → open DevTools, inspect a card. Is `data-types` actually populated? If empty, your metafield read is wrong (back to module 07 debug callouts).
- **"Search lags"** → debounce is missing or too short, or you're re-querying the DOM each keystroke. Cache the NodeList once.
- **"Layout shifts when filtering"** → toggling `display: none` is fine, but reserve space if you want a fade transition.
- **"Type chips show duplicates"** → use a `{% assign types = '' | split: ',' %}` then push uniques, or use `| uniq` filter on the joined string.

## Acceptance checklist
- [ ] Pokédex page renders all 5 Pokémon
- [ ] Type filter chips work (single-select)
- [ ] Search filters by name in <100ms
- [ ] Combining type + search works
- [ ] Mobile view: cards stack to 2 columns by default
- [ ] No console errors

## Further reading
- https://shopify.dev/docs/themes/architecture/sections/section-schema#enabled_on
- https://shopify.dev/docs/storefronts/themes/navigation-search/filtering/storefront-filtering
