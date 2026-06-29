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
