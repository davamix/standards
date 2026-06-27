# Security Guidelines

> **Status: stub.** Establishes the document; content to be filled.

Shared security conventions every application in the ecosystem follows.

- **Authentication:** delegate to the central identity provider (Logto) via OIDC/OAuth2.
  Apps validate JWTs; they never store passwords or implement their own login.
- **Service-to-service:** use OAuth2 client-credentials (machine tokens) with scoped
  permissions; no shared static API keys between apps.
- **Secrets:** never commit secrets; inject via environment / container secrets. Document
  required variables, not their values.
- **Transport:** TLS terminated at the shared reverse proxy; no plaintext between the edge
  and clients.
- **Data:** each app's database credentials are scoped to its own database/schema only.

_TODO: expand with concrete, prescriptive rules and a review checklist._
