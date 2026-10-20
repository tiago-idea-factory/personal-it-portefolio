# Implementation plan — v0.1.0
Status: proposed

## Goal
A static, single-page professional portfolio for Tiago, mirroring his LinkedIn profile, optimised for recruiters, ATS systems, search crawlers and bots, and driving visitors to his LinkedIn.

## Features
- [ ] F-001: Portfolio page — hand-written `site/index.html` and CSS with all sections, SEO metadata, Open Graph, JSON-LD Person, `robots.txt`, `sitemap.xml` and placeholder assets
- [ ] F-002: Release check and CV link — `scripts/check.sh` (with `--release` mode) plus the downloadable CV PDF link

## Technical approach
- Plain HTML + CSS in `site/` (the `publish_dir`): no framework, no JavaScript, no cookies, no analytics, system fonts, no third-party requests.
- All content is static HTML so crawlers and ATS see it with JS disabled. Semantic landmarks, one h1, ordered headings.
- Hand-written `site/index.html` with inline metadata and JSON-LD; no content model and no generator in v0.1.0 (the Architect's revised option B).
- Canonical, OG and sitemap URLs share one origin, currently `http://192.168.1.20:8084`, changed by search-and-replace when a public domain exists.
- Unknown content is marked `TODO-PLACEHOLDER` (text) or a visibly marked placeholder image until Tiago supplies the real data.
- `scripts/check.sh` is plain bash (grep/sed): required meta tags; name, jobTitle and LinkedIn URL each present in both the JSON-LD and the visible text; same origin everywhere; page weight under 500 KB excluding the PDF; no external requests; link targets exist. Normal mode only warns on placeholders; `--release` fails on them and fails if the JSON syntax check cannot run. `--release` also warns on obvious phone/address patterns.
- Lighthouse (mobile >= 90) and the LinkedIn Post Inspector are manual steps.
- Done for v0.1.0 means "ready to publish, structurally verified". Link previews and indexing cannot be proven on a LAN-only address.
- Whether `deploy.yml` runs `check.sh --release` is for the Architect/Release Manager to confirm; it is not part of F-001/F-002.

## Out of scope
- Blog/posts, RSS, `content/posts/` (v0.2.0, not pre-committed).
- Content generator, `content/profile.json`, `scripts/build.sh`.
- Print CSS, custom 404 page, extra Twitter tags (only `twitter:card`), Lighthouse CI gate, analytics, cookie banner, multi-language, "blog coming soon" teaser.

## Risks
- Critical path is content, not code: v0.1.0 cannot be released until Tiago supplies the LinkedIn URL, confirmed public contact fields, photo, CV PDF and target keywords.
- No public domain: LinkedIn preview and SEO cannot fully work on 192.168.1.20.
- CV PDF drifting out of sync with the page; Tiago supplies the PDF and only link and file existence are checked.
- Privacy: once public, the page and PDF are permanently indexable; only fields Tiago confirms go in.
- Brittle checks being ignored; keep them few, exact and with clear failure messages.
