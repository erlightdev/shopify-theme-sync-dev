# Module 13: Debugging Playbook

> **This is a reference, not a tutorial.** Bookmark it. When something breaks, find the symptom, follow the steps. The last section is a reusable prompt template you can paste into Claude.

---

## Symptom index

1. [Theme won't sync from GitHub](#1-theme-wont-sync-from-github)
2. [Liquid error on storefront](#2-liquid-error-on-storefront)
3. [Section editor is broken or blank](#3-section-editor-is-broken-or-blank)
4. [My CSS class isn't applying](#4-my-css-class-isnt-applying)
5. [Image is blurry, huge, or missing](#5-image-is-blurry-huge-or-missing)
6. [Performance regressed after a deploy](#6-performance-regressed-after-a-deploy)
7. [Theme editor saved over my Git changes](#7-theme-editor-saved-over-my-git-changes)
8. [Metafield is empty in Liquid](#8-metafield-is-empty-in-liquid)
9. [Cart property doesn't persist](#9-cart-property-doesnt-persist)
10. [JS works locally but not on Shopify](#10-js-works-locally-but-not-on-shopify)

---

### 1. Theme won't sync from GitHub

**Check in this order:**

1. **View logs** — Themes → ⋯ → View logs. Look for the last sync attempt. Errors are usually:
   - Liquid syntax error (file path + line number shown)
   - JSON syntax error in a template or schema
   - File >2MB (Shopify rejects)
2. **Push reached GitHub?** `git log origin/staging -1` — confirm the commit is on the remote.
3. **GitHub app permissions** — Shopify admin → Apps → GitHub → reauthorize if recently revoked.
4. **Branch matches?** Verify the theme is connected to the correct branch (theme tile shows the branch name).

---

### 2. Liquid error on storefront

You'll see "Liquid error" in red on the rendered page in dev/preview mode.

**Fix:**

1. Read the message — it includes file + line.
2. Wrap the offending output in `{% if x %}...{% endif %}`.
3. Inspect with `{{ x | json }}` to see what's actually there.
4. Common causes:
   - Calling `.value` on a non-existent metafield
   - Using `product.*` outside a product context
   - Filter on wrong type (`money` on a string, `image_url` on nil)

---

### 3. Section editor is broken or blank

The customizer panel shows nothing or a red error icon next to your section.

**Fix:**

1. Open the section file. Find `{% schema %}`. Validate the JSON inside (paste into https://jsonlint.com).
2. Common causes:
   - Trailing comma in JSON
   - Comments inside `{% schema %}` (illegal)
   - Duplicate setting `id` within the same schema
   - `enabled_on` excludes the current template
3. After fix → push → reload customizer.

---

### 4. My CSS class isn't applying

**Fix:**

1. **Inspect** — is the class actually on the element? If not, your Liquid didn't run.
2. **Tailwind class?** Check:
   - `@source` glob in `src/tailwind.css` covers the file with the class
   - You ran `npm run build` (or `dev` is running)
   - `assets/tailwind.css` is committed and pushed
3. **Specificity?** Dawn's `base.css` may override. Try `!` prefix: `!bg-red-500`.
4. **Cache?** Shopify CDN caches CSS. Hard reload (Ctrl+Shift+R).

---

### 5. Image is blurry, huge, or missing

**Fix:**

| Symptom | Cause | Fix |
|---------|-------|-----|
| Blurry on retina | `width:` too small | Bump to 2x intended display size |
| Page weight huge | No `width:` parameter | Always pass `width: N` to `image_url` |
| 404 broken image | Asset deleted or wrong filter | Use `image_url` not `asset_url` for product images |
| Stretched | Missing `width`/`height` HTML attrs | Pass to `image_tag` |
| Slow LCP | Lazy-loaded above the fold | Use `loading: 'eager'` + `fetchpriority: 'high'` for hero |

---

### 6. Performance regressed after a deploy

**Fix:**

1. `git log --oneline origin/main..origin/staging` — what changed?
2. Run Lighthouse on the previous good commit (use `?preview_theme_id=` with the older theme).
3. Compare LCP, CLS, INP. Look at "Network" tab for new resources.
4. Bisect: `git bisect start; git bisect bad; git bisect good <last-good-sha>`. Re-run Lighthouse at each step.
5. Common culprits: a new third-party script, an unminified Tailwind output, a newly-added section that loads everywhere.

---

### 7. Theme editor saved over my Git changes

The merchant edited a section in admin while you were working on the same file in Git. Their commit landed first.

**Fix:**

1. `git pull --rebase` — see the conflict.
2. Open the file. The editor's changes are marked as `Shopify` author. Decide:
   - Code-side (your new logic): keep your changes
   - Content-side (settings, blocks order): keep theirs
3. `git add file && git rebase --continue && git push`.

**Prevention:** announce when you're working on a customizable file, or use a feature branch and merge atomically.

---

### 8. Metafield is empty in Liquid

**Fix:**

1. `{{ product.metafields | json }}` — does the value exist?
2. If yes but blank in your code:
   - Namespace typo (`pokemon` vs `pokémon`)
   - Key typo
   - Forgot `.value` for list/reference types
3. If no in JSON dump:
   - Definition not saved
   - Definition created for wrong resource (Variants vs Products)
   - Product doesn't have a value (not an error — just unset)

---

### 9. Cart property doesn't persist

**Fix:**

1. Inspect the form — is the input `name="properties[Trainer name]"` exactly?
2. Submit form, then `{{ cart | json }}` somewhere — is the property in the JSON?
3. If the field is rendered outside the `<form>`, it won't submit.
4. Properties starting with `_` are hidden from the customer but still saved.

---

### 10. JS works locally but not on Shopify

**Fix:**

1. Open DevTools console — error?
2. Common: you used `import` syntax. Shopify serves files as-is — no bundler. Either:
   - Avoid ES modules
   - Use `<script type="module">`
3. CSP / mixed content — Shopify enforces HTTPS; any `http://` resource is blocked.
4. `{{ 'foo.js' | asset_url }}` not `assets/foo.js` — the latter is wrong path.

---

## Tools

- **View logs** — Shopify admin theme logs. First place to look for sync errors.
- **`?preview_theme_id=ID`** — preview any theme on the live URL
- **`?view=foo`** — render `templates/<template>.foo.json` if it exists (variant testing)
- **Theme inspector for Chrome** — Shopify's official extension; profiles Liquid render time per section
- **DevTools Network tab** — confirm asset URLs resolve, sizes, cache headers
- **Rich Results Test** — validates structured data
- **axe DevTools** — accessibility scan
- **Lighthouse** — perf, a11y, SEO

---

## The reusable debug prompt

When stuck, paste this into Claude with your specifics filled in:

````prompt
I'm hitting this bug in my Shopify theme:

**Symptom:** <what you see — screenshot or text>
**Expected:** <what should happen>
**Reproducer:** <steps>
**Page:** <URL on staging>
**Branch:** staging
**Last commit that worked:** <sha or "unknown">

What I've tried:
- <thing 1>
- <thing 2>

Please:
1. Tell me the most likely cause given the symptom
2. Show me what to inspect to confirm (specific files, lines, devtools tabs)
3. Propose a fix with file:line refs
4. Don't change code yet — explain first

Reference docs/13-debugging-playbook.md if a known symptom matches.
````
