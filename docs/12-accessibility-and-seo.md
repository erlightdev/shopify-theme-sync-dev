# Module 12: Accessibility & SEO

## What you'll learn
- Practical a11y for Shopify themes
- Structured data (Product JSON-LD)
- SEO checklist that matters

## Why it matters
Accessibility is a legal requirement in many markets and a UX win for everyone. SEO determines whether anyone finds your store. Both are non-negotiable for production.

## Concepts

### Accessibility essentials

| Area | Quick check |
|------|-------------|
| Alt text | Every product image has descriptive alt (Shopify uses product title fallback) |
| Color contrast | Body text ≥4.5:1, large text ≥3:1 (use axe DevTools) |
| Keyboard nav | Tab through every interactive element; visible focus ring everywhere |
| ARIA | Filter chips: `role="button"` + `aria-pressed="true/false"` |
| Form labels | Every input has a `<label for>` or `aria-label` |
| Headings | One `<h1>` per page; hierarchy doesn't skip levels |
| Reduced motion | Respect `prefers-reduced-motion` in any animations |

### Structured data — Product JSON-LD

Add to `sections/main-product.liquid` (or its head injection point):

```liquid
<script type="application/ld+json">
{
  "@context": "https://schema.org/",
  "@type": "Product",
  "name": {{ product.title | json }},
  "image": [{{ product.featured_image | image_url: width: 1500 | prepend: 'https:' | json }}],
  "description": {{ product.description | strip_html | truncate: 500 | json }},
  "sku": {{ product.selected_or_first_available_variant.sku | json }},
  "brand": { "@type": "Brand", "name": {{ shop.name | json }} },
  "offers": {
    "@type": "Offer",
    "url": {{ shop.url | append: product.url | json }},
    "priceCurrency": {{ cart.currency.iso_code | json }},
    "price": {{ product.selected_or_first_available_variant.price | divided_by: 100.0 | json }},
    "availability": "{% if product.available %}InStock{% else %}OutOfStock{% endif %}"
  }
}
</script>
```

Validate with https://search.google.com/test/rich-results.

### Other SEO essentials

- Title template: `{{ page_title }} – {{ shop.name }}` (Shopify default)
- Meta description: filled per product/page in admin
- OG tags: Dawn ships with these in `theme.liquid`'s `<head>`
- Canonical URLs: Shopify handles automatically
- `robots.txt`: Shopify auto-generates; override only if needed (admin → Online Store → Preferences)
- Sitemap: auto at `/sitemap.xml`
- Submit sitemap to Google Search Console after launch

## Prompt-driven build

```prompt
1. Run axe DevTools on:
   - Pokédex page
   - A product page (Pikachu)
   - Cart drawer (open it first)
   Report all serious + critical issues with file:line refs.

2. Fix each, prioritizing:
   - Missing alt text
   - Color contrast on type badges
   - Filter chips lacking ARIA
   - Form inputs without labels

3. Add Product JSON-LD to the product page (use the template in docs/12).

4. Validate Charizard product page in Rich Results Test → confirm "Product" structured data is detected and valid.

Acceptance: axe shows 0 serious/critical issues; Rich Results Test passes.
```

## Debug callouts

- **"axe says low contrast on type badges"** → some types (Yellow/Electric on white) need darker text. Use a contrast-aware text color per type.
- **"Filter chips don't announce state to screen readers"** → toggle `aria-pressed` when active.
- **"JSON-LD invalid"** → unescaped quotes in title/description. Use `| json` filter (it handles escaping).
- **"OG image is wrong"** → Shopify uses `shop.brand.cover_image` or first image; override per-page in admin or in `theme.liquid`.

## Acceptance checklist
- [ ] axe DevTools: 0 serious or critical on all 3 surfaces
- [ ] All Pokémon images have alt text (defaults to product title — verify)
- [ ] Keyboard tab order is logical on every page
- [ ] Product JSON-LD validates in Rich Results Test
- [ ] Meta titles and descriptions filled for all products
- [ ] Sitemap accessible at `/sitemap.xml`

## Further reading
- https://shopify.dev/docs/storefronts/themes/best-practices/accessibility
- https://shopify.dev/docs/storefronts/themes/seo
- https://schema.org/Product
