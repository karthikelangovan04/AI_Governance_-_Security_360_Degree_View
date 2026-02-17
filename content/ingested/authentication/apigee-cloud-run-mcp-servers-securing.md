---
source_url: "https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235"
source_title: "Deploying and Securing MCP Servers with Google Cloud Run and Apigee | ApiGeek | Medium"
ingested_date: "2025-02-17"
topic: "authentication"
google_services:
  - "Cloud Run"
  - "Apigee (API management)"
  - "Secret Manager"
  - "Artifact Registry"
  - "IAM (Cloud Run Invoker role)"
  - "Apigee Developer Portal"
---

# Deploying and Securing MCP Servers with Google Cloud Run and Apigee

**Google Cloud services:** [**Cloud Run**](https://cloud.google.com/run) (MCP server runtime), [**Apigee**](https://cloud.google.com/apigee) (API management, auth, governance, developer portal), [**Secret Manager**](https://cloud.google.com/secret-manager) (credentials), [**Artifact Registry**](https://cloud.google.com/artifact-registry) (container images). **IAM:** Cloud Run Invoker granted only to Apigee’s service account so Apigee is the only entry point.

## Summary

Reference architecture for **enterprise MCP at scale**: deploy MCP servers on **Cloud Run**, front them with **Apigee** for authentication, authorization, governance, discoverability, and tiered access. Solves: guardrails and policy enforcement, discoverability, granular access control (tool-level), governance and auditability, security, scalability.

## Cloud Run (runtime for MCP servers)

- **Serverless**, per-request billing; scale to zero; multi-language containers.
- **Secure by default:** Authenticated invocations only; grant **Cloud Run Invoker** only to Apigee’s service account → Apigee is sole entry point.
- **Secret Manager** — retrieve credentials for backends/APIs; no hardcoding.
- **Artifact Registry** — store container images; deploy with one command.
- Centralized: one deployment shared by clients instead of per-developer servers.

## Apigee (front door for MCP)

- **Authentication & authorization:** API keys, OAuth 2.0, JWT validation, enterprise SSO.
- **API Products:** Group MCP tools into tiers (e.g. Basic, Advanced, Premium); each API key maps to a product → **allowed_tools** attribute; clients see only entitled tools in `tools/list`; invocation blocked by Apigee if not allowed.
- **Governance and guardrails:** Policies applied consistently; compliance, auditability, tool access standards.
- **Developer portal:** Discoverability — catalog of MCP servers and tools; request access, docs, self-onboard.
- **Traffic management:** Quotas, rate limits; protect from overload.
- **Analytics:** Usage, latency, errors.
- **Composable MCP:** Multiple domain-specific MCP servers on Cloud Run; Apigee unifies via API products and policies.

## End-to-end flow

1. **Onboarding:** Developer uses **Apigee Developer Portal** → creates App, subscribes to API Product(s), gets API key/token (defines allowed tools and policies).
2. **MCP client → Apigee** with API key/token.
3. **Apigee:** Validates credential; resolves API Product and `allowed_tools`.
4. **tools/list:** Request forwarded to Cloud Run MCP server; Apigee **filters** returned tool list by `allowed_tools`.
5. **Tool invocation:** Apigee checks tool name vs `allowed_tools`; if not allowed → access denied; if allowed → forward to Cloud Run; MCP server gets credentials from **Secret Manager**, runs tool, returns response; Apigee can enforce quotas/rate limits before returning to client.

## Demo (Shopify MCP)

- MCP server on **Cloud Run**; four tools (e.g. `search_products`, `get_product_details`, `optimize_product`, `duplicate_and_optimize`).
- **Apigee** exposes with API key auth and tool-level access: Basic tier `allowed_tools: "search_products,get_product_details"` (read-only); Advanced tier `allowed_tools: "*"` (all tools). Basic user’s `tools/list` shows only two tools; `optimize_product` blocked by Apigee.

## Reference

- **URL:** [Deploying and Securing MCP Servers with Google Cloud Run and Apigee](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)
- **Cited:** 2025-02-17
