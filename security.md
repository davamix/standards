# Security Guidelines

Security rules every project in the set must follow. These are cross-project requirements;
project-specific hardening lives in that project's `docs/`. "MUST" = required, "SHOULD" =
strongly recommended.

## Secrets & credentials

- **Never commit secrets** (API keys, tokens, passwords, private keys, connection strings).
  MUST.
- Inject secrets via **environment variables / container secrets**, never source. Document
  the *required variable names*, never their values. MUST.
- Run a **pre-commit secret scan** (gitleaks) so secrets are caught before they're
  committed. SHOULD. Copy [`templates/pre-commit`](templates/pre-commit) to your repo's
  `.githooks/pre-commit` (used verbatim) and [`templates/post-create.sh`](templates/post-create.sh)
  to `.devcontainer/post-create.sh` (hydrates `.standards`, installs gitleaks, enables the hook;
  extend with project-specific setup as needed), wired via the devcontainer `postCreateCommand`.
- CI MUST fail on any detected secret (see CI scanning below). This is the backstop for the
  pre-commit hook.

## CI security scanning

Every project's CI pipeline MUST include:

- **Secret scan — gitleaks** over full history (`fetch-depth: 0`), as a **hard gate**
  (fails the build on any finding).
- **Vulnerability / misconfiguration scan — Trivy** (`scan-type: fs`, scanners
  `vuln,secret,misconfig`). Findings SHOULD upload as SARIF to the GitHub Security tab.
  Start report-only if needed, but graduate to a hard gate (`exit-code: 1`) once findings
  are triaged.
- **Least-privilege token:** set `permissions: contents: read` at the workflow top level;
  widen per-job only as needed (e.g. `security-events: write` to upload SARIF). MUST.
- **Pin third-party Actions** to a tag (SHOULD pin to a commit SHA) and let Dependabot keep
  them current.

## Gate enforcement

Scanning only protects you if a failing scan actually **blocks** progress. Both paths into a
release MUST be gated and enforced, not advisory:

- **Protect the default branch.** `main` MUST require the CI security/quality checks (secret
  scan, vuln/misconfig scan, build/tests, dependency audit) to pass before merge — via branch
  protection / a ruleset, **enforced for admins** — so a red CI blocks landing code instead of
  relying on discipline. MUST.
- **Releases must not ship a red build.** A tag/release or image-publish pipeline MUST re-run
  (or `needs`) the same correctness + security gates as CI — tests and the
  vulnerable-dependency audit at minimum — because tag-triggered pipelines don't run the
  push/PR CI. A release that fails a gate MUST stop before publishing. MUST.

## Dependencies

- **Dependabot** MUST be enabled for every package ecosystem the repo uses (e.g.
  `nuget`, `github-actions`, `devcontainers`).
- CI MUST surface known-vulnerable dependencies and SHOULD fail on them (e.g.
  `dotnet list package --vulnerable` for .NET).

## Containers

- Run containers as a **non-root user** (e.g. `USER $APP_UID` for .NET images). MUST.
- Base images MUST be official/maintained and kept updated (Dependabot / Trivy).
- Before deploying, scan the **built image** with Trivy (`scan-type: image`) for base-image
  CVEs. SHOULD.

## Authentication & authorization

- Delegate authentication to the **central identity provider** (our IdP: Logto) via
  OIDC/OAuth2. Apps **validate JWTs**; they MUST NOT store passwords or roll their own login.
- **Browser apps use a backend-for-frontend (BFF).** The server runs the OIDC code flow and
  keeps access/refresh tokens **server-side** (encrypted cookie); tokens MUST NOT be exposed to
  browser JavaScript (OWASP ASVS V10.1.1). A public SPA holding access/refresh tokens is not
  acceptable.
- **Resource servers validate JWTs** with the issuer pinned to the IdP, the audience checked,
  the signature verified via JWKS, lifetime enforced, and `alg:none` rejected. Each app has
  **one API resource (audience)**; a token for another audience MUST be rejected. Identify the
  user by `iss`+`sub` (which cannot be reassigned) — never from a client-supplied field.
- **Authorize at a trusted layer.** Per-user / per-tenant data isolation is enforced
  server-side — e.g. a data-layer query filter that **fails closed** when no subject is set
  (blocking IDOR / BOLA), plus operation-level checks in the service layer. Never rely on
  client-side checks. MUST.
- **Service-to-service** calls use OAuth2 **client-credentials** (scoped machine tokens);
  no shared static API keys between apps. MUST.

## Security baseline (OWASP ASVS)

- Every app targets **OWASP ASVS 5.0 Level 2**. MUST.
- Track conformance per app in `docs/security/asvs-l2/`: one file per ASVS chapter with a
  per-requirement status (✅ / ❌ / ❓ / ➖) and `file:line` / PR evidence, plus a dashboard
  index. The *tracker structure* is shared; the per-app status rows are project-local.

## Transport & data

- **TLS** is terminated at the shared reverse proxy; no plaintext to clients. MUST.
- Each app's **database credentials are scoped** to its own database/schema only, with the
  least privilege required. MUST.

---

_This file is shared content — it captures our stack's decisions. Forks should adapt the
stack-specific parts (IdP, container/runtime specifics) while keeping the cross-project
rules. Add rules here whenever a security practice should apply to every project._
