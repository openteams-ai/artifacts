# AGENTS.md

This repo publishes standalone HTML pages (plans, assessments, reference material) to GitHub Pages. There is no build step: GitHub Pages serves `main` as-is.

- Site: https://openteams-ai.github.io/artifacts/
- A page at `<slug>/index.html` is served at `https://openteams-ai.github.io/artifacts/<slug>/`
- The site root renders `README.md`, which is the index of pages. Its header title and description come from `_config.yml`.

**The repo and the site are public.** Never add secrets, credentials, customer names or data, internal hostnames, or anything the user hasn't agreed to publish. Run the [public-content check](#public-content-check) on every page before it goes in. If a page's content looks internal-only, ask before pushing.

**Every page must have a row in `README.md`.** The README is the site's index, so a page without a row is unlisted. Run the [README check](#readme-check) before every commit, not just when adding a page.

## Adding a page

1. **Pick a slug.** Short, kebab-case, named for the subject: `github-plane-sync`, not `assessment-2`. It becomes the URL, so don't rename it later. Don't start it with `_` or `.` (Jekyll skips those).
2. **Create `<slug>/index.html`.** One folder per page. Put images or data files the page needs in the same folder and reference them with relative paths (`./chart.png`). Never use root-relative paths like `/chart.png`, because the site lives under `/artifacts/`.
3. **Add a row to the table in `README.md`** in the same commit as the page. New rows go at the top of the table, so it reads newest first. Link the page name to its full GitHub Pages URL (`https://openteams-ai.github.io/artifacts/<slug>/`), not the repo path, so the link opens the rendered page from both GitHub and the site. The second column is one sentence on what the page covers, written from the page's own content.
4. **Run the public-content check** (see below). Do this even for a page the user wrote themselves.
5. **Check it locally** (see below).
6. **Run the README check** (see below). It also catches pages other people added without a row.
7. **Commit and push to `main`.**
   - New page: commit directly to `main`, with its README row in the same commit.
   - Changing a page someone else added: open a PR instead.
   - Adding a missing README row for someone else's page: commit directly to `main`. It's an index fix, not a change to their page.
   - Message: `Add <page name> page` or `Update <slug>: <what changed>`.
8. **Confirm it's live** (see below).

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

## Public-content check

Everything pushed here is public the moment Pages builds, and git history keeps it forever. Before adding or changing a page, read the whole page (not just the parts you wrote) and confirm none of the following is in it:

- **Secrets and credentials**: API keys, tokens, passwords, private keys, connection strings, signed URLs, cookies.
- **Internal infrastructure**: internal hostnames or domains, private IP addresses, cluster or namespace names, internal dashboards, VPN-only URLs.
- **Customers, prospects and partners**: company names, contact names, logos, contract or pricing terms, usage numbers, anything from a customer's own systems.
- **People**: email addresses, phone numbers, employee names attached to opinions, performance or HR material. Authorship of the page is fine.
- **Internal conversations and links**: pasted Slack or meeting notes, links to private repos, Jira/Plane/Notion/Google Docs that aren't public.
- **Unannounced plans**: roadmap, launch dates, deals, financials, anything the user would not say in a public blog post.
- **Screenshots and data files** in the page folder: check them too, they are published alongside the page.

Illustrative placeholders are fine: `example.com`, `my-platform`, `data.lab.internal` in a sample config, fake names that are obviously fake. The test is whether the value points at something real.

This grep flags the patterns that can be searched for. Review every hit; a JavaScript variable called `token` is not a leak, a real key is:

```sh
grep -rniE 'password|passwd|secret|api[_-]?key|bearer |BEGIN [A-Z ]*PRIVATE KEY|AKIA[0-9A-Z]{16}|ghp_[A-Za-z0-9]{20,}|sk-[A-Za-z0-9]{20,}|xox[abp]-|[a-z0-9._-]+@[a-z0-9-]+\.[a-z]+|\.(internal|corp|lan)\b|\b(10|172\.(1[6-9]|2[0-9]|3[01])|192\.168)\.[0-9]+\.[0-9]+\b|slack\.com/archives|atlassian\.net|notion\.so|docs\.google\.com|github\.com/[^/"]+/[^/"]+' --include='*.html' --include='*.md' --include='*.json' --include='*.csv' --include='*.js' --include='*.css' .
```

If anything looks internal-only and the user hasn't explicitly said it can be public, stop and ask before committing. Once it's pushed, removing it doesn't unpublish it (see "Removing a page").

## README check

Every `<slug>/` folder needs a row in `README.md`, and every row needs a folder. Run this from the repo root before each commit:

```sh
for d in */; do s=${d%/}; grep -q "artifacts/$s/" README.md || echo "MISSING README ROW: $s"; done
grep -oE 'artifacts/[a-z0-9-]+/' README.md | sed 's#artifacts/##;s#/##' | sort -u | while read s; do [ -d "$s" ] || echo "README ROW WITHOUT FOLDER: $s"; done
```

No output means the index is complete. If a page someone else added is missing, add its row (describe it from the page's `<title>` and content) and commit that directly to `main`. Also check that the newest page is at the top of the table and that each link is the full `https://openteams-ai.github.io/artifacts/<slug>/` URL.

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
