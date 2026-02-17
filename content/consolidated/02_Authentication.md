# Authentication — Identity for Agents and Users

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Security pillar (governance):** *Least privilege* — only authorized entities can interact with the agent; agent can only access permitted resources ([Tahir — Four Pillars](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)). Every user and the agent should have bare minimum permissions.
- **Authentication model:** Humans vs agents/M2M; APIs.
- **Identity providers & SSO:** SSO, MFA.
- **API keys and service accounts:** Issuance, rotation, revocation; API key management, service principals.
- **Secrets management:** Where secrets live; access and rotation; secret management systems.

## Google Cloud

**Google services in this section:**

| Service | Purpose | Source |
|---------|---------|--------|
| **Identity Platform** | User sign-in (email/password, social, OIDC, SAML, MFA) | [Sign in with email](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) |
| **Google Cloud auth (docs)** | ADC, API keys, OAuth, service accounts, tokens, gcloud | [Authentication methods at Google](https://docs.cloud.google.com/docs/authentication) |
| **OAuth 2.0 (RFC 6749)** | Standard used by GCP/Identity Platform (authorization code, client credentials, etc.) | [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749) |
| **Vertex AI Agent Engine** | Per-agent identity (IAM principal), mTLS/CAA, least privilege for agents | [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) |
| **Google Cloud MCP servers** | MCP auth: ADC, IAM, Agent Engine agent identity, OAuth client ID, API keys, Workload Identity, Compute Engine | [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp) |
| **Cloud Run** | MCP server runtime; authenticated invocations; Secret Manager, Artifact Registry | [Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) |
| **Apigee** | MCP front door: API keys, OAuth 2.0, JWT, SSO; API Products + allowed_tools; Developer Portal; quotas/analytics | [Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) |
| **Cloud API Registry** | Centralized catalog of MCP servers; registry-first tools for Vertex AI Agent Engine; IAM (cloudapiregistry.viewer, mcp.toolUser); zero-boilerplate tool consumption | [Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) |
| **Apigee as LLM/agent gateway** | Gateway in front of Cloud Run (integration layer or **agent API server**); API Products, Key Value Map (encrypted credentials), Model Armor (prompt sanitization), Developer Portal; auth, rate limits, quotas | [Apigee LLM gateway Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4), [Part 2](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093) |
| **ADK / Vertex AI (agent identity)** | **Agent-Auth** (tool uses agent’s identity, e.g. service account; IAM on external systems) vs **User Auth** (tool uses controlling user’s identity, e.g. OAuth); log attribution | [ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) |

- **User authentication (humans):** **Identity Platform** — enable in Cloud Marketplace; configure Email/Password or other providers; use Firebase-compatible Web SDK (v9 modular or v8). Roles: Identity Platform Admin, Service Usage Admin ([Sign in with email](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email)).
- **Google Cloud APIs and workloads:** **Authentication methods** — choose by environment: single-user dev (user creds or impersonation), running in GCP (attach service account or Workload Identity for GKE), on-prem/other cloud (Workload Identity Federation or service account key). **ADC** finds credentials automatically; prefer attached service account over keys ([Authentication methods at Google](https://docs.cloud.google.com/docs/authentication)).
- **OAuth 2.0:** Google APIs implement OAuth 2.0; use authorization code or client credentials; limit **scope** for least privilege ([RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749)).
- **Agents (how Google addresses/handles agent auth):** **Vertex AI Agent Engine** — **agent identity** = per-agent IAM principal (`principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID`); SPIFFE-based; CAA + mTLS binding to reduce credential theft. Grant/deny via IAM or Principal Access Boundary. Third-party: store OAuth client secret or API keys in **Secret Manager**; restrict access by agent identity; agent uses ADC to read secrets at runtime. Logs show agent (and user) identity ([Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)).
- **Secrets:** **Secret Manager** — store API keys and OAuth credentials; grant `secretmanager.secretAccessor` to service account or **agent identity**.
- **MCP (Model Context Protocol) authentication:** Google Cloud MCP servers use HTTP-based MCP auth. **Google services tagged for MCP:** [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp) — **ADC**, **IAM**, **Vertex AI Agent Engine** (agent identity), **OAuth client ID** (Google API Console), **API keys** (APIs & Services > Credentials), **Workload Identity Federation**, **Compute Engine** (service account attach), **gcloud**. Production: use **service account** or **agent identity** (Agent Engine); or **OAuth client ID** for app-on-behalf-of-user; API keys only for services that don’t require IAM principal ([Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp)). **MCP OAuth (general):** Discovery (`.well-known/oauth-protected-resource`, `.well-known/oauth-authorization-server`), Dynamic Client Registration, PKCE, token exchange — pattern applies to any MCP server; no Google-specific services in [Upstash MCP OAuth](https://upstash.com/blog/mcp-oauth-implementation). **MCP at scale (Google):** **Cloud Run** (MCP server runtime), **Apigee** (front door: API keys, OAuth 2.0, JWT, SSO; API Products with `allowed_tools` for tool-level access; Developer Portal; quotas/rate limits), **Secret Manager** (credentials for MCP server), **Artifact Registry** (containers); IAM: grant **Cloud Run Invoker** only to Apigee’s service account ([Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)). **Cloud API Registry (MCP catalog and governance):** **Cloud API Registry** is the centralized catalog of MCP servers for Vertex AI Agent Engine; “registry-first” workflow: discover via `gcloud beta api-registry mcp servers list`, enable server (e.g. bigquery.googleapis.com), agent gets tools (e.g. execute_sql, list_dataset_ids) with **zero boilerplate** via ADK **ApiRegistry** and `get_toolset(mcp_server_name)`; **unified security** via IAM — grant Agent Engine service account `roles/cloudapiregistry.viewer`, `roles/mcp.toolUser`, and data roles (e.g. `roles/bigquery.user`, `roles/bigquery.dataViewer`); no manual secrets or token passing ([Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)).
- **Console sign-in flow:** Access to **Cloud Console** (e.g. [solution pages](https://console.cloud.google.com/products/solutions/details/ecommerce-platform-serverless)) requires **Google Account** sign-in (`accounts.google.com` with `service=cloudconsole`); after auth, user is redirected to the requested console URL. Deploying console solutions (e.g. eCommerce Platform Serverless) uses **IAM** (e.g. Service Usage Admin, Service Account Admin, Project IAM Admin, Cloud Infrastructure Manager Admin, Service Account User); deployment runs as a temporary **service account** with scoped roles ([eCommerce serverless flow and services](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless)).
- **Apigee as LLM or agent API gateway (not only plain LLM):** Apigee can front **plain LLM calls** or an **agent API server** built on **Google Cloud Run**. Pattern: client → **Apigee** (auth, API Products, rate limits, quotas, **Key Value Map** for backend credentials, optional **Model Armor** prompt sanitization) → **Cloud Run** (integration layer in the PoC, or **agent API server** e.g. MCP/custom agent). **Google services:** Apigee, **Cloud Run**, **Vertex AI / Gemini** (or other LLMs), **Model Armor**, **Apigee Key Value Map**, **Apigee Developer Portal**. Same governance (analytics, monetization, security) applies to agent APIs on Cloud Run ([Apigee LLM gateway Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4); [Part 2 implementation](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093)).
- **ADK agent identity (Vertex AI):** **Agent-Auth** — tool acts with agent’s identity (e.g. service account); authorize agent in external systems (e.g. DB IAM); constrains actions to developer intent; use when all users share same access; ensure logs attribute actions to users. **User Auth** — tool acts with controlling user’s identity (e.g. OAuth from frontend); agent only does what user could do; reduces abuse; OAuth scopes often broader than needed — combine with guardrails ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).
- **Full service list:** (see [09_Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))

## AWS

<!-- Placeholder: fill when AWS-specific content is ingested -->
- **Services:** (see Hyperscaler_Cloud_Service_Mapping)
- _To be filled._

## Azure

<!-- Placeholder: fill when Azure-specific content is ingested -->
- **Services:** (see Hyperscaler_Cloud_Service_Mapping)
- _To be filled._

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | What is AI Agent Governance? The Four Pillars (Security) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Sign in a user with an email (Identity Platform) | [Google Cloud](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) | `content/ingested/authentication/gcp-identity-platform-sign-in-email.md` |
| 3 | Authentication methods at Google | [Google Cloud](https://docs.cloud.google.com/docs/authentication) | `content/ingested/authentication/gcp-authentication-methods.md` |
| 4 | RFC 6749 — OAuth 2.0 Authorization Framework | [IETF](https://datatracker.ietf.org/doc/html/rfc6749) | `content/ingested/authentication/rfc6749-oauth2-authorization-framework.md` |
| 5 | Use agent identity with Vertex AI Agent Engine | [Google Cloud](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) | `content/ingested/authentication/gcp-vertex-ai-agent-engine-agent-identity.md` |
| 6 | Authenticate to Google and Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/mcp/authenticate-mcp) | `content/ingested/authentication/gcp-mcp-authenticate-mcp.md` |
| 7 | Implementing MCP OAuth (Upstash; general pattern) | [Upstash](https://upstash.com/blog/mcp-oauth-implementation) | `content/ingested/authentication/upstash-mcp-oauth-implementation.md` |
| 8 | Deploying and Securing MCP Servers with Cloud Run and Apigee | [Medium](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) | `content/ingested/authentication/apigee-cloud-run-mcp-servers-securing.md` |
| 9 | eCommerce Platform Serverless — sign-in flow and GCP services | [Google Cloud](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless) | `content/ingested/governance/gcp-ecommerce-platform-serverless-flow-and-services.md` |
| 10 | Deploy agent with Cloud API Registry on Vertex AI Agent Engine | [Google Developer forums](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) | `content/ingested/authentication/gcp-cloud-api-registry-mcp-vertex-ai-agent-engine.md` |
| 11 | Apigee as LLM gateway (Part 1) — applies to agent API server on Cloud Run | [Medium](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4) | `content/ingested/governance/apigee-llm-gateway-cloud-run-part1.md` |
| 12 | Apigee as LLM gateway — Implementation walkthrough (Part 2) | [Medium](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093) | Part 2: setup, API proxy, Cloud Run; see Part 1 ingested doc for Part 2 URL |
| 13 | Safety and Security for AI Agents (ADK) | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
