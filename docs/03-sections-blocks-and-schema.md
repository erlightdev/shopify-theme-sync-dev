# Module 03: Sections, Blocks & Schema

## What you'll learn
- Section anatomy: Liquid + `{% schema %}`
- Block-driven editor UX
- Presets and section groups

## Why it matters
Sections are the unit of composition in modern Shopify themes. A well-designed section gives merchants a powerful editor experience and lets you add features without touching templates.

## Concepts

### Anatomy

A section is **one Liquid file** in `sections/` with this shape:

```liquid
<section class="pokemon-type-banner color-{{ section.settings.scheme }}">
  <h2>{{ section.settings.heading }}</h2>
  {% for block in section.blocks %}
    <div {{ block.shopify_attributes }}>
      {% case block.type %}
        {% when 'badge' %}
          <span class="type-badge">{{ block.settings.label }}</span>
        {% when 'image' %}
          {{ block.settings.image | image_url: width: 400 | image_tag }}
      {% endcase %}
    </div>
  {% endfor %}
</section>

{% schema %}
{
  "name": "Pokémon Type Banner",
  "tag": "section",
  "class": "section-pokemon-type-banner",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Choose your type" },
    {
      "type": "select",
      "id": "scheme",
      "label": "Color scheme",
      "options": [
        { "value": "scheme-1", "label": "Scheme 1" },
        { "value": "scheme-3", "label": "Scheme 3" }
      ],
      "default": "scheme-1"
    }
  ],
  "blocks": [
    {
      "type": "badge",
      "name": "Type badge",
      "settings": [
        { "type": "text", "id": "label", "label": "Type name" }
      ]
    },
    {
      "type": "image",
      "name": "Image",
      "settings": [
        { "type": "image_picker", "id": "image", "label": "Image" }
      ]
    }
  ],
  "max_blocks": 12,
  "presets": [
    {
      "name": "Pokémon Type Banner",
      "blocks": [{ "type": "badge" }, { "type": "badge" }]
    }
  ]
}
{% endschema %}
```

### Key schema fields

- `settings` — section-wide config
- `blocks` — repeatable child items, each with own settings
- `presets` — what shows in the "Add section" picker
- `enabled_on` / `disabled_on` — restrict by template (e.g. `"templates": ["product"]`)
- `max_blocks` — hard cap, default 16
- `tag`, `class` — the wrapping element Shopify generates

### `block.shopify_attributes` is mandatory

Without it, the editor can't highlight/select the block. **Always** include it on the wrapping element of every block.

### Setting types you'll use most

`text`, `textarea`, `richtext`, `inline_richtext`, `number`, `range`, `checkbox`, `radio`, `select`, `color`, `color_scheme`, `image_picker`, `video`, `url`, `product`, `collection`, `blog`, `page`, `link_list`, `font_picker`.

## Prompt-driven build

```prompt
Create sections/pokemon-type-banner.liquid using the schema in docs/03.
Acceptance:
- Appears in the editor under "Add section" with name "Pokémon Type Banner"
- Has 2 settings (heading, scheme) and 2 block types (badge, image)
- Renders with sensible HTML and uses Dawn's color-scheme classes
- Each block wrapper has {{ block.shopify_attributes }}
After creating, open templates/index.json and add this section as a 3rd entry in the "order" array, with two badge blocks defaulted to "Fire" and "Water".
```

## Debug callouts

- **"Editor shows red error icon on the section"** → JSON syntax in `{% schema %}`. Trailing commas, unquoted keys, and comments are all illegal. Validate with `View logs`.
- **"Block editor doesn't highlight on hover"** → missing `{{ block.shopify_attributes }}`.
- **"Preset doesn't appear in the picker"** → preset name conflicts, or `enabled_on` excludes the current template.
- **"Setting changes don't persist"** → setting `id` clashes with another in the same schema.

## Acceptance checklist
- [ ] Your section appears in the theme editor
- [ ] Adding/removing/reordering blocks works
- [ ] Switching `scheme` setting changes the section's color scheme
- [ ] Pushed to staging branch and visible in preview

## Further reading
- https://shopify.dev/docs/themes/architecture/sections
- https://shopify.dev/docs/themes/architecture/sections/section-schema
