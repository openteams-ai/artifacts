# AGENTS.md

This repo publishes standalone HTML pages (plans, assessments, reference material) to GitHub Pages. There is no build step: GitHub Pages serves `main` as-is.

- Site: https://openteams-ai.github.io/artifacts/
- A page at `<slug>/index.html` is served at `https://openteams-ai.github.io/artifacts/<slug>/`
- The site root renders `README.md`, which is the index of pages

**The repo and the site are public.** Never add secrets, credentials, customer names or data, internal hostnames, or anything the user hasn't agreed to publish. If a page's content looks internal-only, ask before pushing.

## Adding a page

1. **Pick a slug.** Short, kebab-case, named for the subject: `github-plane-sync`, not `assessment-2`. It becomes the URL, so don't rename it later. Don't start it with `_` or `.` (Jekyll skips those).
2. **Create `<slug>/index.html`.** One folder per page. Put images or data files the page needs in the same folder and reference them with relative paths (`./chart.png`). Never use root-relative paths like `/chart.png`, because the site lives under `/artifacts/`.
3. **Add a row to the table in `README.md`**, sorted alphabetically by page name. Link the page name to its full GitHub Pages URL (`https://openteams-ai.github.io/artifacts/<slug>/`), not the repo path, so the link opens the rendered page from both GitHub and the site.
4. **Check it locally** (see below).
5. **Commit and push to `main`.**
   - New page: commit directly to `main`.
   - Changing a page someone else added: open a PR instead.
   - Message: `Add <page name> page` or `Update <slug>: <what changed>`.
6. **Confirm it's live** (see below).

## What a page must be

- **A complete HTML document**: `<!doctype html>`, `<html lang="en">`, `<head>` with `<meta charset="utf-8">`, `<meta name="viewport" content="width=device-width, initial-scale=1">` and a `<title>`, then `<body>`.
- **Self-contained.** Inline the page's own CSS and JS. External resources are limited to:
  - Google Fonts stylesheets, with a real fallback font stack
  - Scripts from `cdnjs.cloudflare.com` or `cdn.jsdelivr.net/npm/`, pinned to an exact version
- **No server calls.** No fetches to APIs and no forms that submit anywhere. It's a static site.
- **Readable in light and dark mode.** Define colors as CSS variables on `:root` and redefine them under `@media (prefers-color-scheme: dark)`. Set an explicit `body` background.
- **Works at phone width** (~400px) with no sideways scrolling.
- **If the page uses `localStorage`**, prefix its keys with the slug (`github-plane-sync:answers`), because every page shares one origin. Wrap reads and writes in `try/catch`.

## Converting a claude.ai artifact

Pages published as claude.ai artifacts are fragments: they start at `<title>` / `<style>` and have no `<!doctype>`, `<html>`, `<head>` or `<body>`, because the artifact host adds them. To add one here:

1. Wrap it: put the doctype, `<html>`, `<head>` and the two `<meta>` tags first. Keep the fragment's `<title>`, `<link>` and `<style>` tags in `<head>`, and put the rest in `<body>`.
2. Add the reset the artifact host would have supplied: `<style>body{margin:0}img{max-width:100%}[hidden]{display:none!important}</style>`.
3. Remove any `window.claude.*` calls (runtime capabilities, shared storage, hot reload). They don't exist outside claude.ai. If the page depends on them, tell the user what will stop working.
4. The artifact's `data-theme` light/dark toggle doesn't exist here. Pages following the pattern above still follow the system theme.

## Checking locally

```sh
python3 -m http.server 8000   # from the repo root
# open http://localhost:8000/<slug>/
```

Check the page in both light and dark mode, at phone width, and with the browser console open (no errors, no blocked requests).

## Confirming it's live

After pushing, Pages rebuilds in about a minute:

```sh
gh api repos/openteams-ai/artifacts/pages/builds/latest --jq '{status, error: .error.message}'
# wait for "built", then:
curl -s -o /dev/null -w "%{http_code}\n" https://openteams-ai.github.io/artifacts/<slug>/
```

A `200` means it's live. Give the user the page URL, not the repo URL.

## Removing a page

Delete the folder, remove its README row, and push. Old links will return 404. Git history keeps the content, so if it was removed because it shouldn't have been public, tell the user that it's still in history and may already be cached or indexed.
