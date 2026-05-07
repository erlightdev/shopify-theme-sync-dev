# Module 06: Tailwind v4 Integration

## What you'll learn
- How Tailwind v4 is wired into this Shopify theme (already done)
- The `@source` + no-preflight strategy
- When to use Tailwind vs Dawn's existing classes

## Why it matters
Shopify won't run a build, so Tailwind compiles **locally** and the output is committed. Without preflight, Tailwind utilities live alongside Dawn's existing styles without breaking them — you get speed without rewriting what already works.

## Concepts

### What's in this repo

| File | Role |
|------|------|
| `package.json` | npm scripts: `dev` (watch), `build` (minified) |
| `src/tailwind.css` | Source: imports utilities only, no preflight, scans Liquid + JS |
| `assets/tailwind.css` | Built output (committed). Served by Shopify. |
| `layout/theme.liquid:259` | `<link>` tag loads it after `base.css` |

### `src/tailwind.css` explained

```css
@source "../**/*.liquid";    /* tells Tailwind to scan Liquid for class names */
@source "../**/*.js";        /* + JS strings */

@layer theme, base, components, utilities;

@import "tailwindcss/theme.css" layer(theme);            /* design tokens */
@import "tailwindcss/utilities.css" layer(utilities);    /* utility classes */
/* preflight intentionally NOT imported — Dawn keeps its own reset */
```

### npm scripts

```bash
npm run dev      # watch — rebuild on every Liquid save
npm run build    # one-shot minified — run before committing for production
```

### Class strategy

Rule of thumb:

| Need | Use |
|------|-----|
| Layout/spacing in **new** sections | Tailwind utilities |
| Existing Dawn sections | Don't touch — use Dawn's classes |
| Brand colors | Theme settings → CSS variable → `bg-[var(--my-color)]` |
| Responsive behavior | Tailwind's `sm:`, `md:`, `lg:` |
| Dark mode | Skip for now — Dawn doesn't ship dark mode |

### Workflow

```bash
# Terminal 1
npm run dev

# Then edit any *.liquid → assets/tailwind.css rebuilds → save → git commit → push
# Before committing for production, ALWAYS:
npm run build
git add assets/tailwind.css
```

## Prompt-driven build

```prompt
Sanity check the Tailwind setup:
1. Add a div with classes "bg-yellow-300 p-6 rounded-lg text-black" inside sections/pokemon-type-banner.liquid (after the heading)
2. Run npm run build
3. Confirm those exact classes ended up in assets/tailwind.css (grep for one of them)
4. Tell me the file size before and after

Acceptance: yellow background renders on staging within 60 seconds of pushing.
```

## Debug callouts

- **"Class not applying"** → (1) file isn't matched by `@source` glob, (2) you forgot to rebuild, (3) you forgot to commit `assets/tailwind.css`, (4) Dawn's CSS has higher specificity — try `!` prefix: `!bg-red-500`.
- **"Build is huge"** → run with `--minify` (the `build` script does this). v4 only ships used classes, but check `@source` isn't pulling in `node_modules`.
- **"Watch isn't detecting Liquid changes"** → `@source` glob may be wrong. Ensure paths are relative to `src/tailwind.css`.

## Acceptance checklist
- [ ] `npm run dev` runs without errors
- [ ] Adding a new Tailwind class to any Liquid file rebuilds within 1s
- [ ] `assets/tailwind.css` is committed in the same commit as the Liquid that uses it
- [ ] You can articulate why preflight is disabled

## Further reading
- https://tailwindcss.com/docs/installation/using-the-cli
- https://tailwindcss.com/docs/preflight
