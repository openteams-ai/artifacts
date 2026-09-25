# OpenTeams Artifacts

Standalone HTML pages: plans, assessments and reference material, published at **https://openteams-ai.github.io/artifacts/**.

Each page lives in its own folder as a single `index.html` with no build step.

> **Public information only.** This repository and the site it publishes are public, and git history keeps everything ever pushed. Only add content that is already public or that OpenTeams has agreed to publish. Never include secrets or credentials, internal hostnames or infrastructure details, customer or partner names and data, personal contact information, internal conversations or links, or unannounced plans. If in doubt, don't push it. [`AGENTS.md`](AGENTS.md#public-content-check) has the checklist.

## Pages

| Page | What it covers |
|---|---|
| [Jev vs. LLMs: one writes, one decides](https://openteams-ai.github.io/artifacts/jev-vs-llm/) | Interactive comparison of a typical LLM and TypeSafe AI's Jev on a support-ticket triage task: a simulated side-by-side run, the parse/validate/retry layer Jev removes, confidence-threshold routing, a cost calculator, and a feature table. |
| [WebGPU](https://openteams-ai.github.io/artifacts/webgpu-talk/) | Interactive 30-minute UI/UX CoP talk on WebGPU: what it is, the object graph, compute, hardware, and demos that run on the viewer's own GPU, with speaker notes. |
| [Adopting Nebari Design (deck)](https://openteams-ai.github.io/artifacts/nebari-design-deck/) | Interactive UI/UX CoP deck version of the Nebari design explainer: pipeline, setup, catalog explorer, live token contract, header recipe, motion, with speaker notes. |
| [How Nebari works](https://openteams-ai.github.io/artifacts/nebari-explainer/) | Interactive explainer: pick where Nebari runs (cloud, GovCloud, Hetzner, on-prem), then choose software packs and watch Argo CD wire them in from the GitOps repo. |
| [GitHub–Plane multi-repo sync](https://openteams-ai.github.io/artifacts/github-plane-sync/) | Assessment, design, scope-based estimate, hosting options and runbook for syncing multiple GitHub repos to one Plane project. |

## Adding a page

The easiest way is the `publish-artifact` skill, which lives in this repo at [`.claude/skills/publish-artifact/`](https://github.com/openteams-ai/artifacts/blob/main/.claude/skills/publish-artifact/SKILL.md). It works from any directory: it makes a throwaway checkout, wraps the page, adds the README row, runs the public-content check, pushes, and reports the live URL. You need `gh` logged in with write access to this repo.

Install it once for Claude Code, Cursor, Codex and similar tools:

```sh
npx skills add openteams-ai/artifacts -g
```

Then, in any session:

```
/publish-artifact path/to/page.html my-slug
/publish-artifact https://claude.ai/artifact/... my-slug
```

If you use the skills-sync plugin, add this source instead and it stays up to date on its own:

```json
{ "repo": "openteams-ai/artifacts", "path": ".claude/skills", "skills": "*" }
```

Doing it by hand:

1. Create `<slug>/index.html`: a complete, self-contained HTML document.
2. Add a row to the table above, linking to the page's GitHub Pages URL.
3. Push to `main`. GitHub Pages publishes it at `https://openteams-ai.github.io/artifacts/<slug>/` in about a minute.

[`AGENTS.md`](AGENTS.md) has the full conventions: page requirements, converting a claude.ai artifact, checking locally, and confirming it's live. Coding agents read it automatically, and the skill follows it too.
