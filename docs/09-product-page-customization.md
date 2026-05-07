# Module 09: Product Page Customization

## What you'll learn
- Adding custom blocks to the product page
- Building stat bars and type badges
- Traversing product references for evolution chains

## Why it matters
The product page is where the sale happens. A Pokémon-themed product page with stats, badges, and evolution chain converts visitors into buyers because it feels like a Pokédex entry, not a generic store page.

## Concepts

### Two paths to customize the product page

1. **Add blocks to `sections/main-product.liquid`** — preserves Dawn updates, easy to remove
2. **Override `sections/main-product.liquid`** — full control, but you own the maintenance

We'll do **path 1** — add custom block types. This stays compatible with future Dawn upgrades.

### Anatomy of a product page block

In `sections/main-product.liquid`, the schema has a `blocks` array. Each block type has a `type` and `settings`. To add Pokémon-specific blocks:

```json
{
  "type": "pokemon_stats",
  "name": "Pokémon stats",
  "limit": 1
}
```

Then in the Liquid loop:
```liquid
{%- when 'pokemon_stats' -%}
  {% render 'pokemon-stats', product: product %}
```

`limit: 1` prevents merchants from adding it twice.

### Evolution chain

```liquid
{%- assign evolves_from = product.metafields.pokemon.evolves_from.value -%}
{%- if evolves_from -%}
  <p>Evolves from
    <a href="{{ evolves_from.url }}">
      {{ evolves_from.featured_image | image_url: width: 80 | image_tag }}
      {{ evolves_from.title }}
    </a>
  </p>
{%- endif -%}
```

Walk the chain backward by recursion (limit depth — Pokémon evolutions go max 3 stages).

### Stat bars with Tailwind

```liquid
{%- assign hp = product.metafields.pokemon.hp | default: 0 -%}
<div class="flex items-center gap-2">
  <span class="w-12 text-sm font-mono">HP</span>
  <div class="flex-1 h-3 bg-gray-200 rounded">
    <div class="h-full bg-red-500 rounded" style="width: {{ hp | times: 100 | divided_by: 255 }}%"></div>
  </div>
  <span class="w-10 text-right text-sm font-mono">{{ hp }}</span>
</div>
```

## Prompt-driven build

```prompt
1. Open sections/main-product.liquid. Add three new block types to its schema:
   - pokemon_stats (limit 1)
   - type_badges (limit 1)
   - evolution_chain (limit 1)

2. In the section's Liquid {% case block.type %}, add three new branches that {% render %} corresponding snippets.

3. Create:
   - snippets/pokemon-stats.liquid — render HP/Attack/Defense bars (use module 09 markup)
   - snippets/type-badges.liquid — render type badges with class type-<lowercased>
   - snippets/evolution-chain.liquid — show the previous evolution if metafields.pokemon.evolves_from is set; recurse one more step backward if that has its own evolves_from

4. In templates/product.json (or product.<variant>.json), add the three blocks under main-product section in this order: type_badges, pokemon_stats, evolution_chain. Place them after the title block.

5. Add CSS for type colors in src/tailwind.css using @theme tokens, then use them via type-fire, type-water etc. classes. Run npm run build.

Acceptance:
- /products/charizard shows: type badge "Fire" in orange, stat bars (HP 78, Atk 84, Def 78), evolution chain showing Charmander → Charmeleon (if you've created those, otherwise just show Charmeleon)
```

## Debug callouts

- **"Block doesn't appear in product editor"** → product template is `product.liquid` (legacy) not `product.json`. Convert it first.
- **"Stat bar fills wrong width"** → integer division. Use `| times: 100 | divided_by: 255` (multiply first, divide second) so Liquid doesn't truncate.
- **"Evolution chain infinite loop"** → cap recursion depth. Pokémon has max 3 stages — limit to 2 backward jumps.
- **"Type colors don't render"** → built CSS not pushed, or `@source` doesn't cover the snippet folder.

## Acceptance checklist
- [ ] All 3 new blocks visible in product page editor, can be reordered
- [ ] Stat bars proportional and color-coded
- [ ] Type badges use the right color per type
- [ ] Evolution chain renders (or gracefully hides if no evolves_from)
- [ ] Mobile layout works (stats stack, badges wrap)

## Further reading
- https://shopify.dev/docs/themes/architecture/sections/main-product-section
- https://shopify.dev/docs/api/liquid/objects/product
