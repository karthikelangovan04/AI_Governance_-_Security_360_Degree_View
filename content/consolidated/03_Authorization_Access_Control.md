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
