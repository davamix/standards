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
  committed. SHOULD (install via the devcontainer; see calendar's
  `.devcontainer/post-create.sh` + `.githooks/pre-commit` for the reference setup).
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
- **Service-to-service** calls use OAuth2 **client-credentials** (scoped machine tokens);
  no shared static API keys between apps. MUST.

## Transport & data

- **TLS** is terminated at the shared reverse proxy; no plaintext to clients. MUST.
- Each app's **database credentials are scoped** to its own database/schema only, with the
  least privilege required. MUST.

---

_This file is shared content — it captures our stack's decisions. Forks should adapt the
stack-specific parts (IdP, container/runtime specifics) while keeping the cross-project
rules. Add rules here whenever a security practice should apply to every project._
