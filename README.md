# OpenTeams Artifacts

Standalone HTML pages: plans, assessments and reference material, published at **https://openteams-ai.github.io/artifacts/**.

Each page lives in its own folder as a single `index.html` with no build step.

> **Public information only.** This repository and the site it publishes are public, and git history keeps everything ever pushed. Only add content that is already public or that OpenTeams has agreed to publish. Never include secrets or credentials, internal hostnames or infrastructure details, customer or partner names and data, personal contact information, internal conversations or links, or unannounced plans. If in doubt, don't push it. [`AGENTS.md`](AGENTS.md#public-content-check) has the checklist.

## Pages

| Page | What it covers |
|---|---|
| [Adopting Nebari Design](https://openteams-ai.github.io/artifacts/nebari-design-system/) | For engineers adopting the Nebari design system: the registry-to-call-site pipeline, setup steps, an interactive catalog of what each install pulls in, the token contract shown live in both themes, composition rules, the shared app header recipe, motion tokens, and the ecosystem around the registry. |
| [How Nebari works](https://openteams-ai.github.io/artifacts/nebari-explainer/) | Interactive explainer: pick where Nebari runs (cloud, GovCloud, Hetzner, on-prem), then choose software packs and watch Argo CD wire them in from the GitOps repo. |
| [GitHub–Plane multi-repo sync](https://openteams-ai.github.io/artifacts/github-plane-sync/) | Assessment, design, scope-based estimate, hosting options and runbook for syncing multiple GitHub repos to one Plane project. |

## Adding a page

1. Create `<slug>/index.html`: a complete, self-contained HTML document.
2. Add a row to the table above, linking to the page's GitHub Pages URL.
3. Push to `main`. GitHub Pages publishes it at `https://openteams-ai.github.io/artifacts/<slug>/` in about a minute.

[`AGENTS.md`](AGENTS.md) has the full conventions: page requirements, converting a claude.ai artifact, checking locally, and confirming it's live. Coding agents read it automatically.
