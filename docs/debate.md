# Debate

Every debate round targets one version. A round ends with a conclusion that feeds [spec.md](spec.md).

<!--
## Round 1 — target v0.1.0 — YYYY-MM-DD

**Participants:** Tiago, Product Owner, Devil's Advocate, Architect

### Questions and answers
- **PO:** …
- **Tiago:** …

### Arguments
- **Devil's Advocate:** risk / alternative …
- **Architect:** proposal …

### Conclusion
- Agreed scope for v0.1.0: F-001, F-002
- Rejected / postponed: … (reason)
- ✋ Approved by Tiago: yes / no — YYYY-MM-DD
-->

## Round 1 — target v0.1.0 — 2026-10-20

**Participants:** Tiago, Product Owner

### Questions and answers
- **Tiago (v0.1.0):** The site first serves as a portfolio of his professional experience, a professional website with the same kind of information as his LinkedIn. It must boost his LinkedIn awareness and convince HR people, agents and bots to consider him for positions he matches, so it must be optimised for that.
- **Tiago (v0.2.0, second phase):** Add posts about things he did, researched or experienced (about 2 per month), always with the goal of growing his LinkedIn.
- **PO (assumptions, v0.1.0):**
  - v0.1.0 is a static, single-page portfolio (summary, experience, skills, education, certifications, contact, prominent LinkedIn link). No blog yet.
  - "Optimised" means: recruiter-readable, fast, mobile-friendly, semantic HTML, SEO metadata, Open Graph/LinkedIn share preview, JSON-LD Person schema, sitemap/robots, and machine-readable (ATS/bot friendly) content, plus a downloadable CV/PDF link.
  - The blog (2 posts/month, LinkedIn share-friendly) is deferred to v0.2.0.
  - Tiago supplies the real content (LinkedIn profile text); until then the builder uses clearly marked placeholders.

### Analyst — requirements for v0.1.0
- **Analyst:** (v0.1.0) User stories, target version only:
  1. As an HR person, I want to see role, summary, experience and skills at a glance, so I can decide quickly whether Tiago fits. AC: above the fold on a 375px-wide screen shows name, headline, short summary and a LinkedIn button; experience lists company, title, dates and 2-4 achievements per role, newest first.
  2. As a recruiter, I want a prominent LinkedIn link and contact option, so I can reach him. AC: LinkedIn link appears in header and contact section, opens the real profile URL, uses rel="me noopener"; the contact option is visible without scripts.
  3. As an ATS/bot/search crawler, I want machine-readable content, so I can index Tiago for matching roles. AC: all content is in static HTML (no JS needed); one h1, ordered headings, landmarks; valid JSON-LD Person (name, jobTitle, sameAs LinkedIn, knowsAbout); title, meta description, canonical; robots.txt and sitemap.xml present.
  4. As someone seeing the link shared on LinkedIn, I want a good preview, so it attracts clicks. AC: og:title/description/image/url and twitter card tags present; image is 1200x630; the preview validates in LinkedIn Post Inspector.
  5. As a recruiter, I want a downloadable CV, so I can attach it to my system. AC: a PDF link works, shows file size, and its content matches the page.
  6. As a mobile visitor, I want a fast, readable page, so I do not leave. AC: Lighthouse mobile >= 90 for Performance, Accessibility, SEO and Best Practices; no horizontal scroll at 320px; page weight under 500 KB excluding the PDF; WCAG AA contrast; works with JS disabled.
- **Analyst:** (v0.1.0) Data needed: all content is hand-authored static data from Tiago's LinkedIn (name, headline, summary, experience, skills, education, certifications, languages, contact, LinkedIn URL, photo, CV PDF). Keep it in one content source so v0.2.0 can reuse it. No analytics or cookies in v0.1.0, so no consent banner. Base URL comes from the Caddy address (http://192.168.1.20:<port>) for now.
- **Analyst:** (v0.1.0) Edge cases: placeholders must be visibly marked and a check must fail or warn before release if any remain; canonical/OG URLs and sitemap must use a single configurable base URL (no real domain exists yet); missing or oversized photo/OG image; long titles or many roles on mobile; print view; PDF out of sync with the page; privacy (no phone or home address unless Tiago chooses); stale links.
- **Analyst:** (v0.1.0) Open questions for Tiago and the plan:
  1. Which language(s)? Assumed English only.
  2. Which contact details are public (email, phone, LinkedIn only)?
  3. Will Tiago provide the LinkedIn URL, photo and CV PDF, or should the CV be generated from the page (print to PDF)?
  4. Which target roles/keywords to emphasise (headline, skills order, knowsAbout)?
  5. Is a public domain planned? On a LAN-only address (192.168.1.20) LinkedIn previews and SEO cannot work fully; the plan should state this limit and keep the base URL configurable.
  6. Any "blog coming soon" teaser, or nothing until v0.2.0? Suggested: nothing.

### Architect — technical proposal for v0.1.0
- **Architect:** (v0.1.0) Stack: plain HTML + CSS, no framework, no runtime JS needed. All content must be in static HTML so crawlers and ATS see it with JS disabled.
- **Architect:** (v0.1.0) Deliverable folder (`publish_dir` = `site/`): `index.html`, `assets/style.css`, `assets/img/` (photo, `og-image.png` 1200x630), `cv/<name>-cv.pdf`, `robots.txt`, `sitemap.xml`, `404.html`. Helper scripts in `scripts/`.
- **Architect:** (v0.1.0) Data storage: nothing at runtime (no IndexedDB, no cookies, no analytics). Content lives in one source file `content/profile.json` (name, headline, summary, roles, skills, education, certifications, links).
- **Architect:** (v0.1.0) Option A (recommended): `scripts/build.sh` (bash plus a tiny dependency-free Node or Python script) renders `content/profile.json` into `site/index.html` (including the JSON-LD Person block), `sitemap.xml` and `robots.txt`, using one `BASE_URL` variable. The same JSON is reused by v0.2.0. The build fails on leftover placeholders (a `TODO-PLACEHOLDER` marker) and on missing files, covering the analyst's edge cases. Generated `site/` is committed so Caddy can serve it even without a build.
- **Architect:** (v0.1.0) Option B: edit `site/index.html` by hand, with JSON-LD and metadata inline. Zero tooling, but content, JSON-LD and the CV drift out of sync and v0.2.0 would need a refactor.
- **Architect:** (v0.1.0) Recommendation: Option A, kept deliberately small (one template, one script, no npm packages). For v0.2.0 the blog fits as Markdown files in `content/posts/` rendered by the same script into `site/posts/<slug>/index.html`, with per-post Open Graph tags and an RSS feed.
- **Architect:** (v0.1.0) Main risks: (1) LinkedIn previews and SEO cannot fully work on a LAN-only address; BASE_URL stays configurable and a public domain is a later step outside the factory setup. (2) CV PDF out of sync with the page; mitigate with a build check on key fields, or generate the PDF from the page via print CSS (analyst question 3). (3) Lighthouse >= 90 is easy with static files if images are optimised and fonts are system fonts (no third-party requests). (4) Placeholders shipped by mistake; the build check covers it. (5) Privacy: only public contact fields Tiago confirms.
- **Architect:** (v0.1.0) Verification: `scripts/check.sh` (HTML well-formed, JSON-LD parses, required meta tags present, page weight under 500 KB excluding PDF, no external requests); Lighthouse run manually.

### Developer — build split for v0.1.0
- **Developer:** (v0.1.0) I back Architect Option A, kept small. Proposed features, in order, each reviewable on its own:
  1. F-001 Content model and build — `content/profile.json` (with `TODO-PLACEHOLDER` values), `scripts/build.sh` plus one dependency-free script, one template, a single `BASE_URL`. Renders a minimal valid `site/index.html`, `robots.txt`, `sitemap.xml`. Build fails on leftover placeholders in release mode.
  2. F-002 Page sections and styling — header with LinkedIn button, summary, experience, skills, education, certifications, contact, mobile-first CSS with system fonts, print CSS, `404.html`. No JS.
  3. F-003 SEO and sharing — title/description/canonical, Open Graph and Twitter tags, JSON-LD Person, `og-image.png` 1200x630.
  4. F-004 CV PDF and `scripts/check.sh` — PDF link with file size, checks for well-formed HTML, JSON-LD, meta tags, page weight under 500 KB, no external requests.
- **Developer:** (v0.1.0) Flags: (a) placeholders vs. deploy — the placeholder check must be a release-mode flag, otherwise early deploys fail before Tiago provides content. (b) The generated `site/` is committed, so PRs include generated diffs; the Reviewer should check the `build.sh` output is current. (c) The CV PDF is bigger than it looks: either Tiago supplies the file, or we generate it from the page via print CSS, which needs a headless browser the runner may not have. I suggest Tiago supplies the PDF for v0.1.0 and the check only verifies the link and file exist. (d) Lighthouse cannot run in CI without Chrome; keep it a manual step, as the Architect said. (e) Photo and OG image need real assets from Tiago; until then a clearly marked placeholder image is used.
- **Developer:** (v0.1.0) Open questions that block a real release (not the build): Tiago's LinkedIn URL, public contact fields, photo, CV PDF, target keywords, and language (English assumed). I agree with the Analyst that "blog coming soon" should be omitted.

### Conclusion (draft, awaiting round table)
- Proposed scope for v0.1.0: portfolio landing page optimised for recruiters, search engines and bots.
- Postponed to v0.2.0: blog/posts.
