---
name: publish-artifact
description: Publish a standalone HTML page (a local file or a claude.ai artifact link) to the OpenTeams artifacts site at https://openteams-ai.github.io/artifacts/ without needing a clone of the repo. Use when asked to "publish this page", "put this on the artifacts site", "add this artifact to openteams-ai/artifacts", or /publish-artifact <file-or-url> [slug].
---

# Publish a page to the OpenTeams artifacts site

The site is the `main` branch of `openteams-ai/artifacts`, served as-is by GitHub Pages. `AGENTS.md` in that repo is the single source of truth for what a page must be and how it gets added. This skill gets you a fresh checkout, then you follow `AGENTS.md` from there. Do not rely on a memory of its rules: read it from the checkout every time, because it changes.

## Inputs

- **Page**: a path to an `.html` file, or a claude.ai artifact URL (`claude.ai/artifact/...` or `claude.ai/code/artifact/...`). Read an artifact URL with the Artifact tool's `read` action, never with a web fetch. Artifact pages are fragments and need wrapping (see "Converting a claude.ai artifact" in `AGENTS.md`).
- **Slug** (optional): the URL path segment. If not given, derive one from the page's `<title>` (short, kebab-case, named for the subject). Confirm the slug with the user before pushing, because it can't be renamed later.
- Any images or data files the page references, which go in the same folder.

## Steps

1. **Check access.** Run `gh auth status`. If not logged in, stop and tell the user to run `gh auth login`. Confirm write access with `gh repo view openteams-ai/artifacts --json viewerPermission --jq .viewerPermission` (need `WRITE`, `MAINTAIN` or `ADMIN`). Without it, stop and say who to ask.

2. **Get a checkout.**
   - If the current directory is already a clone of `openteams-ai/artifacts`, use it: `git checkout main && git pull --ff-only`. Refuse to continue if the working tree is dirty.
   - Otherwise shallow-clone into the scratchpad directory (or a temp dir if there is none) and work there:
     ```sh
     gh repo clone openteams-ai/artifacts "$DIR" -- --depth 1
     ```
   Never touch the user's other repos.

3. **Read `AGENTS.md` in the checkout** and follow its "Adding a page" section end to end. In particular:
   - Create `<slug>/index.html` as a complete, self-contained document. Wrap an artifact fragment as described there, and remove `window.claude.*` calls, telling the user what stops working.
   - Add the README row at the top of the table, linking the full `https://openteams-ai.github.io/artifacts/<slug>/` URL, with a one-sentence description written from the page's content.
   - Run the **public-content check** grep from `AGENTS.md` over the whole checkout and read every hit. If anything looks internal-only (customer names, internal hosts, private links, unannounced plans) and the user hasn't explicitly said it can be public, stop and show them the hits before committing. Pushing is irreversible: the repo is public and history is permanent.
   - Run the **README check** loop from `AGENTS.md`. It must print nothing.
   - If a browser tool is available, check the page locally in light and dark mode and at phone width as `AGENTS.md` describes. Otherwise say that this was skipped.

4. **Commit and push.** Commit the page and its README row together with the message `Add <page name> page`, then `git push origin main`. If the push is rejected by branch protection, push to a branch named `add-<slug>` instead and open a PR with `gh pr create`, then give the user the PR URL.

5. **Confirm it's live** using the commands in `AGENTS.md` ("Confirming it's live"). Wait for the Pages build to report `built`, then check that the page URL returns `200`. Pages usually builds within a minute or two.

6. **Clean up.** Delete the throwaway clone if you made one. Give the user the page URL (`https://openteams-ai.github.io/artifacts/<slug>/`), not the repo URL.

## Updating an existing page

Same flow, but if the page was added by someone else, `AGENTS.md` says to open a PR rather than push to `main`. Check `git log --format=%an -- <slug>/` to see who added it.
