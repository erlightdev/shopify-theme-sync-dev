# Module 11: Performance & Core Web Vitals

## What you'll learn
- The three Core Web Vitals (LCP, CLS, INP) and what moves them
- Image, JS, and CSS strategy in a Shopify theme
- Auditing with Lighthouse and Shopify's web-vitals data

## Why it matters
Slow themes lose sales and SEO rank. Mobile shoppers abandon at >3s LCP. Shopify themes have specific perf footguns (images, third-party scripts, render-blocking CSS) that you can avoid if you know them.

## Concepts

### The three vitals

| Metric | Target | What hurts it |
|--------|--------|---------------|
| **LCP** (Largest Contentful Paint) | <2.5s | Big un-optimized hero image, render-blocking CSS, slow server |
| **CLS** (Cumulative Layout Shift) | <0.1 | Images without dimensions, async-loaded fonts, ads injected late |
| **INP** (Interaction to Next Paint) | <200ms | Heavy JS on main thread, unoptimized event handlers |

### Image strategy

```liquid
{{ product.featured_image
   | image_url: width: 1500
   | image_tag:
       loading: 'lazy',
       sizes: '(min-width: 750px) 50vw, 100vw',
       widths: '300, 600, 900, 1200, 1500',
       width: product.featured_image.width,
       height: product.featured_image.height
}}
```

For above-the-fold hero/LCP image:
```liquid
{{ image | image_url: width: 1500 | image_tag:
   loading: 'eager',
   fetchpriority: 'high',
   ...
}}
```

**Always set `width` and `height`** to prevent CLS.

### JS strategy

- Default to `defer` on `<script>` — Shopify's `script_tag` filter does this
- No jQuery (Dawn doesn't use it — don't add it)
- Lazy-load heavy components (e.g. video players) on intersection
- Audit Dawn's bundles — `assets/global.js` is loaded everywhere; check what you can remove

### CSS strategy

- `base.css` is loaded on every page — keep it lean
- Section-specific CSS: load via `media="print" onload="this.media='all'"` pattern (Dawn already does this for some files)
- Tailwind output: `npm run build` (minified) before production push
- Inline critical CSS for above-the-fold (advanced — defer until needed)

### Third parties

Every third-party script (analytics, chat, reviews) costs LCP and INP. Audit ruthlessly. If it doesn't need to run before interaction, defer it.

## Prompt-driven build

```prompt
1. Run a Lighthouse mobile audit on the staging Pokédex page (give me the URL pattern to use).
2. Read the report and tell me:
   - LCP element (which image/text)
   - Top 3 opportunities by potential savings
   - Any render-blocking resources
3. For each opportunity, propose a specific code change with file:line refs.
4. Implement the lowest-effort, highest-impact change first.

Acceptance: re-run Lighthouse, mobile Performance score increases by ≥5 points and is ≥85.
```

```prompt
Audit assets/global.js. Tell me:
- Total file size
- Top 5 features by approximate LOC
- Any feature unused on the Pokédex page that could be lazy-loaded
```

## Debug callouts

- **"LCP is the wrong element"** → Lighthouse shows the actual LCP element. Often it's the first product card image, not the hero. Apply eager + fetchpriority to *that*.
- **"CLS spikes after page load"** → web fonts loading. Use `font-display: optional` or preload critical font.
- **"Performance regressed after a deploy"** → `git bisect` between the last good commit and HEAD; check what scripts/sections changed.
- **"Lighthouse score varies wildly"** → throttling. Always run mobile, simulated 4G, multiple times, take median.

## Acceptance checklist
- [ ] Lighthouse mobile Performance ≥85 on Pokédex page
- [ ] All product images have explicit width/height
- [ ] No console errors or warnings
- [ ] No render-blocking scripts in `<head>`
- [ ] `assets/tailwind.css` minified

## Further reading
- https://shopify.dev/docs/storefronts/themes/best-practices/performance
- https://web.dev/articles/vitals
