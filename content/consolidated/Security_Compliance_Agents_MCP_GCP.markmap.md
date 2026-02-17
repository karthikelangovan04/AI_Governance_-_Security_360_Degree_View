---
title: markmap
markmap:
  colorFreezeLevel: 2
---

# Security & Compliance: Securing Agents & MCP on GCP

## What authentication means

- **Authentication** == proving **who** (or what) is making a request
- **Credentials** (password, OAuth token, API key, service account) → validated → **tokens** (access, refresh, ID)
- **Authorization** (separate) == *what* that identity is allowed to do — least privilege
- **Google:** OAuth 2.0, **IAM**, **Identity Platform**, **ADC**, **Vertex AI Agent Engine**, **Apigee**
- **Implement:** Validate credentials server-side; issue scoped tokens; enforce IAM roles. [Authentication methods at Google](https://docs.cloud.google.com/docs/authentication)

## 360° view: User login & authentication types (GCP)

### Human users — sign-in

- **Identity Platform**
  - Email/password, social, OIDC, SAML, MFA
  - Firebase-compatible Web SDK v9 / v8
  - IAM: Identity Platform Admin, Service Usage Admin
  - **Implement:** Enable in Cloud Marketplace → Identity Providers → Add Email/Password (or social/OIDC/SAML); in app use `signInWithEmailAndPassword(auth, email, password)` (v9) or `firebase.auth().signInWithEmailAndPassword()` (v8). [Sign in with email (Identity Platform)](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) · [Identity Platform](https://cloud.google.com/identity-platform)
- **Google Account**
  - Cloud Console sign-in (`accounts.google.com` with `service=cloudconsole`)
  - **Implement:** Redirect users to Google sign-in with `service=cloudconsole` and `continue` URL; no extra SDK for console-only. [Cloud Console](https://console.cloud.google.com/)
- **OAuth 2.0 (3LO)**
  - App on behalf of user — **OAuth client ID** (Google API Console)
  - Limit **scopes** for least privilege
  - **Implement:** Create OAuth client ID in APIs & Services → Credentials; use authorization code flow + PKCE; request minimal scopes. [OAuth 2.0 credentials](https://developers.google.com/identity/protocols/oauth2) · [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749)

### Workloads & APIs (non-human)

- **Application Default Credentials (ADC)**
  - Credential discovery by environment
  - **Implement:** Use client libraries (no explicit key in code); set up ADC per env: local `gcloud auth application-default login`, GCP attach SA to resource. [Authentication methods](https://docs.cloud.google.com/docs/authentication)
- **Running in GCP**
  - **IAM** Service Accounts attached to **Cloud Run**, **GKE**, **Compute Engine**
  - Metadata server for tokens — no keys on disk
  - **Implement:** Create SA with `gcloud iam service-accounts create`; grant roles; attach to Cloud Run (`--service-account=SA_EMAIL`), GKE (Workload Identity), or Compute Engine (`--service-account=SA_EMAIL`). [IAM](https://cloud.google.com/iam/docs) · [Cloud Run identity](https://cloud.google.com/run/docs/securing/service-identity)
- **On-prem / other cloud**
  - **Workload Identity Federation** or (last resort) **Service account key**
  - **Implement:** Configure workload identity pool + provider; use ADC with workload identity; avoid keys. [Set up ADC on-prem or another cloud](https://cloud.google.com/docs/authentication/set-up-adc-on-premises-or-another-cloud-provider) · [Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation)
- **Single-user dev**
  - **gcloud** `auth application-default login` or **impersonation** `--impersonate-service-account`
  - **Implement:** Run `gcloud auth application-default login`; or `gcloud auth application-default login --impersonate-service-account=SA_EMAIL` for impersonation. [Authentication methods](https://docs.cloud.google.com/docs/authentication)

### Agents — per-agent identity

- **Vertex AI Agent Engine**
  - `identity_type: AGENT_IDENTITY` — per-agent IAM principal
  - SPIFFE-based; **CAA** + **mTLS** — certificate-bound tokens, reduce credential theft
  - **Implement:** Create Agent Engine instance with `identity_type: AGENT_IDENTITY` (Vertex AI SDK v1beta1 or config); deploy with `identity_type=types.IdentityType.AGENT_IDENTITY`. [Use agent identity with Vertex AI Agent Engine](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) · [Vertex AI Agent Builder](https://cloud.google.com/vertex-ai/agent-builder)
- **IAM**
  - Allow/deny by agent principal or principal set
  - **Principal Access Boundary (PAB)** to limit resources
  - **Implement:** Grant roles to `principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID` or principal set; add deny/PAB to cap scope. [Agent identity (IAM, PAB)](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Third-party (OAuth / API keys)**
  - **Secret Manager** — store client secret or API key
  - IAM `secretmanager.secretAccessor` for **agent identity**
  - Agent uses **ADC** to read at runtime
  - **Implement:** Create secret in Secret Manager; grant agent principal `roles/secretmanager.secretAccessor`; in agent use ADC + Secret Manager client to read at runtime. [Secret Manager](https://cloud.google.com/secret-manager/docs)
- **Cloud Logging**
  - Logs show **agent identity** and **user identity** when agent acts on user's behalf
  - **Implement:** Use default Cloud Logging; agent identity and user (if present) appear in log entries. [Cloud Logging](https://cloud.google.com/logging/docs)

### MCP (Model Context Protocol)

- **Google Cloud MCP servers**
  - HTTP-based MCP auth; production: **service account** or **agent identity** (Agent Engine)
  - Or **OAuth client ID** for app-on-behalf-of-user; **API keys** for services without IAM
  - **Implement:** Supply credentials to MCP client (ADC for SA/agent identity; OAuth client for user-delegated; API key from Secret Manager for non-IAM). [Authenticate to Google and Google Cloud MCP servers](https://docs.cloud.google.com/mcp/authenticate-mcp) · [Google Cloud MCP](https://cloud.google.com/mcp)
- **MCP at scale**
  - **Apigee** — front door: API keys, OAuth 2.0, JWT, SSO; **API Products** + **allowed_tools**
  - **Cloud Run** — MCP server runtime; **Cloud Run Invoker** only to **Apigee** SA
  - **Secret Manager** (credentials); **Artifact Registry** (containers)
  - **Implement:** Deploy MCP server container to Cloud Run (authenticated only); create Apigee API proxy → Cloud Run; grant only Apigee SA `roles/run.invoker`; in Apigee define API Products with `allowed_tools`; developers get key from Developer Portal. [Deploying and Securing MCP Servers with Cloud Run and Apigee](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) · [Cloud Run](https://cloud.google.com/run) · [Apigee](https://cloud.google.com/apigee)
- **Cloud API Registry**
  - Centralized MCP catalog; `gcloud beta api-registry mcp servers list/enable`
  - IAM: `cloudapiregistry.viewer`, `mcp.toolUser`; **zero boilerplate** tool consumption via **ADK** ApiRegistry
  - **Implement:** Run `gcloud beta api-registry mcp servers list --all`; `gcloud beta api-registry mcp enable bigquery.googleapis.com`; grant Agent Engine SA `roles/cloudapiregistry.viewer`, `roles/mcp.toolUser`, plus data roles; in ADK use `ApiRegistry(PROJECT_ID).get_toolset(mcp_server_name)` and pass to LlmAgent. [Deploy agent with Cloud API Registry on Vertex AI Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)

## Frontend to backend: security retained

- **Frontend → gateway**
  - Client sends **token or API key** only (never raw passwords); HTTPS
  - **Identity Platform** (ID/access tokens); **Apigee** (API key, OAuth 2.0, JWT)
  - **Implement:** Frontend gets ID/access token from Identity Platform or OAuth; send in `Authorization` header to Apigee; Apigee VerifyAPIKey / OAuthV2 / VerifyJWT policies. [Identity Platform](https://cloud.google.com/identity-platform/docs) · [Apigee auth](https://cloud.google.com/apigee/docs/api-platform/security/oauth/overview)
- **Gateway**
  - **Apigee** — validates credential; API Products; **allowed_tools** for MCP; rate limits, quotas; optional **Model Armor**
  - **Implement:** Create API proxy with auth policies; attach to API Product; for MCP add custom attribute `allowed_tools` and filter tools/list + tool calls. [Apigee](https://cloud.google.com/apigee/docs)
- **Gateway → backend**
  - **Cloud Run** — only **Apigee** service account has **Cloud Run Invoker**; no public anonymous access
  - **Implement:** Remove `allUsers` from Cloud Run IAM; grant only `roles/run.invoker` to Apigee’s service account. [Cloud Run IAM](https://cloud.google.com/run/docs/securing/ingress)
- **Backend credentials**
  - **Apigee Key Value Map** (encrypted) or **Secret Manager** — backend never sees user password
  - **Implement:** Store LLM/API keys in Apigee Key Value Map (encrypted) and read in proxy; or Cloud Run reads from Secret Manager at runtime. [Apigee Key Value Map](https://cloud.google.com/apigee/docs/api-platform/reference/policies/key-value-map-operations-policy) · [Secret Manager](https://cloud.google.com/secret-manager/docs)
- **Agent API server**
  - Same pattern: **Apigee** + **Cloud Run** + **Vertex AI / Gemini**; **Model Armor** optional
  - **Implement:** Deploy agent API (e.g. FastAPI/Flask) to Cloud Run; Apigee proxy to Cloud Run URL; optional Model Armor policy on request. [Apigee as LLM/agent gateway Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4) · [Part 2 implementation](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093)
- **Audit**
  - **Cloud Logging**, **Cloud Trace**, **Apigee** analytics, **Vertex AI** evaluation and tracing
  - **Implement:** Enable Cloud Logging on Cloud Run; use Cloud Trace for requests; enable Apigee analytics; use Vertex AI evaluation/tracing for agent runs. [Cloud Logging](https://cloud.google.com/logging/docs) · [Cloud Trace](https://cloud.google.com/trace/docs) · [ADK Evaluate](https://github.com/google/adk-docs/blob/main/docs/evaluate/index.md)

## VPC securing (network & perimeter)

- **VPC Service Controls (VPC-SC)**
  - Confine agent and API activity inside a **service perimeter**; API calls only to resources within the perimeter; reduces **data exfiltration** and impact radius
  - **Implement:** Create an **access context** (optional: add access levels, e.g. device/region); create **service perimeter** (regular or bridge); add **projects** to the perimeter; add **restricted APIs** (e.g. Vertex AI, BigQuery, Cloud Storage) so only calls from inside the perimeter or allowed VPCs succeed. Run agents and MCP in projects inside the perimeter. [VPC Service Controls overview](https://cloud.google.com/vpc-service-controls/docs/overview) · [Create a service perimeter](https://cloud.google.com/vpc-service-controls/docs/create-service-perimeter) · [ADK Safety — VPC-SC](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)
- **VPC network (private & ingress)**
  - Keep agent/MCP workloads off public internet; use **Private Google Access** for GCP APIs; restrict **ingress** to Cloud Run / GKE to VPC only or to load balancer
  - **Implement:** Deploy Cloud Run with **VPC connector** or **Direct VPC egress**; set **ingress** to "Internal and Cloud Load Balancing" (no `allUsers`). For GKE use **private cluster** and **authorized networks**. Use **Cloud Armor** (optional) in front of load balancer. [Cloud Run VPC egress](https://cloud.google.com/run/docs/configuring/vpc-direct-vpc) · [Cloud Run ingress](https://cloud.google.com/run/docs/securing/ingress) · [VPC overview](https://cloud.google.com/vpc/docs/overview)
- **Why for agents & MCP**
  - VPC-SC ensures agent tool calls and MCP server traffic stay within your perimeter; even if credentials leak, data cannot be exfiltrated to projects/APIs outside the perimeter. VPC keeps workloads in your network and limits exposure.

## How agents are authenticated (all methods)

- **Agent identity (Agent Engine)** — production on Vertex AI; **Vertex AI Agent Engine**, **IAM**, **Secret Manager**
  - **Implement:** Deploy agent with `identity_type: AGENT_IDENTITY`; grant IAM to agent principal; store third-party creds in Secret Manager, access via ADC. [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Service account (attached)** — agent on Cloud Run/GKE/Compute Engine; **IAM** SA + **ADC**
  - **Implement:** Create SA, attach to Cloud Run/GKE/VM; app uses default credentials (metadata server). [Authentication methods](https://docs.cloud.google.com/docs/authentication)
- **Service account (impersonation)** — dev/test; **gcloud** / **ADC** `--impersonate-service-account`
  - **Implement:** `gcloud config set auth/impersonate_service_account SA_EMAIL` or `gcloud auth application-default login --impersonate-service-account=SA_EMAIL`. [Authentication methods](https://docs.cloud.google.com/docs/authentication)
- **OAuth client ID** — app on behalf of user; **Google API Console**; **Identity Platform** / OAuth flows
  - **Implement:** Create OAuth 2.0 client in Console; implement auth code flow in app; pass tokens to backend. [OAuth 2.0](https://developers.google.com/identity/protocols/oauth2)
- **API key** — services without IAM; **APIs & Services > Credentials**; **Secret Manager**
  - **Implement:** Create API key in Console; restrict by app/API; store in Secret Manager; app reads at runtime. [API key best practices](https://cloud.google.com/docs/authentication/api-keys)
- **User credentials (ADC)** — local dev only; **gcloud auth application-default login**
  - **Implement:** Run `gcloud auth application-default login`; use same code path as prod (ADC). [Authentication methods](https://docs.cloud.google.com/docs/authentication)
- **Tool-level (ADK)**
  - **Agent-Auth** — tool uses agent identity; **Cloud Logging** for attribution
  - **User Auth** — tool uses user OAuth; combine with **ADK** guardrails, **Tool Context**, **Gemini** safety
  - **Implement:** For Agent-Auth authorize agent principal in external system (e.g. DB IAM). For User Auth pass OAuth token from frontend; use ADK Before Tool Callback + Tool Context for guardrails. [ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)

## How MCP is authenticated & connects to agents securely

### MCP client auth (Google)

- **Agent identity** — **Vertex AI Agent Engine**
  - **Implement:** Deploy agent on Agent Engine with agent identity; MCP client uses same identity (ADC). [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp)
- **Service account** — **IAM** SA attached to Cloud Run / GKE / Compute Engine; **ADC**
  - **Implement:** Attach SA to compute resource; MCP client uses ADC (metadata server). [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp)
- **OAuth client ID** — **Google API Console**
  - **Implement:** Create OAuth client; app obtains tokens on behalf of user; pass to MCP client. [OAuth 2.0](https://developers.google.com/identity/protocols/oauth2)
- **API key** — **APIs & Services**; **Secret Manager**; **Apigee** if gateway
  - **Implement:** Create key, restrict, store in Secret Manager or configure in Apigee. [API keys](https://cloud.google.com/docs/authentication/api-keys)

### MCP server side — who can call

- **Apigee in front**
  - Only valid **Apigee** credential (API key, OAuth, JWT); **allowed_tools** enforced
  - **Cloud Run** (MCP server); **IAM**: only Apigee SA has **Cloud Run Invoker**
  - **Implement:** Apigee validates credential and `allowed_tools`; forwards to Cloud Run; Cloud Run IAM allows only Apigee SA. [Deploying and Securing MCP with Cloud Run and Apigee](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)
- **Cloud API Registry**
  - Agent gets tools via registry; auth = **Agent Engine** SA + **IAM** (registry + data roles e.g. **BigQuery**)
  - **Implement:** Grant Agent Engine SA `cloudapiregistry.viewer`, `mcp.toolUser`, and e.g. `bigquery.user`, `bigquery.dataViewer`; no MCP server URL or tokens in code. [Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)
- **Direct MCP**
  - **Google Cloud MCP servers**; **ADC** / **Agent Engine** credentials
  - **Implement:** MCP client sends request with ADC or agent-identity token; MCP server validates. [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp)

### End-to-end: agent ↔ MCP secure path

1. **Agent** on **Vertex AI Agent Engine** or **Cloud Run** — **agent identity** or **service account**
   - [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) · [Cloud Run](https://cloud.google.com/run)
2. **Agent** discovers tools via **Cloud API Registry** or calls **Apigee**-protected MCP with API key / OAuth
   - [Cloud API Registry](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) · [Apigee MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)
3. **Apigee** validates → **API Product** + **allowed_tools** → forward to **Cloud Run** (only Apigee can invoke)
   - [Apigee](https://cloud.google.com/apigee/docs)
4. **MCP server** on **Cloud Run** — **Secret Manager** for backend credentials
   - [Secret Manager](https://cloud.google.com/secret-manager/docs)
5. **Registry-first:** **ADK** ApiRegistry `get_toolset(mcp_server_name)`; **IAM** `cloudapiregistry.viewer`, `mcp.toolUser`, data roles
   - [Cloud API Registry + ADK](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)

## Role-based access (RBAC) for agents & MCP tools

### RBAC for agents

- **Per-agent IAM**
  - Grant different **roles** to each agent principal `principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID` (e.g. Agent A = bigquery.dataViewer only; Agent B = bigquery.user + cloudapiregistry.viewer + mcp.toolUser)
  - **Implement:** Create agent with agent identity; run `gcloud projects add-iam-policy-binding PROJECT --member="principal://agents.global..../reasoningEngines/AGENT_ID" --role="roles/bigquery.dataViewer"` (or custom role). [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Principal set (all agents in project)**
  - One role for all agents: `principalSet://agents.global..../attribute.platformContainer/aiplatform/projects/PROJECT_NUMBER`
  - **Implement:** Bind role to principal set instead of single principal. [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Custom roles**
  - Define “Agent Data Analyst” etc. (viewer + registry + mcp.toolUser + bigquery.dataViewer); bind to agent principal(s)
  - **Implement:** Create custom role with `gcloud iam roles create` or Console; bind to agent principal. [IAM custom roles](https://cloud.google.com/iam/docs/creating-custom-roles)
- **Principal Access Boundary (PAB)** / **IAM conditions**
  - Cap resources (e.g. only certain buckets/datasets) or use conditions (e.g. resource tag)
  - **Implement:** Add PAB to agent or use conditional role binding. [Agent identity (PAB)](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) · [IAM conditions](https://cloud.google.com/iam/docs/conditions-overview)
- **User-context roles (agent acts as user)**
  - Map **Identity Platform** custom claims or **Apigee** API Product to user role; enforce in app or **ADK** Before Tool Callback
  - **Implement:** Set role in session state from IdP; in Before Tool Callback allow/deny tool or params by role. [ADK Safety](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)

### RBAC for MCP tools

- **Apigee API Products + allowed_tools**
  - Each **API Product** = role; set **allowed_tools** (comma-separated or `*`). Client key → only those tools in tools/list; Apigee blocks others
  - **Implement:** Create API Product; add custom attribute `allowed_tools` (e.g. "search_products,get_product_details"); in proxy filter tools/list and validate tool name on invoke. [Apigee API Products](https://cloud.google.com/apigee/docs/api-platform/publish/creating-api-products) · [Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)
- **Cloud API Registry — different SAs per role**
  - Different **Agent Engine** SAs (or agent identities) per role; grant Analyst SA only viewer + mcp.toolUser + bigquery.dataViewer; Writer SA adds bigquery.user
  - **Implement:** Create separate SAs; grant each only the registry + data roles for that role; deploy agents with the SA for their role. [Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)
- **Cloud API Registry — IAM conditions**
  - One SA with **IAM conditions** on resources (e.g. only datasets in folder X)
  - **Implement:** Add condition to role binding (e.g. resource.name.startsWith("projects/_/datasets/analytics_")). [IAM conditions](https://cloud.google.com/iam/docs/conditions-overview)
- **ADK Tool Context / Before Tool Callback**
  - Store role in session; in **Tool Context** or **Before Tool Callback** allow/deny tool or parameters by role
  - **Implement:** Read role from session state; in callback compare tool name/args to allowed list for role; return error map to block. [ADK Safety (Tool Context, callbacks)](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)

### Google services for RBAC

- **Agent roles:** **IAM** (roles, custom roles, conditions, PAB), **Vertex AI Agent Engine**
- **MCP tool roles (gateway):** **Apigee** (API Products, allowed_tools)
- **MCP tool roles (registry):** **Cloud API Registry**, **IAM** (cloudapiregistry.viewer, mcp.toolUser, data roles)
- **User/context roles:** **Identity Platform** (custom claims), **Apigee** (API Product per group), **ADK** (Tool Context, Before Tool Callback)

## Best practices (Google ecosystem)

- **Least privilege** — **IAM** roles and **OAuth** scopes minimal; **PAB** for agents; **Apigee** **allowed_tools**; use **RBAC** (per-agent roles, API Products / allowed_tools for MCP)
  - [IAM](https://cloud.google.com/iam/docs) · [Agent identity (PAB)](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **No secrets in code** — **Secret Manager** (Agent Engine, Cloud Run); **Apigee Key Value Map**
  - [Secret Manager](https://cloud.google.com/secret-manager/docs) · [Apigee KVM](https://cloud.google.com/apigee/docs/api-platform/reference/policies/key-value-map-operations-policy)
- **Single entry point for MCP** — **Apigee** in front of **Cloud Run** MCP; **Cloud Run Invoker** only for Apigee SA
  - [Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)
- **Agent identity in production** — **Vertex AI Agent Engine** `AGENT_IDENTITY`; CAA/mTLS
  - [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Registry-first** — **Cloud API Registry** + **IAM**; no manual MCP URLs or per-tool secrets
  - [Cloud API Registry](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)
- **Guardrails** — **ADK** (Tool Context, Before Tool Callback, plugins); **Gemini** content filters, system instructions; **Model Armor**; **VPC-SC**; escape model output in UIs
  - [ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) · [Vertex AI](https://cloud.google.com/vertex-ai/generative-ai/docs/overview) · [VPC Service Controls](https://cloud.google.com/vpc-service-controls/docs)
- **VPC securing** — Put agent and MCP projects inside a **VPC Service Controls** perimeter; use **VPC** (private ingress, no public egress) for Cloud Run/GKE running agents and MCP
  - [VPC Service Controls](https://cloud.google.com/vpc-service-controls/docs/overview) · [Create service perimeter](https://cloud.google.com/vpc-service-controls/docs/create-service-perimeter) · [Cloud Run ingress](https://cloud.google.com/run/docs/securing/ingress)
- **Audit everything** — **Cloud Logging**, **Cloud Trace**, **Apigee** analytics, **Vertex AI** evaluation and tracing
  - [Cloud Logging](https://cloud.google.com/logging/docs) · [Cloud Trace](https://cloud.google.com/trace/docs) · [ADK Evaluate](https://github.com/google/adk-docs/blob/main/docs/evaluate/index.md)
- **User vs agent** — **User Auth** (OAuth) when users have different access; **Agent-Auth** when same access; always log attribution
  - [ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)

## Google service by phase (summary)

### User sign-in

- **Identity Platform**, **IAM**, **Google Account**
  - [Identity Platform](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) · [IAM](https://cloud.google.com/iam/docs)

### Workload / API auth

- **ADC**, **IAM** (service accounts), **Workload Identity Federation**, **gcloud**, **Cloud Run**, **GKE**, **Compute Engine**
  - [Authentication methods](https://docs.cloud.google.com/docs/authentication) · [Workload Identity](https://cloud.google.com/iam/docs/workload-identity-federation)

### Agent identity

- **Vertex AI Agent Engine**, **IAM**, **CAA/mTLS**, **Secret Manager**, **Cloud Logging**
  - [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) · [Secret Manager](https://cloud.google.com/secret-manager/docs) · [Cloud Logging](https://cloud.google.com/logging/docs)

### MCP client auth

- **Google Cloud MCP servers**, **ADC**, **IAM**, **Vertex AI Agent Engine**, **OAuth client ID** (API Console), **API keys**, **Secret Manager**
  - [Authenticate to MCP](https://docs.cloud.google.com/mcp/authenticate-mcp)

### MCP server / gateway

- **Apigee**, **Cloud Run**, **Secret Manager**, **Artifact Registry**, **IAM** (Cloud Run Invoker)
  - [Apigee](https://cloud.google.com/apigee) · [Cloud Run](https://cloud.google.com/run) · [Artifact Registry](https://cloud.google.com/artifact-registry)

### MCP discovery / tools

- **Cloud API Registry**, **IAM** (cloudapiregistry.viewer, mcp.toolUser), **Vertex AI Agent Engine**, **ADK** (ApiRegistry)
  - [Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)

### RBAC (agents & MCP tools)

- **Agents:** **IAM** (per-agent principal, principal set, custom roles, PAB, conditions), **Vertex AI Agent Engine**; **Identity Platform** / **Apigee** / **ADK** for user-context roles
  - [IAM custom roles](https://cloud.google.com/iam/docs/creating-custom-roles) · [IAM conditions](https://cloud.google.com/iam/docs/conditions-overview) · [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **MCP tools:** **Apigee** (API Products, allowed_tools); **Cloud API Registry** + **IAM** (roles per SA/agent); **ADK** (Tool Context, Before Tool Callback)
  - [Apigee API Products](https://cloud.google.com/apigee/docs/api-platform/publish/creating-api-products) · [Cloud API Registry](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) · [ADK Safety](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)

### Frontend → backend

- **Identity Platform**, **Apigee**, **Cloud Run**, **Apigee Key Value Map**, **Model Armor**, **Cloud Logging**, **Cloud Trace**
  - [Apigee LLM gateway Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4) · [Part 2](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093)

### VPC & network securing

- **VPC Service Controls (VPC-SC)** — service perimeter; restrict APIs (Vertex AI, BigQuery, etc.); confine agent/MCP to perimeter to reduce data exfiltration
  - [VPC Service Controls overview](https://cloud.google.com/vpc-service-controls/docs/overview) · [Create service perimeter](https://cloud.google.com/vpc-service-controls/docs/create-service-perimeter)
- **VPC** — private ingress for Cloud Run/GKE; VPC connector or Direct VPC egress; optional Cloud Armor
  - [Cloud Run VPC](https://cloud.google.com/run/docs/configuring/vpc-direct-vpc) · [Cloud Run ingress](https://cloud.google.com/run/docs/securing/ingress) · [VPC](https://cloud.google.com/vpc/docs/overview)

### Safety & compliance

- **Vertex AI** (Gemini, evaluation, tracing), **ADK**, **Model Armor**, **VPC Service Controls**, **Vertex AI Code Execution API** / **Code Interpreter Extension**
  - [ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) · [Vertex AI](https://cloud.google.com/vertex-ai/generative-ai/docs/overview) · [Code Execution API](https://cloud.google.com/vertex-ai/generative-ai/docs/multimodal/code-execution-api) · [Code Interpreter](https://cloud.google.com/vertex-ai/generative-ai/docs/extensions/code-interpreter)
