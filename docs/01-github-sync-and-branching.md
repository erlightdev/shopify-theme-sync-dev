# Module 01: GitHub Sync & Branching

## What you'll learn
- The two-way sync model between Shopify and GitHub
- A safe staging → main branching workflow
- How to resolve sync conflicts

## Why it matters
Shopify's theme editor commits back to GitHub. If you don't have a discipline, the editor will silently overwrite your code, or your push will overwrite a merchant's content edits. A branching strategy keeps both worlds honest.

## Concepts

### Two-way sync

Each connected theme is bound to **one branch**:
- Push to branch → Shopify pulls within ~30s
- Edit in theme editor → Shopify commits to that branch (author: Shopify)

### Our branching strategy

```
main      → Live theme (published).  Auto-syncs to production storefront.
staging   → Draft theme (preview).   Safe sandbox for code changes.
```

**Daily flow:**
```
1. git checkout staging
2. <make changes>
3. git add . && git commit -m "..."
4. git push                    ← Shopify staging theme updates
5. Preview in admin → looks good?
6. git checkout main && git merge staging && git push
7. git checkout staging
```

### Files the editor will rewrite

Expect commits from `Shopify` author on:
- `templates/*.json` — any section reorder, settings, or block edit
- `config/settings_data.json` — theme settings panel
- `sections/header-group.json`, `footer-group.json` — header/footer customizer

Treat these as **content**. Don't hand-edit them on a branch where the merchant is also editing.

### Conflict resolution

If both Git and the editor changed `index.json`:

```bash
git pull --rebase
# Conflict marker in templates/index.json
# → Open it, decide which side wins (usually editor wins for content; Git wins for new sections you added)
git add templates/index.json
git rebase --continue
git push
```

## Prompt-driven build

```prompt
Show me the last 10 commits to this repo with author names. Highlight any commits made by "Shopify" (the GitHub app) so I can see how often the editor writes back.
```

```prompt
I'm about to make a code change. Write me a one-line bash command that:
1. Verifies I'm on staging
2. Pulls latest with rebase
3. Aborts safely if there are uncommitted changes
```

## Debug callouts

- **"My push was rejected"** → editor committed since your last pull. `git pull --rebase` and re-push.
- **"Theme isn't updating after push"** → check **Themes → ⋯ → View logs** for the connected theme. Look for sync errors (often a Liquid syntax error stops sync).
- **"I committed to the wrong branch"** → `git log` to find the SHA, `git checkout staging`, `git cherry-pick <sha>`, then `git checkout main && git reset --hard origin/main` (only if not yet pushed to main).

## Acceptance checklist
- [ ] You can describe the daily flow from memory
- [ ] You've checked View logs for the staging theme at least once
- [ ] You understand what files the editor will silently rewrite

## Further reading
- https://shopify.dev/docs/storefronts/themes/tools/github
- https://shopify.dev/docs/storefronts/themes/tools/github/troubleshooting
