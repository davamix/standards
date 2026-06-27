# API Design Guidelines

> **Status: stub.** Establishes the document; content to be filled.

Shared API conventions so every app's API is predictable — for humans and for the AI
assistant that consumes them as tools.

- **Style:** REST over HTTP/JSON; resource-oriented URLs; standard verbs and status codes.
- **Contracts:** publish a machine-readable OpenAPI document per API (generated, e.g. via
  `Microsoft.AspNetCore.OpenApi`). The Swagger *UI* is optional and currently skipped; the
  *document* is not.
- **Errors:** one consistent error shape across all apps (e.g. RFC 9457 problem details).
- **Pagination/filtering:** one consistent convention across all apps.
- **Auth:** every endpoint expects a JWT from the central IdP (see [security.md](security.md)).
- **Versioning:** version the API surface so consumers (including the assistant) don't break.

_TODO: pin the exact error shape, pagination params, and naming rules._
