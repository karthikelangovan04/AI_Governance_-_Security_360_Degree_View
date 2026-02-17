# Authorization & Access Control

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Security pillar:** *Least privilege access* — no entity should get unnecessary access to data, tools, or systems; limits damage from accidents and attacks ([Tahir — Four Pillars](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)).
- **Model:** RBAC / ABAC / other; granular access controls.
- **Least privilege:** Defaults and scoping.
- **Agent permissions:** What agents can and cannot access; scope and boundaries.

## Google Cloud

- **Services:** (see [Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))
- **IAM:** Roles, custom roles, resource hierarchy; OAuth 2.0 scopes for API access.
- **Agent scoping:** **Vertex AI Agent Engine** — per-agent identity (principal); grant/deny via IAM allow/deny policies; **Principal Access Boundary (PAB)** to limit resources regardless of other permissions. Prefer agent identity over shared service accounts for least privilege ([Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)). **Tool access via Cloud API Registry:** Centralized MCP tool catalog; grant Agent Engine service account `roles/cloudapiregistry.viewer` and `roles/mcp.toolUser` for registry access, plus data roles (e.g. bigquery.user, bigquery.dataViewer) for underlying services; tools consumed via ADK ApiRegistry with no per-tool secret management ([Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)).

- **Role-based access (RBAC) for agents and MCP tools (Google Cloud):**
  - **Agents:** Use **IAM** to assign roles per agent or per agent “role type.” (1) **Per-agent:** Grant roles to a single agent principal `principal://agents.global..../reasoningEngines/AGENT_ENGINE_ID` — e.g. one agent gets only `roles/bigquery.dataViewer`, another gets `roles/bigquery.user` + `roles/cloudapiregistry.viewer` + `roles/mcp.toolUser`. (2) **Per “role” (multiple agents):** Use **principal set** `principalSet://agents.global..../attribute.platformContainer/aiplatform/projects/PROJECT_NUMBER` to grant the same role to all agents in the project, or use **custom IAM roles** (e.g. “Agent Data Analyst” = viewer + registry + BigQuery read) and bind to agent principals or principal set. (3) **Tighten scope:** Use **Principal Access Boundary (PAB)** or **IAM conditions** (e.g. resource name, resource tags) so a role applies only to specific buckets/datasets. (4) **User-context roles:** If the agent acts as the user (User Auth), enforce role in the app (e.g. **Identity Platform** custom claims or **Apigee** API Product per user group) and pass only allowed tools/APIs to the agent; optionally validate in **ADK** Before Tool Callback using session state. **Google services:** IAM (roles, custom roles, conditions, PAB), Vertex AI Agent Engine (agent identity), Identity Platform (user claims), Apigee (API Products), ADK (callbacks). [IAM custom roles](https://cloud.google.com/iam/docs/creating-custom-roles) · [IAM conditions](https://cloud.google.com/iam/docs/conditions-overview) · [Agent identity](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity).
  - **MCP tools:** (1) **Apigee (gateway):** Define **API Products** as “roles” — each product has an **allowed_tools** attribute (comma-separated list or `*`). Assign API key/OAuth to a product; client sees only those tools in `tools/list`; Apigee blocks tool calls not in `allowed_tools`. Example: “MCP Basic” = read-only tools, “MCP Advanced” = all tools. (2) **Cloud API Registry:** Use different **Agent Engine service accounts** (or different agent identities) per “role”; grant each SA only the registry + data roles needed (e.g. Analyst SA: `cloudapiregistry.viewer`, `mcp.toolUser`, `bigquery.dataViewer`; Writer SA: add `bigquery.user`). Or one SA with **IAM conditions** on resources (e.g. only datasets in folder X). **Apigee API hub** can register MCP APIs (set API style to MCP); Cloud API Registry imports metadata from Apigee API hub for unified discovery. (3) **In-agent (ADK):** Use **Tool Context** or **Before Tool Callback** to enforce policy from session state (e.g. user role from Identity Platform); allow/deny tool or parameters by role. **Google services:** Apigee (API Products, allowed_tools), **Apigee API hub** (register MCP APIs), Cloud API Registry + IAM (registry + data roles per SA/agent), ADK (Tool Context, Before Tool Callback), Identity Platform (optional user attributes). [Apigee API Products](https://cloud.google.com/apigee/docs/api-platform/publish/creating-api-products) · [Register MCP APIs (Apigee API hub)](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis) · [Cloud API Registry overview](https://docs.cloud.google.com/api-registry/docs/overview) · [Cloud API Registry + Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) · [ADK Safety (Tool Context, callbacks)](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md).

## AWS

<!-- Placeholder -->
- **Services:** (see Hyperscaler_Cloud_Service_Mapping)
- _To be filled._

## Azure

<!-- Placeholder -->
- **Services:** (see Hyperscaler_Cloud_Service_Mapping)
- _To be filled._

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | What is AI Agent Governance? The Four Pillars (Security / Least privilege) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Use agent identity with Vertex AI Agent Engine (IAM, PAB) | [Google Cloud](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) | `content/ingested/authentication/gcp-vertex-ai-agent-engine-agent-identity.md` |
| 3 | Cloud API Registry + Agent Engine (tool access, IAM) | [Google Developer forums](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) | `content/ingested/authentication/gcp-cloud-api-registry-mcp-vertex-ai-agent-engine.md` |
| 4 | IAM custom roles | [Google Cloud](https://cloud.google.com/iam/docs/creating-custom-roles) | — |
| 5 | IAM conditions overview | [Google Cloud](https://cloud.google.com/iam/docs/conditions-overview) | — |
| 6 | Apigee API Products | [Google Cloud](https://cloud.google.com/apigee/docs/api-platform/publish/creating-api-products) | — |
| 7 | Register MCP APIs (Apigee API hub) | [Google Cloud](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis) | `content/ingested/governance/apigee-api-hub-register-mcp-apis.md` |
| 8 | Cloud API Registry overview | [Google Cloud](https://docs.cloud.google.com/api-registry/docs/overview) | `content/ingested/governance/gcp-cloud-api-registry-overview.md` |
