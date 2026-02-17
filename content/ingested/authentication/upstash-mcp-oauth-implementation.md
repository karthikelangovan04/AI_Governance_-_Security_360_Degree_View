---
source_url: "https://upstash.com/blog/mcp-oauth-implementation"
source_title: "Implementing MCP OAuth: A Technical Deep-Dive | Upstash Blog"
ingested_date: "2025-02-17"
topic: "authentication"
google_services: []
notes: "No Google Cloud services in this article. OAuth 2.1 / MCP authorization pattern; Clerk as IdP; Context7 MCP server. Use for general MCP OAuth governance (discovery, DCR, PKCE, token exchange)."
---

# Implementing MCP OAuth: A Technical Deep-Dive (Upstash)

**Source:** Upstash blog. **Google services:** None — article uses Clerk and Context7; relevant for **MCP OAuth** patterns (discovery, dynamic client registration, PKCE, token exchange) used in any MCP deployment, including on Google Cloud.

## Summary

MCP OAuth replaces API keys with OAuth so users authorize in-browser without copying keys. MCP authorization follows OAuth 2.1 with adaptations for MCP client–server. Roles: Resource Owner = user; Resource Server = MCP server; Client = MCP client (e.g. Cursor, Claude); Authorization Server = auth provider.

## Four-phase OAuth flow

1. **Discovery** — Client hits MCP server unauthenticated → 401 + `WWW-Authenticate: Bearer resource_metadata=".../.well-known/oauth-protected-resource"` → client fetches resource metadata (`authorization_servers`, `scopes_supported`) → client fetches auth server metadata (`/.well-known/oauth-authorization-server`: `authorization_endpoint`, `token_endpoint`, `registration_endpoint`, PKCE S256, `authorization_code`, `refresh_token`, `token_endpoint_auth_method: none`).
2. **Dynamic Client Registration (DCR)** — Client POSTs to `registration_endpoint` with `client_name`, `redirect_uris`, `grant_types`, `token_endpoint_auth_method: "none"` (public client). Server returns `client_id`. MCP clients should be public clients (no secret).
3. **Authorization (PKCE)** — Client generates `code_verifier` and `code_challenge` (S256); opens browser to `authorize` with `code_challenge`, `state`, `redirect_uri`; user authenticates and consents; server redirects back with short-lived `code`.
4. **Token exchange** — Client POSTs `grant_type=authorization_code`, `code`, `code_verifier`, `redirect_uri`, `client_id`. Server verifies PKCE, `redirect_uri`, single use; returns `access_token`, optional `refresh_token`. Client sends `Authorization: Bearer <token>` to MCP server.

## Governance takeaways

- **PKCE** is required for public MCP clients.
- **DCR** is unauthenticated; access is granted only at authorization (user consent).
- **Redirect URI** must match exactly (localhost vs 127.0.0.1 causes real-world failures — normalize at registration, authorization, and token exchange).
- **Opaque tokens** require server-side validation (e.g. `/oauth/userinfo`); JWTs can be verified locally.
- **token_endpoint_auth_method** should be `"none"` for MCP clients; some clients incorrectly request confidential client — override in a proxy if needed.

## References (from source)

- RFC 7591 (DCR), RFC 8414 (auth server metadata), RFC 9728 (protected resource metadata), OAuth 2.1 draft, [MCP Authorization Specification](https://modelcontextprotocol.io/specification/draft/basic/authorization).

## Reference

- **URL:** [Implementing MCP OAuth: A Technical Deep-Dive](https://upstash.com/blog/mcp-oauth-implementation)
- **Cited:** 2025-02-17
