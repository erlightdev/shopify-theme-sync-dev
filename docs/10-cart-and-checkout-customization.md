# Module 10: Cart & Checkout Customization

## What you'll learn
- Customizing Dawn's cart drawer
- Line item properties (custom data per cart item)
- The boundary between theme and checkout

## Why it matters
Cart UX directly affects conversion. Adding "Trainer name" as a line item property gives the order character and shows merchants how to extend the cart with custom data.

## Concepts

### Theme vs Checkout

| Surface | Customizable via |
|---------|------------------|
| Storefront, cart, cart drawer | Theme code (you) |
| Checkout pages (Shopify Plus) | Checkout extensions (apps) |
| Checkout pages (non-Plus) | Limited admin settings only |

So in this module: **theme cart only**. Checkout extension is flagged but out of scope.

### Cart drawer location

Dawn's cart drawer is `sections/cart-drawer.liquid` + `assets/cart-drawer.js`. The product form (`product-form.liquid`) submits to `/cart/add` and triggers the drawer.

### Line item properties

Add a hidden or visible input named `properties[Name]` inside the product form:

```liquid
<input type="text" name="properties[Trainer name]" placeholder="Your trainer name">
```

Shopify automatically attaches it to the cart item. It then shows up in:
- Cart page / drawer (`item.properties`)
- Order confirmation
- Admin order detail
- Email templates

```liquid
{% for item in cart.items %}
  <h3>{{ item.product.title }}</h3>
  {% for p in item.properties %}
    {%- if p.last != blank -%}
      <small>{{ p.first }}: {{ p.last }}</small>
    {%- endif -%}
  {% endfor %}
{% endfor %}
```

Properties starting with `_` are hidden from the customer (useful for internal tracking).

### Other cart customizations to consider

- **Free shipping threshold bar** — `cart.total_price` vs threshold
- **Cart notes** — already in Dawn (`cart.note`)
- **Discount code field** — `cart.cart_level_discount_applications`

## Prompt-driven build

```prompt
1. Open sections/main-product.liquid (or its block-based equivalent in this Dawn variant). Find the product form.
2. Add a new block type "trainer_name" with one setting:
   - text, id "label", default "Trainer name"
3. When that block is present, render an <input type="text" name="properties[Trainer name]" required maxlength="20"> with the configured label, styled with Tailwind.

4. Open sections/cart-drawer.liquid (or whatever Dawn renders cart line items in this repo). For each cart item, render its properties below the title (skip _-prefixed and empty).

5. Add to templates/product.json under main-product blocks: { "type": "trainer_name", "settings": {} }

Acceptance:
- Add Pikachu to cart with trainer name "Ash" → cart drawer shows "Trainer name: Ash" under the line
- Complete a test order → admin order detail shows the property
- Empty trainer name should fail with the browser's required validation
```

## Debug callouts

- **"Property doesn't show in cart"** → input name must be exactly `properties[Foo]` with that bracket syntax. Misspelling silently drops it.
- **"Property shows as empty even when filled"** → form was submitted before the input was in scope. Check it's inside the `<form action="/cart/add">`.
- **"Underscore properties showing to customer"** → cart template doesn't filter them. Add `{%- if p.first contains '_' -%}{%- continue -%}{%- endif -%}`.

## Acceptance checklist
- [ ] Trainer name input renders on the product page when block enabled
- [ ] Property persists into the cart and to the order
- [ ] Empty/long values handled gracefully
- [ ] Cart total still shows in NPR (lightskool's currency)

## Further reading
- https://shopify.dev/docs/storefronts/themes/pricing-payments/line-item-properties
- https://shopify.dev/docs/api/liquid/objects/line_item
