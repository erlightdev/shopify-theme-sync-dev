# Module 14: Production Launch Checklist

## What you'll learn
- The single checklist to run before publishing `main`
- Post-launch monitoring basics

## Why it matters
This is the gate. Don't publish without ticking every box. A 30-minute checklist beats a 30-day customer support backlog.

---

## Pre-launch checklist

### Content & data
- [ ] All product metafield definitions match what Liquid reads (no typos)
- [ ] At least one product per Pokémon type used in filters
- [ ] All product images have descriptive alt text (not the filename)
- [ ] All product titles, descriptions, and SEO fields filled
- [ ] Currency, weight units, and locale match the storefront (NPR for lightskool)
- [ ] All pages (About, Contact, FAQ, Policies) created and assigned templates

### Visual & UX
- [ ] Renders on mobile (320px), tablet (768px), desktop (1280px)
- [ ] Cart drawer flow works end-to-end with a test order
- [ ] Search works (storefront search, not just Pokédex client-side filter)
- [ ] All Dawn sections in `templates/index.json` reviewed and either kept or removed intentionally
- [ ] Favicon set
- [ ] Social share image set (admin → Online Store → Preferences)
- [ ] Brand fonts loaded and rendering on all pages
- [ ] No placeholder text ("Lorem ipsum", "Coming soon", "TODO")
- [ ] 404 page customized

### Performance
- [ ] Lighthouse mobile: Performance ≥85, Accessibility ≥95, SEO 100, Best Practices ≥95
- [ ] No render-blocking scripts above the fold
- [ ] `assets/tailwind.css` minified (`npm run build` before final commit)
- [ ] All product images served at appropriate sizes
- [ ] Hero image has `loading="eager"` + `fetchpriority="high"`
- [ ] All other images have explicit `width`/`height`

### SEO
- [ ] Meta title and description set per template type
- [ ] Product JSON-LD validates in Rich Results Test
- [ ] OG tags present and showing correct preview (test at https://www.opengraph.xyz/)
- [ ] Sitemap accessible at `/sitemap.xml`
- [ ] `robots.txt` correct (no `Disallow: /` left from staging)
- [ ] Canonical URLs render correctly

### Accessibility
- [ ] axe DevTools: 0 serious or critical issues on home, Pokédex, product, cart
- [ ] Keyboard navigation reaches every interactive element with visible focus
- [ ] Color contrast ≥ AA for body text
- [ ] All forms have labels
- [ ] Skip-to-content link works

### Operational
- [ ] Password protection removed (or scheduled to remove at launch)
- [ ] GA4 / Meta Pixel / analytics installed and firing (verify in real-time)
- [ ] Test purchase via real payment gateway in test mode
- [ ] Email templates customized: order confirmation, shipping confirmation, refund
- [ ] Refund, shipping, privacy, and terms policies published and linked in footer
- [ ] Cookie consent banner if required (EU/UK)
- [ ] Domain configured (no `myshopify.com` URLs for customers)
- [ ] HTTPS verified on custom domain

### Git/sync hygiene
- [ ] `staging` and `main` themes both connected in Shopify admin
- [ ] No uncommitted changes locally
- [ ] `package-lock.json` committed
- [ ] No `.env` or secrets in repo
- [ ] Tag the launch commit:
  ```bash
  git checkout main
  git tag -a v1.0.0 -m "Production launch"
  git push origin v1.0.0
  ```

---

## Launch day

1. Final pull on both branches: `git checkout main && git pull && git checkout staging && git pull`
2. Final merge: `git checkout main && git merge staging && git push`
3. In Shopify admin: **Themes → main theme → Publish**
4. Remove password protection: Online Store → Preferences → Restrict access (uncheck)
5. Monitor for 30 min:
   - DevTools console on homepage
   - Place a real test order from a clean browser
   - Check Lighthouse on the live URL
   - Watch GA4 real-time

---

## Post-launch (first 7 days)

- [ ] Daily: check Shopify Theme → View logs for sync errors
- [ ] Daily: check error-rate in Shopify analytics
- [ ] Day 1: submit sitemap to Google Search Console
- [ ] Day 1: validate Product structured data on 3 random products
- [ ] Day 3: re-run Lighthouse on production
- [ ] Day 7: review Web Vitals report in GSC
- [ ] Bug fixes go through staging → main as usual

---

## Hotfix protocol

If you must push directly to `main` (live) for an urgent fix:

```bash
git checkout main
# fix
git commit -m "hotfix: <what>"
git push origin main
# then immediately bring staging in sync:
git checkout staging
git merge main
git push origin staging
```

Document every hotfix in commit message. Avoid hotfixes — they are how regressions get merged later.

---

## Done

If every box above is ticked, you're production-ready. Publish with confidence.
