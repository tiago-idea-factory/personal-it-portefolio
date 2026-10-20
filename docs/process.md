# Process

How every idea repo in [tiago-idea-factory](https://github.com/tiago-idea-factory) works.

## 1. Lifecycle (the factory pipeline)

`idea` → `refining` → `planning` → `approval` → `building` → `ready` → `live`

The factory bot drives every version through these phases, and shows the status on the landing page (http://192.168.1.20).

1. **Brief:** the idea is written verbatim into [brief.md](brief.md), with who proposed it.
2. **Refining:** the 🧭 Product Owner clarifies the goal, users and scope with the people in the idea's Telegram topic ([debate.md](debate.md)).
3. **Planning (round table):** 🔎 Analyst → 🏛️ Architect → 💻 Developer → 😈 Devil's Advocate each give their input in the same debate round; then the Product Owner writes the **implementation plan** in [plan.md](plan.md) and the features in [spec.md](spec.md).
4. **Approval:** ✋ **Tiago approves the plan** (or asks for changes, which go back to the round table). Nothing is built before.
5. **Building:** the Architect sets up the structure; then for each feature in the plan, the Developer opens a PR and the 🔍 Reviewer reviews and squash-merges it (see §3).
6. **Ready:** the 📦 Release Manager prepares the release notes and ✋ **Tiago decides**: release now, or keep it built but unreleased.
7. **Live:** the Release Manager creates the GitHub Release `vX.Y.Z`, which deploys the deliverable (see §4). A new request after that starts the next version at step 2.

## 2. Versioning (SemVer)

| Change | Bump | Example |
|--------|------|---------|
| First usable deliverable (MVP) | — | `v0.1.0` |
| New feature | minor | `v0.1.0` → `v0.2.0` |
| Feature removed | minor | `v0.2.0` → `v0.3.0` |
| Bug fix only | patch | `v0.3.0` → `v0.3.1` |
| Tiago declares the idea "done" | major | `v1.0.0` |

**Every document references the version**: debate rounds have a target version, features have "added in / removed in", and decisions and changelog entries name their version.

## 3. Features: one ID = one branch = one PR = one commit

1. Give the feature the next ID (`F-001`, `F-002`, …) and add it to the feature registry in [spec.md](spec.md).
2. Branch: `feature/F-002-short-name`.
3. PR title: `F-002: Short name`. Fill in the PR template (version, debate and spec links).
4. **Squash-merge** into `main`, so the whole feature is exactly one commit.
5. Add the feature under `## [Unreleased]` in [CHANGELOG.md](../CHANGELOG.md).

Fixes follow the same flow with branch `fix/<short-name>` and no new feature ID.

## 4. Releasing

1. Move the `[Unreleased]` changelog entries under a new `## [X.Y.Z] - YYYY-MM-DD` heading.
2. Update the **status** in `idea.json` and the version in `README.md`, then commit to `main`.
3. Publish the release, with the changelog section as notes:

   ```bash
   gh release create vX.Y.Z --title "vX.Y.Z" --notes-file <notes.md>
   ```

4. The **Deploy** workflow runs on the home lab runner:
   - it uploads the deliverable to `/srv/sites/<id>/releases/vX.Y.Z/` on Caddy;
   - it points `/srv/sites/<id>/current` at that release;
   - it writes the Caddy site config (`:<port>`) and reloads Caddy when needed;
   - it updates this idea's card (version, status, links) on the landing page.

## 5. Removing a feature

1. Debate/decide it, and record it in [decisions.md](decisions.md) with the reason.
2. `git revert <squash commit of F-00X>` on a branch `revert/F-00X-short-name`, then open a PR and squash-merge it.
3. In the feature registry, set **Removed in** to the next version.
4. Add it under `### Removed` in the changelog, then release (minor bump).

## 6. Rolling back a deployment

To serve an older release again, without changing `main`:

- GitHub → Actions → **Deploy** → *Run workflow* → version `vX.Y.Z`
- or from a terminal: `gh workflow run deploy.yml -f version=vX.Y.Z`

Record the rollback in [decisions.md](decisions.md).

## 7. `idea.json`

| Field | Meaning |
|-------|---------|
| `id` | kebab-case id, the same as the repo name. Used for the folder on Caddy. |
| `title`, `summary`, `tags` | Shown on the landing page |
| `kind` | `site` or `report`. Decides whether the landing page link is "Open site" or "Report". |
| `status` | Lifecycle status (§1) |
| `created` | Date the idea entered the factory |
| `port` | Caddy port for this idea (8081+, unique per idea) |
| `publish_dir` | Folder to publish (`site`, `report`, `dist`, …) |

**Optional build step:** if `scripts/build.sh` exists and is executable, the deploy runs it first. It must leave the output in `publish_dir`.
