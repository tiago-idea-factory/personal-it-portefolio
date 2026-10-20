# Decisions

Decisions taken while building this idea. Newest on top. Every entry states the version it applies to.

<!--
## YYYY-MM-DD — v0.1.0 — Decision title
- **Context:** …
- **Options considered:** …
- **Decision:** …
- **Why:** …
- **Related:** F-00X, debate round N, PR #N
-->

## 2026-10-20 — v0.1.0 — Hand-written static site, no build step
- **Context:** Round table for v0.1.0; the page is one portfolio page, published on GitHub Pages and previewed on the Caddy LAN address.
- **Options considered:** (A) generator from `content/profile.json` via `scripts/build.sh`; (B) hand-written `site/index.html` plus `scripts/check.sh`.
- **Decision:** Option B. Plain HTML + CSS in `site/`, no JavaScript, relative paths only, one absolute origin (the Pages URL) for canonical/OG/JSON-LD/sitemap. `scripts/check.sh` guards consistency and the release gate; `pages.yml` publishes. `deploy.yml` stays unchanged. A generator is reconsidered only if v0.2.0 is approved.
- **Why:** A generator is machinery for one page whose content rarely changes and adds generated diffs to every PR; checks give the same consistency guarantee.
- **Related:** F-001, F-002, F-003, debate round 1, docs/plan.md
