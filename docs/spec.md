# Spec

The approved specification. Nothing is built until Tiago approves the spec for a version.

## Goal

A static professional portfolio for Tiago that mirrors his LinkedIn, so recruiters, ATS systems, search crawlers and bots can find and shortlist him, and visitors are sent to his LinkedIn profile (no CV download). Target v0.1.0. Done for v0.1.0 means "ready to publish, structurally verified"; LinkedIn preview and indexing need a public domain and cannot be proven on the LAN address.

## Feature registry

One row per feature, never deleted. A removed feature keeps its row and gets a "Removed in" version.

| ID | Feature | Added in | Removed in | PR | Notes |
|----|---------|----------|------------|----|-------|
| F-001 | Portfolio page | v0.1.0 | — | | Hand-written HTML/CSS, LinkedIn button, SEO, Open Graph, JSON-LD |
| F-002 | Release check | v0.1.0 | — | | `scripts/check.sh` and its `deploy.yml` step |

## Features

### F-001 — Portfolio page
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** Single static page `site/index.html` with `assets/style.css`, `robots.txt`, `sitemap.xml` and a placeholder photo and 1200x630 `og-image.png`. Sections: header (name, headline, LinkedIn button), summary, experience (newest first), skills, education, certifications, contact. English only. No JS, cookies or analytics. No CV link. LinkedIn is the only public contact (no `mailto:`, no form) unless Tiago explicitly chooses an email.
- **Acceptance criteria:**
  - [ ] A primary LinkedIn button is above the fold at 375px, repeated in the header and the contact section.
  - [ ] The LinkedIn URL is one identical string in the header button, the contact section and JSON-LD `sameAs`; links use `rel="me noopener"` and work without JavaScript.
  - [ ] Summary, experience (company, title, dates, 2-4 achievements per role, newest first), skills, education and certifications are complete in static HTML; the LinkedIn button is a call to action, not a substitute for content.
  - [ ] One h1, ordered headings, semantic landmarks.
  - [ ] Title, meta description, canonical, `og:title/description/image/url` and `twitter:card` are present; the OG image is 1200x630.
  - [ ] Valid JSON-LD Person (name, jobTitle, sameAs LinkedIn, knowsAbout, image) matching the visible text.
  - [ ] `robots.txt` and a one-URL `sitemap.xml` use the same origin as canonical/OG.
  - [ ] No horizontal scroll at 320px, WCAG AA contrast, page weight under 500 KB, no request to another origin.
  - [ ] Unknown content is marked `TODO-PLACEHOLDER` or a visibly marked placeholder image.
  - [ ] Manual (before release): Lighthouse mobile >= 90 in Performance, Accessibility, SEO and Best Practices; Tiago confirms every public contact field.

### F-002 — Release check
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** `scripts/check.sh` (bash, grep/sed) with a `--release` mode, run by a step in `.github/workflows/deploy.yml` before deploy, so a release with placeholders cannot go live.
- **Acceptance criteria:**
  - [ ] `check.sh` verifies required meta tags; name, jobTitle and LinkedIn URL identical in JSON-LD and visible text (the failure names the differing field); structure (one h1, ordered headings, non-empty experience/skills/education); one origin everywhere; page weight under 500 KB; no request to another origin (only img, stylesheet and script count, not `<a href>`); local link targets exist.
  - [ ] Normal mode warns on `TODO-PLACEHOLDER`; `--release` fails on it.
  - [ ] In `--release`, JSON-LD syntax is validated and a missing JSON parser is a failure, not a skip.
  - [ ] In `--release`, the LinkedIn URL must have a host ending in `linkedin.com` and a path starting `/in/` (trailing slash and encoded slugs accepted); the placeholder fails.
  - [ ] Phone/address patterns in the page produce a warning only.
  - [ ] Exit code is non-zero on failure and messages say what to fix.
  - [ ] `deploy.yml` has a step that runs `scripts/check.sh --release` before the deploy step (verified by the Reviewer).
  - [ ] Manual (post-release, not a gate): Tiago adds the site URL to his LinkedIn profile (contact info website and Featured section).

## Out of scope

- CV PDF or CV link — removed from v0.1.0 by Tiago's feedback (Round 1): link to LinkedIn only.
- Blog/posts and RSS — postponed to v0.2.0 (Round 1); the v0.2.0 design is not pre-committed.
- Content generator and `content/profile.json` — cut in v0.1.0 (Round 1): one page does not need it.
- Print CSS, custom 404, Lighthouse CI gate, extra Twitter tags, analytics, multi-language, contact form, public email by default, "blog coming soon" teaser — cut in v0.1.0 (Round 1).
