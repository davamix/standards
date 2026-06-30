# Architecture Guidelines

> **Status: stub.** Establishes the document; content to be filled as the ecosystem grows.

Shared architectural conventions every application in the ecosystem follows. The agreed
target shape (see the discussion that produced this repo):

- **One shared instance of each capability, logically partitioned.** Run one database
  server, one identity provider, one reverse proxy, one broker — each with per-app
  isolation inside — rather than one of everything per app.
- **Identity is centralized.** A single identity provider (Logto) issues tokens; every app
  is a JWT validator and implements no auth of its own. This is the keystone of ecosystem
  cohesion.
- **Data:** one PostgreSQL server, one database (or schema) per app; use the `pgvector`
  extension rather than a separate vector database until scale demands otherwise.
- **Inter-app communication:** synchronous REST first (each app exposes its own API behind
  a shared reverse proxy); an event bus (NATS) added only when apps need to react to each
  other's changes.
- **Deployment:** Docker / Compose; each app keeps its own Dockerfile. No orchestrator
  until genuinely needed.
- **Simplicity first:** default to the leanest approach that works; introduce a
  framework/toolchain only when the need is clearly justified.

## Deployment modes & joining the shared stack

Every app runs two ways with **no code change**, driven only by configuration — a connection
string, an issuer URL, an API audience, client credentials:

- **Standalone** — the app bundles its own database, identity provider, and reverse proxy
  (behind a Compose profile) for solo dev, demos, and CI.
- **Integrated** — the bundled infra stays off and the app points at the *shared* ecosystem
  database, identity provider, and reverse proxy. The shared composition (which apps run, plus
  the shared DB / IdP / proxy) lives in a separate **platform repo**, never in an app's own
  compose, and **no app bundles another**.

When an app moves standalone → integrated it MUST:

1. **Re-point identity at the shared, ecosystem-neutral issuer.** A standalone app may front
   its bundled IdP at an app-scoped host (e.g. `auth.<app>.localhost`); the *shared* issuer is
   **ecosystem-neutral** (e.g. `auth.localhost`, or `auth.<ecosystem-domain>` in production).
   Update the issuer/authority config accordingly. The issuer host is **configuration, never
   code** — no app may hardcode or assume its own name in the issuer.
2. **Re-register the app's redirect URIs** in the shared IdP for the host the app is served on
   in the shared stack (e.g. `…/signin-oidc` and the sign-out callback under
   `http://<app>.localhost:8080`). Changing the issuer invalidates redirect URIs registered
   against the bundled IdP.
3. **Point persistence at the shared database** — one database per app, connecting as the
   app's own least-privilege role — and disable the bundled-infra profile.

The reverse proxy routes to each app and to the IdP **by hostname**; browsers resolve
`*.localhost → 127.0.0.1` so a single forwarded port serves the whole local stack, and
containers resolve the issuer host via a proxy network alias so an app's server-side OIDC
back-channel uses the *same* URL the browser does.

## Decision records (ADRs)

Every big change is recorded as a short **ADR** in the app's `docs/decisions/` —
`context → decision → why → consequences`, one numbered file per change (`0001-…`). This keeps
the *why* behind a change from being lost once it ships. MUST for anything that changes
architecture, auth, the data model, or deployment.

## Review agents

Each app maintains a small set of **project-tuned, read-only reviewer agents** in
`.claude/agents/` and runs them on each significant diff before merge. Baseline themes:
**security** (auth/secrets/config), **access-control / data-isolation** (authorization query
paths), **architecture** (conventions/layering), and **migration** (schema-change safety). The
agents encode the app's own rules so reviews stay consistent across humans and AI. SHOULD.
(Precursor to the reviewer plugin in [ROADMAP.md](ROADMAP.md).)

_TODO: expand the "target shape" bullets above into concrete, prescriptive rules._
