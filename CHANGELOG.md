# Changelog

All notable changes to the shared standards are recorded here. The repo uses tagged
releases (`v0.1.0`, `v0.2.0`, …); consuming projects pin to a tag/commit and upgrade
deliberately.

## v0.2.0 — 2026-06-27

Security guidelines + a pinning fix.

- Filled in [security.md](security.md) with cross-project security rules: secrets handling,
  required CI scanning (gitleaks hard gate + Trivy), pre-commit secret scanning,
  least-privilege CI tokens, Dependabot, container hardening, auth, transport/data.
- [working-with-standards.md](working-with-standards.md): use `git submodule update --init`
  (not `--remote`) so devcontainers check out the *pinned* commit instead of the branch tip.

## v0.1.0 — 2026-06-27

Initial structure.

- Established the repo as the single source of truth for shared specifications, consumed
  via git submodule at `.standards/`.
- Added [working-with-standards.md](working-with-standards.md) documenting the convention
  (submodule + devcontainer hydration + `CLAUDE.md` imports + versioning workflow).
- Added stub guideline docs: [architecture.md](architecture.md), [security.md](security.md),
  [api-design.md](api-design.md).
- Added [ROADMAP.md](ROADMAP.md) tracking deferred cross-project upgrades (static docs
  site; Claude Code reviewer plugin).
