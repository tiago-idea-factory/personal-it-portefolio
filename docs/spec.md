# Spec

The approved specification. Nothing is built until Tiago approves the spec for a version.

## Goal

A static professional portfolio for Tiago that mirrors his LinkedIn, so recruiters, ATS systems, search crawlers and bots can find and shortlist him, and visitors are sent to his LinkedIn. Target v0.1.0. Done for v0.1.0 means "ready to publish, structurally verified"; LinkedIn preview and indexing need a public domain and cannot be proven on the LAN address.

## Feature registry

One row per feature, never deleted. A removed feature keeps its row and gets a "Removed in" version.

| ID | Feature | Added in | Removed in | PR | Notes |
|----|---------|----------|------------|----|-------|
| F-001 | Portfolio page | v0.1.0 | — | | Hand-written HTML/CSS, SEO, Open Graph, JSON-LD |
| F-002 | Release check and CV link | v0.1.0 | — | | `scripts/check.sh`, CV PDF link |

## Features

### F-001 — Portfolio page
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** Single static page `site/index.html` with `assets/style.css`, `robots.txt`, `sitemap.xml` and a placeholder photo and 1200x630 `og-image.png`. Sections: header (name, headline, LinkedIn button), summary, experience (newest first), skills, education, certifications, contact. English only. No JS, cookies or analytics.
- **Acceptance criteria:**
  - [ ] Above the fold at 375px shows name, headline, short summary and a LinkedIn button.
  - [ ] Each role lists company, title, dates and 2-4 achievements, newest first.
  - [ ] The LinkedIn link in the header and contact section uses `rel="me noopener"`; the contact option is visible without scripts.
  - [ ] All content is in static HTML; one h1, ordered headings, semantic landmarks.
  - [ ] Title, meta description, canonical, `og:title/description/image/url` and `twitter:card` are present; the OG image is 1200x630.
  - [ ] Valid JSON-LD Person (name, jobTitle, sameAs LinkedIn, knowsAbout) matching the visible text.
  - [ ] `robots.txt` and a one-URL `sitemap.xml` use the same origin as canonical/OG.
  - [ ] No horizontal scroll at 320px, WCAG AA contrast, page weight under 500 KB excluding the PDF, no third-party requests.
  - [ ] Unknown content is marked `TODO-PLACEHOLDER` or a visibly marked placeholder image.
  - [ ] Manual (before release): Lighthouse mobile >= 90 in Performance, Accessibility, SEO and Best Practices.

### F-002 — Release check and CV link
- **Added in:** v0.1.0
- **Debate:** [Round 1](debate.md#round-1--target-v010--2026-10-20)
- **Description:** `scripts/check.sh` (bash, grep/sed) and a downloadable CV PDF link showing the file size. The PDF is supplied by Tiago.
- **Acceptance criteria:**
  - [ ] The CV link works, shows the file size, and the PDF exists in `site/cv/`.
  - [ ] `check.sh` verifies required meta tags; name, jobTitle and LinkedIn URL appear in both JSON-LD and visible text (the failure names the differing field); one origin everywhere; page weight; no external requests; link targets exist.
  - [ ] Normal mode warns on `TODO-PLACEHOLDER`; `--release` fails on it.
  - [ ] JSON-LD syntax is validated; in `--release` mode a missing JSON parser is a failure, not a skip.
  - [ ] `--release` warns on obvious phone/address patterns in the page.
  - [ ] Exit code is non-zero on failure and messages say what to fix.
  - [ ] Manual (before release): Tiago confirms every public contact field, and that the PDF has no phone or home address unless he chose to include it.

## Out of scope

- Blog/posts and RSS — postponed to v0.2.0 (Round 1); the v0.2.0 design is not pre-committed.
- Content generator and `content/profile.json` — cut in v0.1.0 (Round 1): one page does not need it.
- Print CSS, custom 404, Lighthouse CI gate, extra Twitter tags, analytics, multi-language, "blog coming soon" teaser — cut in v0.1.0 (Round 1).
