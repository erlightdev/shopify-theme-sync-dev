# Pokémon Store: A-Z Shopify Theme Curriculum

A 15-module, prompt-driven curriculum to build a production-ready Pokémon store on top of Dawn 15.4.1, with GitHub ↔ Shopify sync.

## How to use this

Each module is **30-60 minutes** and follows the same structure:

1. **What you'll learn** — outcomes
2. **Why it matters** — production angle
3. **Concepts** — the manual tutorial (read this slowly)
4. **Prompt-driven build** — copy-paste blocks for Claude (work this fast)
5. **Debug callouts** — common failures + fixes
6. **Acceptance checklist** — tick before moving on
7. **Further reading** — official Shopify docs

The intent: **learn slowly, build fast.** Concepts give you the mental model so the prompts aren't black boxes.

## Prerequisites

- This repo cloned locally
- GitHub `staging` and `main` branches connected to themes in Shopify admin (already done)
- Node 18+ and npm (already verified)
- A Shopify development store (or partner sandbox)
- Claude Code CLI

## Curriculum

| # | Module | Time | Builds |
|---|--------|------|--------|
| 00 | [Setup & mental model](00-setup-and-mental-model.md) | 30m | — |
| 01 | [GitHub sync & branching](01-github-sync-and-branching.md) | 30m | Workflow doc |
| 02 | [Liquid fundamentals](02-liquid-fundamentals.md) | 60m | Read-along |
| 03 | [Sections, blocks & schema](03-sections-blocks-and-schema.md) | 60m | Type Banner section |
| 04 | [JSON templates](04-json-templates-and-page-composition.md) | 45m | `page.pokedex.json` |
| 05 | [Theme settings & config](05-theme-settings-and-config.md) | 30m | Brand color settings |
| 06 | [Tailwind v4 integration](06-tailwind-v4-integration.md) | 30m | (already wired) |
| 07 | [Products & metafields](07-products-and-metafields-for-pokemon.md) | 60m | 5 starter Pokémon |
| 08 | [Pokédex section](08-pokedex-section-build.md) | 60m | Filterable grid |
| 09 | [Product page customization](09-product-page-customization.md) | 60m | Stat bars, evolutions |
| 10 | [Cart customization](10-cart-and-checkout-customization.md) | 45m | Trainer name |
| 11 | [Performance & Core Web Vitals](11-performance-and-core-web-vitals.md) | 60m | Lighthouse ≥85 |
| 12 | [Accessibility & SEO](12-accessibility-and-seo.md) | 60m | axe + JSON-LD |
| 13 | [Debugging playbook](13-debugging-playbook.md) | reference | — |
| 14 | [Production launch checklist](14-production-launch-checklist.md) | 60m | Go live |

## Working rhythm

- One module per day → done in ~3 weeks
- Always work on the `staging` branch
- Commit after each acceptance checklist passes
- Revisit module 13 (debugging) any time you're stuck — it's a reference, not a tutorial
