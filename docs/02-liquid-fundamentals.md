# Module 02: Liquid Fundamentals

## What you'll learn
- Liquid output, tags, and filters
- Object scope per template
- Whitespace control and common gotchas

## Why it matters
Liquid is the language of every storefront page. 80% of theme bugs are Liquid mistakes — wrong object, wrong filter, wrong scope. Mastering it now saves hundreds of "why is this empty?" moments.

## Concepts

### Output vs tags

```liquid
{{ product.title }}              ← OUTPUT — prints a value
{% if product.available %}       ← TAG — control flow / logic
  In stock
{% endif %}
```

### Core objects (and where they exist)

| Object | Available on |
|--------|--------------|
| `shop` | everywhere |
| `cart` | everywhere |
| `request` | everywhere (`request.path`, `request.host`) |
| `template` | everywhere (`template.name`) |
| `product` | only `templates/product.*` and inside product loops |
| `collection` | only `templates/collection.*` and inside collection loops |
| `article`, `blog` | only their respective templates |
| `customer` | only when logged in |

**Rule:** if you reference `product` outside a product context, it's `nil` and your output is silently empty.

### Filters

Pipe a value through a filter:
```liquid
{{ product.price | money }}                  → ₨ 1,500.00
{{ 'tailwind.css' | asset_url }}             → /cdn/.../tailwind.css
{{ 'cart.empty' | t }}                       → "Your cart is empty" (translated)
{{ product.featured_image | image_url: width: 600 }}
{{ product.description | strip_html | truncate: 100 }}
```

Chain freely: `{{ x | filter1 | filter2 | filter3 }}`.

### Control flow

```liquid
{% if product.available and product.price > 0 %} ... {% endif %}
{% case product.type %}
  {% when 'Pokemon' %} ...
  {% when 'Card' %} ...
  {% else %} ...
{% endcase %}
{% for variant in product.variants %}
  {{ forloop.index }} / {{ forloop.length }} — {{ variant.title }}
{% endfor %}
```

### Variables

```liquid
{% assign type_color = 'fire' %}
{% capture badge_class %}badge badge-{{ type_color }}{% endcapture %}
<span class="{{ badge_class }}">{{ type_color }}</span>
```

### Whitespace control

```liquid
{%- if x -%}                    ← strips whitespace before AND after the tag
{{- product.title -}}           ← also works on output
```

Use `-` whenever you want clean HTML. Without it, Liquid leaves blank lines that bloat output.

### Includes

```liquid
{% render 'product-card', product: product, show_price: true %}
```

`render` (modern, scoped) is preferred over `include` (deprecated, leaks scope).

## Prompt-driven build

```prompt
Open snippets/card-product.liquid (or the closest equivalent in this repo).
Walk me through it section by section, explaining:
- Which object it expects
- Each filter chain and what it produces
- Any conditional and what it gates
Output as a numbered list with line references like file.liquid:42.
```

```prompt
Write a Liquid snippet I can drop into any section that, for debugging, dumps the current product object as JSON inside a <pre> tag — but only if ?debug=1 is in the URL.
Acceptance: the dump is hidden in production, visible when I add ?debug=1.
```

## Debug callouts

- **"Liquid error: undefined variable"** → object doesn't exist in this template. Wrap in `{% if x %}` or move the code.
- **"Output is blank but no error"** → silent nil. Inspect with `{{ object | json }}` temporarily.
- **"My HTML has weird gaps"** → missing whitespace control on tags. Add `-`.
- **"Filter is doing nothing"** → wrong type. `money` needs an integer in cents; `image_url` needs an image object.

## Acceptance checklist
- [ ] You can predict what `{{ cart.item_count | plus: 1 }}` outputs in an empty cart
- [ ] You can name 3 filters used in `snippets/card-product.liquid`
- [ ] You wrote and tested the `?debug=1` JSON dump snippet

## Further reading
- https://shopify.dev/docs/api/liquid
- https://shopify.dev/docs/api/liquid/objects
- https://shopify.dev/docs/api/liquid/filters
