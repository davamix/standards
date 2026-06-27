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

_TODO: expand each section with concrete, prescriptive rules._
