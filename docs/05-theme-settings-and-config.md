# Module 05: Theme Settings & Config

## What you'll learn
- `settings_schema.json` (defines) vs `settings_data.json` (current values)
- Adding global settings used across sections
- Color schemes in Dawn

## Why it matters
Theme-wide settings let merchants tweak the whole look in one place: brand colors, fonts, spacing. Without them, you hard-code values and merchants come back asking for code changes.

## Concepts

### The two files

- [config/settings_schema.json](../config/settings_schema.json) — defines what settings exist (panels, fields, defaults). Code-controlled.
- [config/settings_data.json](../config/settings_data.json) — current values + presets. Editor-controlled. Treat as content.

### Reading settings in Liquid

```liquid
{{ settings.primary_type_color }}
{{ settings.brand_font | font_face }}
```

### Adding a setting

In `settings_schema.json`, append a panel or add to an existing one:

```json
{
  "name": "Pokémon brand",
  "settings": [
    {
      "type": "color",
      "id": "primary_type_color",
      "label": "Primary type color (used for Fire badges by default)",
      "default": "#EE8130"
    },
    {
      "type": "color_scheme",
      "id": "pokedex_scheme",
      "label": "Pokédex section default scheme",
      "default": "scheme-1"
    }
  ]
}
```

### Color schemes

Dawn defines `scheme-1`..`scheme-5` in `settings_schema.json` under `color_schemes`. They map to CSS custom properties. Use them in section settings (`type: "color_scheme"`) instead of raw colors so merchants can recolor everything from one place.

### Versioning settings safely

If you change a setting `id`, the existing value in `settings_data.json` becomes orphaned. Either:
- **Add new + deprecate old** (don't remove until you're sure)
- **Migrate manually** in `settings_data.json` after coordinating with the merchant

## Prompt-driven build

```prompt
Open config/settings_schema.json. Append a new panel called "Pokémon brand" with these settings:
1. color, id "primary_type_color", default #EE8130, label about Fire type
2. color_scheme, id "pokedex_scheme", default scheme-1
3. range, id "pokedex_columns_default", min 2, max 6, step 1, default 4, label "Default Pokédex columns"

Then in sections/pokemon-type-banner.liquid (from module 03), use settings.primary_type_color as a fallback when no scheme is set. Show me the exact diff.
```

## Debug callouts

- **"Editor crashes after editing settings_schema.json"** → invalid JSON. Validate, push, check View logs.
- **"My setting works locally but resets on push"** → the editor saved `settings_data.json` between your edits. Always pull before changing settings.
- **"Color is showing as transparent"** → `color` setting needs a default; if blank, it's empty string.

## Acceptance checklist
- [ ] You added the "Pokémon brand" panel and see it in Theme settings → Customize
- [ ] You can read `settings.primary_type_color` from any Liquid file
- [ ] You changed the value in admin and see it reflected after the editor saves

## Further reading
- https://shopify.dev/docs/themes/architecture/config/settings-schema-json
- https://shopify.dev/docs/themes/architecture/settings/input-settings
