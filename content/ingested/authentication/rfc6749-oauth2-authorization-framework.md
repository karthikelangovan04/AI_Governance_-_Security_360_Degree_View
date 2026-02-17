---
source_url: "https://datatracker.ietf.org/doc/html/rfc6749"
source_title: "RFC 6749 - The OAuth 2.0 Authorization Framework"
ingested_date: "2025-02-17"
topic: "authentication"
---

# RFC 6749 — The OAuth 2.0 Authorization Framework

**Type:** IETF standard (Proposed Standard, October 2012). **Not Google-specific** — foundational for OAuth 2.0 used by Google and others.

## Summary

OAuth 2.0 enables a **third-party application** to obtain **limited access** to an HTTP service:
- On behalf of a **resource owner** (user), via an approval flow, or
- On its **own behalf** (client credentials).

Replaces OAuth 1.0 (RFC 5849).

## Roles

- **Resource owner** — entity that can grant access (often end-user).
- **Resource server** — hosts protected resources; accepts access tokens.
- **Client** — application requesting access on behalf of resource owner.
- **Authorization server** — issues access tokens after authenticating resource owner and validating authorization.

## Protocol flow (abstract)

1. Client requests authorization from resource owner.
2. Client receives **authorization grant**.
3. Client exchanges grant for **access token** at authorization server (with client auth if confidential).
4. Client uses **access token** to access protected resource at resource server.

## Grant types

- **Authorization code** — server as intermediary; code exchanged for token; no password to client.
- **Implicit** — token in redirect (e.g. fragment); no refresh token; less secure.
- **Resource owner password credentials** — username/password to client; high trust only.
- **Client credentials** — client acts on its own behalf; confidential clients only.

## Tokens

- **Access token** — used to access protected resources; scope and lifetime; Bearer or other type (e.g. RFC 6750).
- **Refresh token** — used only with authorization server to get new access tokens; optional; confidential.

## Security (high level)

- TLS for endpoints; client authentication for confidential clients; short-lived authorization codes; binding of refresh token to client; CSRF protection (e.g. state); avoid implicit for sensitive flows.

## Relevance to agent governance

- **Agent-as-client:** Agents calling APIs often use **client credentials** (M2M) or **authorization code** (on-behalf-of user). Google Cloud and Identity Platform implement OAuth 2.0.
- **Least privilege:** Use minimal **scope** when requesting tokens.

## Reference

- **URL:** [RFC 6749 - The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)
- **Cited:** 2025-02-17
