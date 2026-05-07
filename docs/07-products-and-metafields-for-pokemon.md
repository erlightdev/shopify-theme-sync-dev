# Module 07: Products & Metafields for Pokémon

## What you'll learn
- Metafield definitions vs values
- Designing the Pokémon data model
- Reading metafields from Liquid

## Why it matters
Metafields are how you extend Shopify's product model with custom fields. You'll model HP/Attack/Defense/Type — and these fields will power every section we build from here on.

## Concepts

### Metafield definitions live in admin

Settings → Custom data → Products → **Add definition**.

Each definition has:
- **Namespace** — group prefix (we'll use `pokemon`)
- **Key** — the field name
- **Type** — single-line text, integer, list, product reference, etc.
- **Validation** — min/max, allowed values

Once defined, every product gets the field in admin (Product → Metafields).

### Our schema

| Namespace | Key | Type | Notes |
|-----------|-----|------|-------|
| `pokemon` | `type` | List of single-line text | Fire, Water, Grass, Electric, Psychic, Normal, Rock, Ghost… |
| `pokemon` | `hp` | Integer | 1-255 |
| `pokemon` | `attack` | Integer | 1-255 |
| `pokemon` | `defense` | Integer | 1-255 |
| `pokemon` | `pokedex_number` | Integer | 1-1010 |
| `pokemon` | `evolves_from` | Product reference | Optional |
| `pokemon` | `is_legendary` | Boolean | |

### Reading metafields in Liquid

```liquid
{{ product.metafields.pokemon.hp }}              {# 35 #}
{{ product.metafields.pokemon.type | join: ', ' }}   {# "Electric" #}

{% for type in product.metafields.pokemon.type.value %}
  <span class="badge type-{{ type | downcase }}">{{ type }}</span>
{% endfor %}

{% assign evolves = product.metafields.pokemon.evolves_from.value %}
{% if evolves %}
  Evolves from <a href="{{ evolves.url }}">{{ evolves.title }}</a>
{% endif %}
```

**Always use `.value`** for list types and references — without it you get the raw metafield object.

### The 5 starter products

Create in Shopify admin → Products → Add:

| # | Title | Pokédex # | Type | HP | Atk | Def |
|---|-------|-----------|------|----|----|-----|
| 1 | Bulbasaur | 1 | Grass | 45 | 49 | 49 |
| 2 | Squirtle | 7 | Water | 44 | 48 | 65 |
| 3 | Pikachu | 25 | Electric | 35 | 55 | 40 |
| 4 | Charizard | 6 | Fire | 78 | 84 | 78 |
| 5 | Snorlax | 143 | Normal | 160 | 110 | 65 |

Use any product image (Bulbapedia or Pokemon.com sprites).

## Prompt-driven build

```prompt
Walk me through, step by step (with screenshots-style descriptions), how to:
1. Create the 7 metafield definitions in admin under namespace "pokemon"
2. Create 5 starter Pokémon products with the data in module 07
3. Verify by visiting /products/pikachu and seeing the metafields in admin

Then create snippets/pokemon-stats.liquid that takes a product and renders:
- Pokédex number (formatted #025)
- Type badges (one per type, with class type-<lowercased>)
- Three stat bars (HP, Attack, Defense) using <progress> or styled divs

Use Tailwind classes for the bars. Acceptance: rendering this snippet on Pikachu shows correct values.
```

## Debug callouts

- **"Metafield is empty in Liquid but has a value in admin"** → namespace or key typo (most common). Verify with `{{ product.metafields | json }}`.
- **"List type prints as 'ListMetafield'"** → forgot `.value` then a filter. Use `{% for x in product.metafields.pokemon.type.value %}`.
- **"Reference is broken"** → forgot `.value` on the reference. `evolves_from.value.url`, not `evolves_from.url`.
- **"Definition created but field doesn't appear on product"** → page refresh or wrong resource type (you defined it for "Variants" not "Products").

## Acceptance checklist
- [ ] All 7 metafield definitions exist
- [ ] 5 starter Pokémon products created with full data
- [ ] `snippets/pokemon-stats.liquid` renders correctly when included in a product template
- [ ] You can read any metafield in Liquid without consulting docs

## Further reading
- https://shopify.dev/docs/apps/build/custom-data/metafields
- https://shopify.dev/docs/api/liquid/objects/metafield
