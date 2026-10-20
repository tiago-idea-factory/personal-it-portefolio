# Implementation plan — v0.1.0
Status: proposed

## Goal
A static, single-page professional portfolio for Tiago, mirroring his LinkedIn profile, optimised for recruiters, ATS systems, search crawlers and bots, and sending visitors to his LinkedIn profile (no CV download).

## Features
- [ ] F-001: Portfolio page — hand-written `site/index.html` and CSS with the full record, a LinkedIn button, SEO metadata, Open Graph, JSON-LD Person, `robots.txt`, `sitemap.xml` and placeholder assets
- [ ] F-002: Release check — `scripts/check.sh` (with `--release` mode) wired as a step in `deploy.yml` so a release with placeholders cannot go live

## Technical approach
- Plain HTML + CSS in `site/` (the `publish_dir`): no framework, no JavaScript, no cookies, no analytics, system fonts, no third-party requests.
- Bots cannot read LinkedIn behind its login wall, so the page carries the full record in static HTML (summary, roles with company, title, dates and 2-4 achievements, skills, education, certifications). The LinkedIn button is the call to action, not a replacement for content.
- Semantic landmarks, one h1, ordered headings. Hand-written `site/index.html` with inline metadata and JSON-LD; no content model and no generator in v0.1.0.
- LinkedIn is the only public contact: no `mailto:`, no form, no email unless Tiago explicitly chooses one (then plain text, his decision).
- One LinkedIn URL string in exactly three places: header button, contact section and JSON-LD `sameAs`. Links carry `rel="me noopener"` (no `noreferrer`).
- JSON-LD Person: name, jobTitle, `sameAs`, `knowsAbout` (Tiago's keywords), `image`.
- Canonical, OG and sitemap URLs share one origin, currently `http://192.168.1.20:8084`, changed by search-and-replace when a public domain exists.
- Unknown content is marked `TODO-PLACEHOLDER` or a visibly marked placeholder image until Tiago supplies the real data.
- `scripts/check.sh` is plain bash (grep/sed): required meta tags; name, jobTitle and LinkedIn URL identical in JSON-LD and visible text (the message names the differing field); structure (one h1, ordered headings, non-empty experience/skills/education); same origin everywhere; page weight under 500 KB; no request to another origin (only loaded resources count: img, stylesheet, script, not `<a href>`); local link targets exist.
- `--release` mode: fails on `TODO-PLACEHOLDER`; fails if no JSON parser is available (no silent skip); requires a LinkedIn URL with host ending in `linkedin.com` and path starting `/in/` (trailing slash and encoded slugs accepted); warns, not fails, on phone/address patterns.
- Release gate owner: F-002 adds one step to `.github/workflows/deploy.yml` that runs `scripts/check.sh --release` before deploy. The Reviewer checks the step exists.
- Lighthouse (mobile >= 90) and the LinkedIn Post Inspector are manual steps; completeness of the content is a manual Reviewer check.
- Done for v0.1.0 means "ready to publish, structurally verified". Link previews and indexing cannot be proven on a LAN-only address.

## Out of scope
- CV PDF or any CV link (Tiago, v0.1.0 feedback: LinkedIn link only).
- Blog/posts, RSS, `content/posts/` (v0.2.0, not pre-committed).
- Content generator, `content/profile.json`, `scripts/build.sh`.
- Print CSS, custom 404 page, extra Twitter tags (only `twitter:card`), Lighthouse CI gate, analytics, cookie banner, multi-language, contact form, public email by default, "blog coming soon" teaser.

## Risks
- Critical path is content, not code: v0.1.0 cannot be released until Tiago supplies the LinkedIn URL, photo and target keywords (plus an email only if he chooses one).
- No public domain: LinkedIn preview and SEO cannot fully work on 192.168.1.20.
- Thin page: if the page becomes only a signpost to LinkedIn, bots have nothing to index; the full record must be on the page.
- The link must go both ways: after publishing, Tiago adds the site URL to his LinkedIn profile (contact info website and Featured section); otherwise `rel="me"` is unconfirmed and LinkedIn awareness does not grow. Manual post-release step, not a release gate.
- Brittle checks being ignored; keep them few, exact and with clear failure messages.
