# Security & Compliance: Securing Agents and MCP on Google Cloud

<!-- 360° view: user login, auth types, frontend→backend security, agent auth, MCP auth, best practices. All Google ecosystem; explicit GCP service per phase. -->

This document gives a **production-grade, 360° view** of securing AI agents and MCP (Model Context Protocol) on **Google Cloud**: what authentication means, how users log in and what auth methods are available, how security is retained from frontend to backend, how agents and MCP are authenticated, how MCP connects to agents securely, and best practices. **Every phase explicitly names the Google service(s) used.**

---

## 1. What authentication means

**Authentication** = proving **who** (or what) is making a request. It answers: *Is this the claimed user, app, or agent?*

- **Credentials** (e.g. password, OAuth token, API key, service account) are presented; the system validates them and issues **tokens** (access, refresh, ID tokens) that prove the identity was verified.
- **Authorization** (separate) = *what* that identity is allowed to do (roles, scopes, policies). Governance principle: **least privilege** — only authorized entities interact with the agent; agent only accesses permitted resources.

**Google ecosystem:** Authentication for Google Cloud uses and extends **OAuth 2.0**; identity and credentials are managed by **IAM**, **Identity Platform** (users), **Application Default Credentials (ADC)**, **Vertex AI Agent Engine** (agent identity), and **Apigee** (API/gateway auth).

---

## 2. 360° view: user login and authentication types on GCP

End-to-end view of **who** can sign in and **how**, and which **Google service** is used at each step.

### 2.1 Human users (sign-in)

| Phase | What happens | Google service |
|-------|----------------|----------------|
| **Sign-in (web/app)** | User enters email/password or uses social/OIDC/SAML; MFA optional | **Identity Platform** (Cloud Marketplace); Firebase-compatible Web SDK v9/v8 |
| **Identity providers** | Email/Password, Google, OIDC, SAML, MFA configured in one place | **Identity Platform** → Identity Providers |
| **Roles to enable** | Admin enables Identity Platform and configures providers | **IAM**: Identity Platform Admin, Service Usage Admin |
| **Console sign-in** | User accesses Cloud Console (e.g. solutions, Agent Engine) | **Google Account** (`accounts.google.com` with `service=cloudconsole`); **IAM** for deployment roles |

**Available authentication methods for human users (GCP):**

- **Email/password** — Identity Platform Email/Password provider.
- **Social sign-in** — Identity Platform (Google, etc.).
- **OIDC / SAML** — Identity Platform federated providers.
- **MFA** — Identity Platform (multi-factor).
- **Google Account** — For Cloud Console; no separate Identity Platform needed for console-only.
- **OAuth 2.0 (3LO)** — For “app on behalf of user”; **OAuth client ID** (Google API Console); tokens prove user delegated access; limit **scopes** for least privilege.

### 2.2 Workloads and APIs (non-human)

| Phase | What happens | Google service |
|-------|----------------|----------------|
| **Credential discovery** | Libraries find credentials automatically by environment | **Application Default Credentials (ADC)** |
| **Running in GCP** | Attach service account to resource (Cloud Run, GKE, Compute Engine); no keys on disk | **IAM** Service Accounts; **Cloud Run** / **GKE** / **Compute Engine** (metadata server for tokens) |
| **On-prem / other cloud** | No GCP metadata; use federation or (last resort) key | **Workload Identity Federation** or **Service account key** (avoid if possible) |
| **Single-user dev** | Local testing | **gcloud** (`gcloud auth application-default login`); or **impersonation** (`--impersonate-service-account`) |

### 2.3 Agents (per-agent identity)

| Phase | What happens | Google service |
|-------|----------------|----------------|
| **Agent identity** | Per-agent IAM principal; least privilege; tied to agent lifecycle | **Vertex AI Agent Engine** — `identity_type: AGENT_IDENTITY`; SPIFFE-based principal |
| **Credential binding** | Certificate-bound tokens; only usable from intended runtime (e.g. Cloud Run) | **Context-Aware Access (CAA)** + **mTLS** (Agent Engine) |
| **Access to GCP APIs** | Grant roles to agent principal or principal set | **IAM** allow/deny; **Principal Access Boundary (PAB)** to cap scope |
| **Third-party (OAuth/API keys)** | Store client secret or API key; agent reads at runtime | **Secret Manager**; IAM `secretmanager.secretAccessor` for **agent identity**; agent uses **ADC** to read |
| **Logging** | Who did what (agent and, when applicable, user) | **Cloud Logging** — logs show agent identity and user identity |

### 2.4 MCP (Model Context Protocol)

| Phase | What happens | Google service |
|-------|----------------|----------------|
| **MCP client → MCP server auth** | AI app (agent) authenticates to remote MCP server with supplied method | **Google Cloud MCP servers** (product); HTTP-based MCP auth |
| **Production identity** | Separate identity for the app; minimal permissions | **Service account** (attached to Compute Engine, Cloud Run, GKE) or **Vertex AI Agent Engine agent identity** |
| **App on behalf of user** | No shared user credentials; tokens only | **OAuth client ID** (Google API Console) |
| **Services without IAM** | e.g. Google Maps | **API keys** (APIs & Services > Credentials); restrict by app and API |
| **MCP at scale (front door)** | Single entry point; auth, tool-level access, quotas | **Apigee** — API keys, OAuth 2.0, JWT, SSO; **API Products** with **allowed_tools** |
| **MCP server runtime** | Run MCP server in serverless containers | **Cloud Run** — authenticated invocations only; **Cloud Run Invoker** only to **Apigee** SA |
| **Registry-first (discovery)** | Central catalog; no per-tool secrets | **Cloud API Registry** — `gcloud beta api-registry mcp servers list/enable`; **IAM**: `cloudapiregistry.viewer`, `mcp.toolUser` |

---

## 3. Frontend to backend: how security is retained

Security is retained across the chain **user/browser → gateway → backend (agent/MCP)** by consistent auth, no credential leakage, and audit.

| Layer | How security is retained | Google service |
|-------|---------------------------|----------------|
| **Frontend → gateway** | Client sends only **token or API key** (never raw passwords); HTTPS everywhere | **Identity Platform** (ID/access tokens); **Apigee** (API key, OAuth 2.0, JWT validation) |
| **Gateway** | Validates credential; resolves product and scopes/tools; rate limits, quotas | **Apigee** — API Products, **allowed_tools** for MCP; optional **Model Armor** (prompt sanitization) |
| **Gateway → backend** | Backend invoked only by gateway; no public anonymous access | **Cloud Run** — **IAM**: only **Apigee** service account has **Cloud Run Invoker** |
| **Backend credentials** | Backend never sees user password; uses stored credentials for LLM/APIs | **Apigee Key Value Map** (encrypted); or **Secret Manager** (Cloud Run / Agent Engine) |
| **Agent API server** | Same pattern as “LLM gateway”: Apigee in front, Cloud Run hosts agent | **Apigee** + **Cloud Run** + **Vertex AI / Gemini**; **Model Armor** optional |
| **Audit** | Every call and tool use can be logged and traced | **Cloud Logging**; **Cloud Trace**; **Apigee** analytics; **Vertex AI** evaluation and tracing |

**Summary:** Frontend authenticates via **Identity Platform** or **OAuth**; **Apigee** enforces auth and tool access; **Cloud Run** accepts only **Apigee**; credentials live in **Secret Manager** or **Key Value Map**; **Cloud Logging** and **Apigee** provide audit.

---

## 4. How agents are authenticated (all possible methods)

Agents can be authenticated to Google Cloud and to MCP in these ways (all within Google ecosystem).

| Method | When to use | Google service |
|--------|-------------|----------------|
| **Agent identity (Agent Engine)** | Production agents on Vertex AI Agent Engine; per-agent principal, CAA/mTLS | **Vertex AI Agent Engine**; **IAM**; **Secret Manager** for third-party creds |
| **Service account (attached)** | Agent runs on Cloud Run, GKE, Compute Engine; no keys | **IAM** Service Account attached to resource; **ADC** |
| **Service account (impersonation)** | Dev/test; app acts as a service account | **gcloud** / **ADC** with `--impersonate-service-account` |
| **OAuth client ID** | App acts on behalf of end user; no credential sharing | **Google API Console** OAuth client ID; **Identity Platform** / OAuth flows |
| **API key** | Services that don’t require IAM principal (e.g. some external APIs) | **APIs & Services > Credentials**; **Secret Manager** to store; restrict by app/API |
| **User credentials (ADC)** | Local dev only | **gcloud auth application-default login** |

**Tool-level identity (ADK / Vertex AI):**

- **Agent-Auth:** Tool uses **agent’s identity** (e.g. service account); authorize agent in external systems (e.g. DB IAM); use when all users share same access; **Cloud Logging** for attribution.
- **User Auth:** Tool uses **controlling user’s identity** (e.g. OAuth from frontend); agent only does what user could do; combine with guardrails (**ADK** callbacks, **Tool Context**, **Gemini** safety).

**Google services by phase:** **Vertex AI Agent Engine**, **IAM**, **Secret Manager**, **ADC**, **Identity Platform** (if user-bound), **Apigee** (if agent API is behind gateway), **Cloud API Registry** (if using registry-first MCP tools).

---

## 5. How MCP is authenticated and connects to agents securely

### 5.1 MCP authentication methods (Google)

| Method | Use case | Google service |
|--------|----------|----------------|
| **Agent identity** | Agent on Agent Engine calling MCP | **Vertex AI Agent Engine** (agent identity); MCP client uses this identity |
| **Service account** | Agent on Cloud Run/GKE/Compute Engine calling MCP | **IAM** Service Account attached to resource; **ADC** |
| **OAuth client ID** | App-on-behalf-of-user; no shared user credentials | **Google API Console** OAuth client ID |
| **API key** | MCP/backends that don’t require IAM | **APIs & Services** API key; **Secret Manager**; **Apigee** (if gateway exposes key) |

### 5.2 MCP server side (who can call the MCP server)

| Pattern | Who can call | Google service |
|---------|--------------|----------------|
| **Apigee in front** | Only clients with valid **Apigee** credential (API key, OAuth, JWT); **allowed_tools** enforced | **Apigee** (front door); **Cloud Run** (MCP server); **IAM**: only Apigee SA has **Cloud Run Invoker** |
| **Cloud API Registry** | Agent gets tools via registry; no direct MCP server URL to manage; auth = **Agent Engine** service account + IAM for registry and data | **Cloud API Registry**; **Vertex AI Agent Engine**; **IAM** (`cloudapiregistry.viewer`, `mcp.toolUser`, data roles e.g. **BigQuery**) |
| **Direct MCP (no Apigee)** | Caller must present credentials accepted by **Google Cloud MCP** (e.g. ADC, agent identity, OAuth) | **Google Cloud MCP servers**; **ADC** / **Agent Engine** |

### 5.3 End-to-end: agent ↔ MCP securely

1. **Agent** runs on **Vertex AI Agent Engine** (or Cloud Run) with **agent identity** or **service account**.
2. **Agent** discovers MCP tools via **Cloud API Registry** (`gcloud beta api-registry mcp enable ...`) or calls **Apigee**-protected MCP endpoint with **API key / OAuth**.
3. **Apigee** validates credential, resolves **API Product** and **allowed_tools**, forwards only allowed tool calls to **Cloud Run** (MCP server); **Cloud Run** accepts only **Apigee** (Cloud Run Invoker).
4. **MCP server** on **Cloud Run** uses **Secret Manager** (or similar) for any backend credentials; no secrets in code.
5. **Registry-first:** Agent uses **ADK** **ApiRegistry** `get_toolset(mcp_server_name)`; **IAM** grants Agent Engine SA `cloudapiregistry.viewer`, `mcp.toolUser`, and data roles (e.g. **BigQuery**); no per-tool tokens.

**Google services in the path:** **Vertex AI Agent Engine**, **Cloud API Registry**, **IAM**, **Apigee**, **Cloud Run**, **Secret Manager**, **Artifact Registry** (MCP server image).

For **role-based access control (RBAC)** so different agents or users get different permissions and MCP tool sets, see section **6** below; see also [03_Authorization_Access_Control](03_Authorization_Access_Control.md) for the full RBAC narrative and [09_Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md) for the service mapping.

---

## 6. Role-based access control (RBAC) for agents and MCP tools

Within the Google ecosystem you can enforce **role-based access** so different agents (or users) get different permissions and MCP tool sets.

### 6.1 RBAC for agents

| Approach | How it works | Google service |
|----------|--------------|----------------|
| **Per-agent IAM** | Grant different **IAM roles** to each agent principal `principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID`. E.g. Agent A gets only `roles/bigquery.dataViewer`; Agent B gets `roles/bigquery.user` + `roles/cloudapiregistry.viewer` + `roles/mcp.toolUser`. | **IAM**, **Vertex AI Agent Engine** (agent identity) |
| **Principal set (all agents in project)** | Grant one role to all agents: `principalSet://agents.global..../attribute.platformContainer/aiplatform/projects/PROJECT_NUMBER`. Use when all agents in the project share the same “role.” | **IAM**, **Vertex AI Agent Engine** |
| **Custom roles** | Create a custom IAM role (e.g. “Agent Data Analyst” = viewer + Cloud API Registry viewer + mcp.toolUser + BigQuery dataViewer) and bind to agent principal(s) or principal set. | **IAM** ([Create custom roles](https://cloud.google.com/iam/docs/creating-custom-roles)) |
| **Principal Access Boundary (PAB)** | Cap what resources an agent can touch even if it has broad roles; e.g. limit to specific buckets or datasets. | **IAM** ([Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)) |
| **IAM conditions** | Grant role only when condition holds (e.g. resource tag, resource name prefix). E.g. allow BigQuery only for datasets in folder “analytics.” | **IAM** ([IAM conditions](https://cloud.google.com/iam/docs/conditions-overview)) |
| **User-context (agent acts as user)** | If agent uses User Auth (OAuth), map **Identity Platform** custom claims or **Apigee** API Product to “user role”; in app or **ADK** Before Tool Callback, allow only tools/APIs for that role. | **Identity Platform**, **Apigee**, **ADK** (callbacks) |

**Implement:** Create agent with agent identity; create custom role or pick predefined roles; bind to `principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID` (one agent) or `principalSet://...` (all in project). Add PAB or IAM condition if you need resource-level limits. For user-scoped roles, set role in session state and enforce in Before Tool Callback.

### 6.2 RBAC for MCP tools

| Approach | How it works | Google service |
|----------|--------------|----------------|
| **Apigee API Products + allowed_tools** | Each **API Product** = a “role.” Set **allowed_tools** (comma-separated tool names or `*`). Client gets key for a product → sees only those tools in `tools/list`; Apigee blocks calls to tools not in `allowed_tools`. E.g. “MCP Basic” = read-only tools; “MCP Advanced” = all tools. | **Apigee** ([API Products](https://cloud.google.com/apigee/docs/api-platform/publish/creating-api-products)); [Apigee + Cloud Run MCP](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) |
| **Cloud API Registry — different SAs per role** | Use different **Agent Engine service accounts** (or agent identities) per “role.” Grant Analyst SA: `cloudapiregistry.viewer`, `mcp.toolUser`, `bigquery.dataViewer`; Writer SA: add `bigquery.user`. Each agent runs with the SA that matches its role. | **Cloud API Registry**, **IAM**, **Vertex AI Agent Engine** ([Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)) |
| **Cloud API Registry — IAM conditions** | One SA with **IAM conditions** on resources (e.g. only datasets in a given folder or with a tag). | **IAM** ([Conditions](https://cloud.google.com/iam/docs/conditions-overview)) |
| **ADK Tool Context / Before Tool Callback** | Store user or agent “role” in session state; in **Tool Context** or **Before Tool Callback** allow or deny tool (or parameters) by role. Complements gateway/registry RBAC. | **ADK** ([ADK Safety — Tool Context, callbacks](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)) |

**Implement:** For gateway path: create API Products in Apigee with `allowed_tools` per “role”; issue keys per product. For registry path: create separate SAs or agent identities per role; grant only the registry + data roles needed for that role. Optionally add ADK callbacks to enforce role from session (e.g. from Identity Platform claims).

### 6.3 Google services for RBAC (agents and MCP)

| Purpose | Google services |
|---------|------------------|
| **Agent roles** | IAM (roles, custom roles, conditions, PAB), Vertex AI Agent Engine (agent identity) |
| **MCP tool roles (gateway)** | Apigee (API Products, allowed_tools) |
| **MCP tool roles (registry)** | Cloud API Registry, IAM (cloudapiregistry.viewer, mcp.toolUser, data roles per SA/agent) |
| **User/context roles** | Identity Platform (custom claims), Apigee (API Product per group), ADK (Tool Context, Before Tool Callback) |

---

## 7. When to use each security mechanism & service combinations

This section guides **when to use** each security mechanism and how to **combine services** for defense in depth.

### 7.1 Decision guidance: when to use each mechanism

| Security mechanism | When to use | Use case examples |
|-------------------|-------------|-------------------|
| **Identity Platform** | User-facing apps; need email/password, social, OIDC/SAML, MFA | Web app with user sign-in; multi-tenant SaaS |
| **Agent identity (Vertex AI Agent Engine)** | Production agents on Agent Engine; need per-agent IAM principal | Each agent has different permissions; audit per-agent actions |
| **Service account (attached)** | Agent/workload on Cloud Run/GKE/Compute Engine; no per-agent identity needed | Shared service account OK; agent runs on GCP compute |
| **Apigee (gateway)** | Need API management: auth, rate limits, quotas, tool-level access (`allowed_tools`), analytics | MCP at scale; multiple clients; need developer portal; tool-level RBAC |
| **Cloud API Registry** | Want centralized MCP catalog; registry-first workflow; no manual MCP URLs | Agent Engine agents; want unified discovery/governance; zero-boilerplate tool consumption |
| **Model Armor** | Need prompt injection/jailbreak protection; malicious URI filtering; content safety | Agent-only (AO) mode; user-provided prompts; MCP tool calls/responses |
| **VPC Service Controls** | Need data exfiltration prevention; confine API calls to perimeter | High-security workloads; compliance requirements; prevent data leakage |
| **ADK Tool Context / Before Tool Callback** | Need in-app policy enforcement; validate tool params; session/user role checks | Complement gateway/registry RBAC; enforce business rules; dynamic policy |
| **Gemini content filters** | Using Gemini models; need built-in content safety | Using Vertex AI Gemini; want non-configurable (CSAM, PII) + configurable filters |
| **Secret Manager** | Store API keys, OAuth secrets, credentials | Any service needs secrets; avoid hardcoding |
| **Cloud Logging / Trace** | Need audit trail; debugging; compliance | Always use; required for production |

### 7.2 Common service combinations (how services dock together)

#### Combination 1: Enterprise MCP at scale (Apigee + Cloud Run + Cloud API Registry)

**When:** Large organization; multiple MCP servers; need governance, tool-level RBAC, analytics.

**Services dock together:**
- **Apigee** (front door) → validates API key/OAuth, resolves API Product + `allowed_tools`, rate limits
- **Cloud Run** (MCP server runtime) → only Apigee SA can invoke (`roles/run.invoker`)
- **Cloud API Registry** (optional) → import MCP metadata from Apigee API hub; unified discovery
- **Secret Manager** → MCP server reads backend credentials
- **Cloud Logging** → audit Apigee + Cloud Run logs

**Flow:** Client → Apigee (auth + `allowed_tools`) → Cloud Run (MCP server) → backend APIs. Apigee filters `tools/list` and blocks unauthorized tool calls.

**Use this when:** You need tool-level access control, developer portal, quotas, analytics, and centralized MCP governance.

#### Combination 2: Registry-first with Agent Engine (Cloud API Registry + Vertex AI Agent Engine + IAM)

**When:** Agents on Vertex AI Agent Engine; want zero-boilerplate tool consumption; centralized governance.

**Services dock together:**
- **Cloud API Registry** → centralized MCP catalog; `gcloud beta api-registry mcp enable`
- **Vertex AI Agent Engine** → agent identity per agent; agents use ADK `ApiRegistry.get_toolset()`
- **IAM** → grant Agent Engine SA `cloudapiregistry.viewer`, `mcp.toolUser`, plus data roles (e.g. `bigquery.user`)
- **ADK** → `ApiRegistry(PROJECT_ID).get_toolset(mcp_server_name)` → tools available to agent

**Flow:** Admin enables MCP server in registry → Agent Engine SA has registry + data roles → Agent uses ADK ApiRegistry → gets tools without manual URLs/secrets.

**Use this when:** You use Agent Engine; want unified discovery; no per-tool secret management; IAM-based access.

#### Combination 3: Defense in depth (Model Armor + VPC-SC + Apigee + Agent identity)

**When:** High-security workloads; Agent-Only (AO) mode; need multiple layers.

**Services dock together:**
- **Model Armor** → sanitizes MCP tool calls/responses; prompt injection/jailbreak protection
- **VPC Service Controls** → service perimeter; confine API calls; prevent exfiltration
- **Apigee** → gateway auth, `allowed_tools`, rate limits
- **Vertex AI Agent Engine** (agent identity) → per-agent IAM; CAA/mTLS
- **Cloud Logging** → audit all layers

**Flow:** Agent (agent identity) → VPC-SC perimeter → Apigee (auth + `allowed_tools`) → Model Armor (sanitize) → MCP server → backend. All within VPC-SC perimeter.

**Use this when:** Critical data; Agent-Only mode; compliance requirements; need multiple security layers.

#### Combination 4: User-scoped access (Identity Platform + Apigee + ADK callbacks)

**When:** Agent acts on behalf of users; different users have different tool access.

**Services dock together:**
- **Identity Platform** → user sign-in; custom claims (user role)
- **Apigee** → API Products per user group; `allowed_tools` per product
- **ADK Before Tool Callback** → enforce role from session state; validate tool/params
- **Cloud Logging** → log user + agent identity

**Flow:** User signs in (Identity Platform) → gets API key for product (Apigee) → agent uses key → Apigee filters tools → ADK callback validates role → tool executes.

**Use this when:** Multi-user app; user-scoped permissions; need user attribution in logs.

#### Combination 5: Simple registry + Model Armor (Cloud API Registry + Model Armor)

**When:** Using Google Cloud MCP servers; want discovery + content safety; no gateway needed.

**Services dock together:**
- **Cloud API Registry** → discover/enable Google MCP servers
- **Model Armor** → enable MCP sanitization in floor setting; scan tool calls/responses
- **IAM** → grant registry + data roles to agent SA
- **Cloud Logging** → Model Armor detection logs

**Flow:** Enable MCP server in registry → enable Model Armor MCP protection → agent uses tools → Model Armor scans → blocks if malicious.

**Use this when:** Using Google Cloud MCP servers; want content safety; no custom gateway needed.

### 7.3 Decision tree: choosing your combination

**Start here:** What is your primary need?

1. **Need tool-level RBAC + developer portal + analytics?**
   - → Use **Combination 1** (Apigee + Cloud Run + Cloud API Registry)

2. **Agents on Vertex AI Agent Engine; want zero-boilerplate?**
   - → Use **Combination 2** (Cloud API Registry + Agent Engine + IAM)

3. **High-security; Agent-Only mode; compliance?**
   - → Use **Combination 3** (Model Armor + VPC-SC + Apigee + Agent identity)

4. **Multi-user app; user-scoped permissions?**
   - → Use **Combination 4** (Identity Platform + Apigee + ADK callbacks)

5. **Using Google Cloud MCP servers; simple setup?**
   - → Use **Combination 5** (Cloud API Registry + Model Armor)

**Then add layers as needed:**
- **Always add:** Cloud Logging (audit)
- **Add Model Armor if:** User-provided prompts, Agent-Only mode, or content safety required
- **Add VPC-SC if:** Data exfiltration prevention, compliance, or high-security workloads
- **Add Apigee if:** Need gateway features (rate limits, quotas, developer portal, tool-level RBAC)
- **Add Cloud API Registry if:** Want centralized MCP catalog or registry-first workflow

### 7.4 Google services summary by combination

| Combination | Core services | Complementary services |
|-------------|---------------|------------------------|
| **Enterprise MCP** | Apigee, Cloud Run, Cloud API Registry | Secret Manager, Cloud Logging, Artifact Registry |
| **Registry-first** | Cloud API Registry, Vertex AI Agent Engine, IAM | ADK (ApiRegistry), Cloud Logging |
| **Defense in depth** | Model Armor, VPC-SC, Apigee, Agent identity | Cloud Logging, Secret Manager |
| **User-scoped** | Identity Platform, Apigee, ADK | Cloud Logging, Secret Manager |
| **Simple registry** | Cloud API Registry, Model Armor | IAM, Cloud Logging |

---

## 8. Best practices (Google ecosystem)

- **Least privilege:** **IAM** roles and **OAuth** scopes minimal; **Principal Access Boundary (PAB)** for agents; **Apigee** **allowed_tools** for MCP.
- **No secrets in code:** **Secret Manager** (Agent Engine, Cloud Run); **Apigee Key Value Map** for gateway-managed backend keys.
- **Single entry point for MCP:** **Apigee** in front of **Cloud Run** MCP servers; **Cloud Run Invoker** only for Apigee SA.
- **Use agent identity in production:** **Vertex AI Agent Engine** with `identity_type: AGENT_IDENTITY`; CAA/mTLS; avoid shared service accounts for agents.
- **Registry-first when possible:** **Cloud API Registry** + **IAM** for discovery and tool access; no manual MCP URLs or per-tool secrets.
- **Guardrails beyond auth:** **ADK** (Tool Context, Before Tool Callback, plugins); **Gemini** content filters and system instructions; **Model Armor** at gateway or in ADK; **VPC Service Controls (VPC-SC)** for perimeter; escape model output in UIs.
- **Audit everything:** **Cloud Logging** (agent + user identity); **Cloud Trace**; **Apigee** analytics; **Vertex AI** evaluation and tracing.
- **User vs agent actions:** Prefer **User Auth** (OAuth) for tools when users have different access; use **Agent-Auth** only when all users share same access; always log attribution.

---

## 9. Google service summary by phase

| Phase | Google services |
|-------|------------------|
| **User sign-in** | Identity Platform, IAM (roles), Google Account (Console) |
| **Workload / API auth** | ADC, IAM (service accounts), Workload Identity Federation, gcloud, Cloud Run, GKE, Compute Engine |
| **Agent identity** | Vertex AI Agent Engine, IAM, CAA/mTLS, Secret Manager, Cloud Logging |
| **MCP client auth** | Google Cloud MCP servers, ADC, IAM, Vertex AI Agent Engine, OAuth client ID (API Console), API keys, Secret Manager |
| **MCP server / gateway** | Apigee (gateway, API Products, allowed_tools), Apigee API hub (register MCP APIs), Cloud Run, Secret Manager, Artifact Registry, IAM (Cloud Run Invoker) |
| **MCP discovery / tools** | Cloud API Registry (unified discovery, imports from Apigee API hub), Apigee API hub (register MCP APIs), IAM (cloudapiregistry.viewer, mcp.toolUser), Vertex AI Agent Engine, ADK (ApiRegistry) |
| **Frontend→backend** | Identity Platform, Apigee, Cloud Run, Apigee Key Value Map, Model Armor, Cloud Logging, Cloud Trace |
| **Safety & compliance** | Vertex AI (Gemini, evaluation, tracing), ADK, Model Armor (MCP sanitization, prompt injection/jailbreak protection), VPC-SC, Vertex Code Execution API / Code Interpreter |
| **RBAC (agents & MCP tools)** | IAM (roles, custom roles, conditions, PAB), Vertex AI Agent Engine, Apigee (API Products, allowed_tools), Cloud API Registry, ADK (Tool Context, Before Tool Callback), Identity Platform |

---

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | What is AI Agent Governance? The Four Pillars | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Sign in with email (Identity Platform) | [Google Cloud](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) | `content/ingested/authentication/gcp-identity-platform-sign-in-email.md` |
| 3 | Authentication methods at Google | [Google Cloud](https://docs.cloud.google.com/docs/authentication) | `content/ingested/authentication/gcp-authentication-methods.md` |
| 4 | Use agent identity with Vertex AI Agent Engine | [Google Cloud](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) | `content/ingested/authentication/gcp-vertex-ai-agent-engine-agent-identity.md` |
| 5 | Authenticate to Google and Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/mcp/authenticate-mcp) | `content/ingested/authentication/gcp-mcp-authenticate-mcp.md` |
| 6 | Deploying and Securing MCP Servers with Cloud Run and Apigee | [Medium](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) | `content/ingested/authentication/apigee-cloud-run-mcp-servers-securing.md` |
| 7 | Deploy agent with Cloud API Registry on Vertex AI Agent Engine | [Google Developer forums](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) | `content/ingested/authentication/gcp-cloud-api-registry-mcp-vertex-ai-agent-engine.md` |
| 8 | Apigee as LLM/agent gateway (Part 1 & 2) | [Medium Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4), [Part 2](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093) | `content/ingested/governance/apigee-llm-gateway-cloud-run-part1.md` |
| 9 | Safety and Security for AI Agents (ADK) | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
| 10 | Cloud API Registry overview | [Google Cloud](https://docs.cloud.google.com/api-registry/docs/overview) | `content/ingested/governance/gcp-cloud-api-registry-overview.md` |
| 11 | Register MCP APIs (Apigee API hub) | [Google Cloud](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis) | `content/ingested/governance/apigee-api-hub-register-mcp-apis.md` |
| 12 | AI security and safety (MCP) | [Google Cloud](https://docs.cloud.google.com/mcp/ai-security-safety) | `content/ingested/governance/gcp-mcp-ai-security-safety.md` |
| 13 | Model Armor integration with Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration) | `content/ingested/governance/model-armor-mcp-integration.md` |
