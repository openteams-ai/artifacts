# OpenTeams Artifacts

Standalone HTML pages: plans, assessments and reference material, published at **https://openteams-ai.github.io/artifacts/**.

Each page lives in its own folder as a single `index.html` with no build step. This repo and the site are public, so don't add anything internal-only.

## Pages

| Page | What it covers |
|---|---|
| [GitHub–Plane multi-repo sync](https://openteams-ai.github.io/artifacts/github-plane-sync/) | Assessment, design, scope-based estimate, hosting options and runbook for syncing multiple GitHub repos to one Plane project. |
| [How Nebari works](https://openteams-ai.github.io/artifacts/nebari-explainer/) | Interactive explainer: pick where Nebari runs (cloud, GovCloud, Hetzner, on-prem), then choose software packs and watch Argo CD wire them in from the GitOps repo. |

## Adding a page

1. Create `<slug>/index.html`: a complete, self-contained HTML document.
2. Add a row to the table above, linking to the page's GitHub Pages URL.
3. Push to `main`. GitHub Pages publishes it at `https://openteams-ai.github.io/artifacts/<slug>/` in about a minute.

[`AGENTS.md`](AGENTS.md) has the full conventions: page requirements, converting a claude.ai artifact, checking locally, and confirming it's live. Coding agents read it automatically.
