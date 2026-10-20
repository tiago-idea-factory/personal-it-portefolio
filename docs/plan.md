# Implementation plan — v0.1.0
Status: proposed

## Goal
A static, single-page professional portfolio for Tiago, mirroring his LinkedIn profile, optimised for recruiters, ATS systems, search crawlers and bots, hosted initially on GitHub Pages, and sending visitors to his LinkedIn profile (no CV download).

## Features
- [x] F-001: Portfolio page — hand-written `site/index.html` and CSS with the full record, a LinkedIn button, SEO metadata, Open Graph, JSON-LD Person, version meta, `robots.txt`, `sitemap.xml` and placeholder assets, all with relative paths
- [ ] F-002: Release check — `scripts/check.sh` with a `--release <tag>` mode that blocks placeholders, stale versions and bad URLs
- [ ] F-003: GitHub Pages publishing — separate `.github/workflows/pages.yml` that runs the release check and then publishes `site/` to GitHub Pages

## Technical approach
- Plain HTML + CSS in `site/` (the `publish_dir`): no framework, no JavaScript, no cookies, no analytics, system fonts, no third-party requests.
- Bots cannot read LinkedIn behind its login wall, so the page carries the full record in static HTML (summary, roles with company, title, dates and 2-4 achievements, skills, education, certifications). The LinkedIn button is the call to action, not a replacement for content.
- Semantic landmarks, one h1, ordered headings. Hand-written `site/index.html` with inline metadata and JSON-LD; no content model and no generator.
- LinkedIn is the only public contact: no `mailto:`, no form, no email unless Tiago explicitly chooses one.
- One LinkedIn URL string in exactly three places: header button, contact section and JSON-LD `sameAs`. Links carry `rel="me noopener"` (no `noreferrer`).
- JSON-LD Person: name, jobTitle, `sameAs`, `knowsAbout` (Tiago's keywords), `image`, `url`.
- Visible text, title, meta and JSON-LD say "portfolio" (correct spelling); the repo and URL slug "portefolio" is not renamed.
- Hosting: GitHub Pages project site `https://tiago-idea-factory.github.io/personal-it-portefolio/`. This is the single absolute origin used by canonical, `og:url`, `og:image`, JSON-LD `url` and `sitemap.xml`, even in the LAN copy. All internal references are relative (no leading `/`), so one `site/` works at the Caddy root and under the Pages sub-path. The Caddy deploy (`deploy.yml`, unchanged) keeps serving a LAN preview of the same files, without the gate.
- The Pages URL is temporary until Tiago explicitly accepts it as stable; the LinkedIn profile link and the first LinkedIn share wait for that. A custom domain or a `<user>.github.io` repo is a later change of base URL plus a re-release.
- Unknown content is marked `TODO-PLACEHOLDER` or a visibly marked placeholder image until Tiago supplies the real data.
- `scripts/check.sh` is plain bash (grep/sed plus `jq` or `python3`), portable to `ubuntu-latest`: required meta tags; name, jobTitle and LinkedIn URL identical in JSON-LD and visible text (the message names the differing field); structure (one h1, ordered headings, non-empty experience/skills/education); one origin and path prefix for absolute URLs; no `src`/`href` starting with a single `/`; page weight under 500 KB; no request to another origin (only img, stylesheet and script count, not `<a href>`); local link targets exist.
- `--release <tag>` mode: fails on `TODO-PLACEHOLDER`; fails if `<meta name="version">` differs from the tag; fails if no JSON parser is available (no silent skip); requires a LinkedIn URL with host ending in `linkedin.com` and path starting `/in/` (trailing slash and encoded slugs accepted); warns, not fails, on phone/address patterns.
- `pages.yml`: triggers `release: published` and `workflow_dispatch` with a tag input; the tag is validated against `^v[0-9]+\.[0-9]+\.[0-9]+$` in both triggers and passed through `env:`, never interpolated into `run:`; checks out `refs/tags/<tag>`; runs `scripts/check.sh --release <tag>`; then `configure-pages`, `upload-pages-artifact` (`path: site`) and `deploy-pages` with `pages: write` and `id-token: write`, the `github-pages` environment and a `concurrency` group. It never runs on push to `main`.
- Release checklist: run `scripts/check.sh --release <next-tag>` locally on the release PR before tagging, because a tag cannot be reused. The release meta version is bumped in the release PR.
- Hold the v0.1.0 release until Tiago's content, Pages settings and URL acceptance are in, so v0.1.0 means "published". The build can be merged and previewed on the LAN meanwhile. If Tiago prefers an early LAN-only release, the Caddy run is green and `pages.yml` is red on purpose.
- Lighthouse (mobile >= 90) and the LinkedIn Post Inspector (re-run after each release because of caches) are manual steps; content completeness is a manual Reviewer check.
- Done for v0.1.0 means "published on GitHub Pages and the public checks (Post Inspector, indexing) done by hand".

## Out of scope
- CV PDF or any CV link (Tiago, v0.1.0 feedback: LinkedIn link only).
- Blog/posts, RSS, `content/posts/` (v0.2.0, not pre-committed).
- Content generator, `content/profile.json`, `scripts/build.sh`.
- A separate `<user>.github.io` repo, custom domain, `CNAME`, custom 404, sitemap-submission automation, Post Inspector from CI.
- Editing `deploy.yml` (factory infrastructure) or adding the gate to the Caddy path.
- Print CSS, extra Twitter tags (only `twitter:card`), Lighthouse CI gate, analytics, cookie banner, multi-language, contact form, public email by default, "blog coming soon" teaser, renaming the repo slug.

## Risks
- Critical path is Tiago's decisions and content: LinkedIn URL, photo and target keywords; informed yes that the whole repo is public (brief, debate, `CLAUDE.md`, `deploy.yml` with the LAN address and SSH user; no secrets); enabling Pages with source "GitHub Actions"; allowing the release tag in the `github-pages` environment.
- Two unknowns can only be tested on a real release: whether the release event triggers `pages.yml`, and whether the `github-pages` environment accepts a tag ref. `workflow_dispatch` (runs from `main`, checks out the tag) is the documented fallback. The F-003 PR says "not verified" instead of claiming it works.
- The URL shows `tiago-idea-factory` and is part of the product; changing it later loses link equity and shared previews. Mitigation: it is temporary until Tiago accepts it.
- A tag cannot be reused, so a gate failure at release time burns a version; mitigated by the local pre-flight.
- `robots.txt` is only honoured at a host root, so it has no effect under the Pages sub-path; the sitemap is submitted by hand if wanted.
- Thin page: if the page becomes only a signpost to LinkedIn, bots have nothing to index; the full record must be on the page.
- The link must go both ways: after Tiago accepts the URL, he adds it to his LinkedIn profile (contact info website and Featured section), otherwise `rel="me"` is unconfirmed. Manual step, not a release gate.
- Brittle checks being ignored; keep them few, exact and with clear failure messages.
