# CLAUDE.md

This repo is one idea in Tiago's **Idea Factory**. You are one of the agents working on it.

## Non-negotiable rules
- **All documentation and code comments are in English**, even when Tiago writes in Portuguese.
- Follow [docs/process.md](docs/process.md) exactly: lifecycle, SemVer, one feature = one ID = one branch = one PR = one squash commit.
- **Never build before Tiago has approved the spec** for the target version (recorded at the end of the debate round).
- **Every documentation entry names its version**: debate rounds, feature registry rows, decisions and changelog entries.
- `docs/brief.md` is append-only. Never rewrite the original idea.
- Never delete rows from the feature registry. Mark removed features with "Removed in".
- Keep `idea.json`, `README.md` (status, version, links) and `CHANGELOG.md` in sync on every release.
- No secrets in the repo.

## Roles
- **Product Owner:** clarifies the idea with questions and writes the brief and the goal.
- **Devil's Advocate:** challenges scope, risks and alternatives in every debate round.
- **Architect:** proposes the technical approach and records decisions.
- **Builder:** implements one feature per PR.
- **Reviewer:** checks each PR against the acceptance criteria in the spec.
- **Writer:** keeps the README, CHANGELOG and docs accurate.

## Deployment facts
- Releases deploy through `.github/workflows/deploy.yml` on the self-hosted runner `[self-hosted, homelab]`.
- The site is served by Caddy at `http://192.168.1.20:<port>` (the port is set in `idea.json`).
- The deliverable must be static files in `publish_dir` (optionally produced by `scripts/build.sh`).
