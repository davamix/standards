# Changelog

All notable changes to the shared standards are recorded here. The repo uses tagged
releases (`v0.1.0`, `v0.2.0`, …); consuming projects pin to a tag/commit and upgrade
deliberately.

## v0.5.0 — 2026-06-30

Enforce the security gates (don't just run them).

- [security.md](security.md): new **Gate enforcement** — `main` MUST be branch-protected to
  require the CI checks (enforced for admins) so a red CI blocks landing code; and a release /
  image-publish pipeline MUST re-run or depend on the same correctness + security gates (tests +
  vulnerable-dependency audit) so a tag never ships a build that fails them.

## v0.4.0 — 2026-06-30

Deployment modes + the standalone↔integrated transition.

- [architecture.md](architecture.md): new **Deployment modes & joining the shared stack** —
  every app runs standalone (bundled infra) or integrated (shared infra) with no code change.
  When moving to the shared stack an app re-points its OIDC issuer at the **ecosystem-neutral**
  host, re-registers its redirect URIs, and points persistence at the shared database as its
  own least-privilege role. The issuer host is configuration, never an app-hardcoded name; the
  shared composition lives in a separate platform repo and no app bundles another.

## v0.3.0 — 2026-06-29

Promote shared engineering, auth, and API-error conventions.

- [architecture.md](architecture.md): **Decision records (ADRs)** — every big change recorded
  as `context → decision → why → consequences` in `docs/decisions/`. **Review agents** — each
  app keeps project-tuned read-only reviewer agents in `.claude/agents/` and runs them per diff.
- [security.md](security.md): expanded **Authentication & authorization** — BFF for browser apps
  (tokens server-side, ASVS V10.1.1), resource-server JWT validation rules + one audience per
  app, fail-closed server-side authorization (query filter + service-layer checks), identify by
  `iss`+`sub`. New **Security baseline (OWASP ASVS)** — target ASVS 5.0 L2, tracked per app in
  `docs/security/asvs-l2/`.
- [api-design.md](api-design.md): pinned the **error shape** to RFC 9457 problem details
  (`application/problem+json`), emitted via framework helpers; validation uses the `errors`
  member.
- [templates/](templates/): added reference `post-create.sh` (devcontainer bootstrap — submodule
  hydration + gitleaks install + hook enable) and `pre-commit` (gitleaks scan) that the
  secrets/CI rules now point at, so the references resolve to real files.

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
