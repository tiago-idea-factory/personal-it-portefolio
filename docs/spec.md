# Spec

The approved specification. Nothing is built until Tiago approves the spec for a version.

## Goal

A static professional portfolio for Tiago that mirrors his LinkedIn, so recruiters, ATS systems, search crawlers and bots can find and shortlist him, and visitors are sent to his LinkedIn profile (no CV download). Target v0.1.0, hosted initially on GitHub Pages. Done for v0.1.0 means "published on GitHub Pages and the public checks (LinkedIn Post Inspector, indexing) done by hand".

## Feature registry

One row per feature, never deleted. A removed feature keeps its row and gets a "Removed in" version.

| ID | Feature | Added in | Removed in | PR | Notes |
|----|---------|----------|------------|----|-------|
| F-001 | Portfolio page | v0.1.0 | — | #1 | Hand-written HTML/CSS, LinkedIn button, SEO, Open Graph, JSON-LD, relative paths |
| F-002 | Release check | v0.1.0 | — | | `scripts/check.sh` with `--release <tag>` |
| F-003 | GitHub Pages publishing | v0.1.0 | — | | `.github/workflows/pages.yml`; `deploy.yml` unchanged |

## Features

### F-001 — Portfolio page
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** Single static page `site/index.html` with `assets/style.css`, `robots.txt`, `sitemap.xml` and a placeholder photo and 1200x630 `og-image.png`. Sections: header (name, headline, LinkedIn button), summary, experience (newest first), skills, education, certifications, contact. English only. No JS, cookies or analytics. No CV link. LinkedIn is the only public contact (no `mailto:`, no form) unless Tiago explicitly chooses an email. All internal references are relative, so the same files work at a host root and under the GitHub Pages sub-path.
- **Acceptance criteria:**
  - [ ] A primary LinkedIn button is above the fold at 375px, repeated in the header and the contact section.
  - [ ] The LinkedIn URL is one identical string in the header button, the contact section and JSON-LD `sameAs`; links use `rel="me noopener"` and work without JavaScript.
  - [ ] Summary, experience (company, title, dates, 2-4 achievements per role, newest first), skills, education and certifications are complete in static HTML; the LinkedIn button is a call to action, not a substitute for content.
  - [ ] One h1, ordered headings, semantic landmarks.
  - [ ] Title, meta description, canonical, `og:title/description/image/url` and `twitter:card` are present; the OG image is 1200x630.
  - [ ] Valid JSON-LD Person (name, jobTitle, sameAs LinkedIn, knowsAbout, image, url) matching the visible text.
  - [ ] `<meta name="version" content="0.1.0">` is present.
  - [ ] Visible text, title, meta and JSON-LD spell "portfolio" correctly (the repo slug is not renamed).
  - [ ] Canonical, `og:url`, `og:image`, JSON-LD `url` and `sitemap.xml` all use `https://tiago-idea-factory.github.io/personal-it-portefolio/`; `robots.txt` and a one-URL `sitemap.xml` are present.
  - [ ] All `src`/`href` of internal files are relative (no leading `/`); the page renders correctly from a plain static server under a sub-path (manual review).
  - [ ] No horizontal scroll at 320px, WCAG AA contrast, page weight under 500 KB, no request to another origin.
  - [ ] Unknown content is marked `TODO-PLACEHOLDER` or a visibly marked placeholder image.
  - [ ] Manual (before release): Lighthouse mobile >= 90 in Performance, Accessibility, SEO and Best Practices; Tiago confirms every public contact field.

### F-002 — Release check
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** `scripts/check.sh` (bash, grep/sed plus `jq` or `python3`), portable to `ubuntu-latest`, with a `--release <tag>` mode. It is run by `pages.yml` (F-003) and locally as a pre-flight on the release PR.
- **Acceptance criteria:**
  - [ ] Verifies required meta tags; name, jobTitle and LinkedIn URL identical in JSON-LD and visible text (the failure names the differing field); structure (one h1, ordered headings, non-empty experience/skills/education); one origin and path prefix for all absolute URLs; page weight under 500 KB; no request to another origin (only img, stylesheet and script count, not `<a href>`); local link targets exist.
  - [ ] Fails on any `src`/`href` starting with a single `/`.
  - [ ] Normal mode warns on `TODO-PLACEHOLDER`; `--release` fails on it.
  - [ ] `--release <tag>` fails if `<meta name="version">` differs from the tag.
  - [ ] In `--release`, JSON-LD syntax is validated and a missing JSON parser is a failure, not a skip.
  - [ ] In `--release`, the LinkedIn URL must have a host ending in `linkedin.com` and a path starting `/in/` (trailing slash and encoded slugs accepted); the placeholder fails.
  - [ ] Phone/address patterns in the page produce a warning only.
  - [ ] Exit code is non-zero on failure and messages say what to fix.
  - [ ] Release checklist: `check.sh --release <next-tag>` is run locally on the release PR before tagging, since a tag cannot be reused.

### F-003 — GitHub Pages publishing
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** A separate workflow `.github/workflows/pages.yml` publishes `site/` to GitHub Pages only after `scripts/check.sh --release` passes. `deploy.yml` (Caddy, LAN preview) is not edited and has no gate.
- **Acceptance criteria:**
  - [ ] Triggers on `release: published` and on `workflow_dispatch` with a tag input; never on push to `main`.
  - [ ] The tag is validated against `^v[0-9]+\.[0-9]+\.[0-9]+$` in both triggers, passed through `env:` and never interpolated into `run:`.
  - [ ] Checks out `refs/tags/<tag>`, runs `scripts/check.sh --release <tag>`, then `actions/configure-pages`, `actions/upload-pages-artifact` (`path: site`) and `actions/deploy-pages`, with `pages: write`, `id-token: write`, the `github-pages` environment and a `concurrency` group.
  - [ ] After a release the Pages URL returns 200 over HTTPS and shows the released version (checked by hand).
  - [ ] The PR states that the release-event trigger and the environment accepting a tag ref are "not verified" until the first real release; `workflow_dispatch` is the documented fallback.
  - [ ] The PR states that a green Caddy run next to a red `pages.yml` run before the content exists is expected, not a defect.
  - [ ] Manual, by Tiago, recorded in the PR: repository is public (informed yes: the whole repo, including brief, debate, `CLAUDE.md` and `deploy.yml`, becomes public); Pages enabled with source "GitHub Actions"; the `github-pages` environment allows the release tag.
  - [ ] Manual, by Tiago, before the first LinkedIn share: accepts the Pages URL as stable (it is temporary until then), then adds it to his LinkedIn profile (contact info website and Featured section); re-run Post Inspector after each release.

## Out of scope

- CV PDF or CV link — removed from v0.1.0 by Tiago's feedback (Round 1): link to LinkedIn only.
- Blog/posts and RSS — postponed to v0.2.0 (Round 1); the v0.2.0 design is not pre-committed.
- Content generator and `content/profile.json` — cut in v0.1.0 (Round 1): one page does not need it.
- Separate `<user>.github.io` repo, custom domain, `CNAME`, custom 404, sitemap-submission automation, Post Inspector from CI — cut in v0.1.0 (Round 1); a later change of base URL plus a re-release.
- Editing `deploy.yml` or gating the Caddy deploy — cut in v0.1.0 (Round 1): factory infrastructure; `pages.yml` is the public gate.
- Print CSS, extra Twitter tags, Lighthouse CI gate, analytics, multi-language, contact form, public email by default, "blog coming soon" teaser, renaming the repo slug — cut in v0.1.0 (Round 1).
