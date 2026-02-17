---
source_url: "https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130"
source_title: "Where is the MCP server? Deploy your agent with Cloud API Registry on Vertex AI Agent Engine - Google Developer forums"
ingested_date: "2025-02-17"
topic: "authentication"
google_services:
  - "Cloud API Registry"
  - "Vertex AI Agent Engine"
  - "Vertex AI Agent Builder"
  - "BigQuery (MCP server example)"
  - "IAM (cloudapiregistry.viewer, mcp.toolUser, bigquery.user, bigquery.dataViewer)"
  - "Agent Development Kit (ADK)"
  - "gcloud CLI (beta api-registry mcp)"
---

# Deploy your agent with Cloud API Registry on Vertex AI Agent Engine

**Source:** [Google Developer forums — Where is the MCP server?](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) (December 2025).

**Google services:** **Cloud API Registry**, **Vertex AI Agent Engine**, **Vertex AI Agent Builder**, **BigQuery** (example MCP server), **IAM**, **Agent Development Kit (ADK)**, **gcloud** (beta api-registry mcp).

## Problem (before Cloud API Registry)

Enterprise adoption of **MCP** for agent deployment is limited by:

- **Discoverability** — Finding and reusing MCP servers is hard; tool reuse across platforms is low.
- **Governance** — No centralized catalog or policies for which tools/APIs agents can use.
- **Security** — Manual secrets and token management; unsupported or complex interfaces for third-party tools.

## Cloud API Registry — role

**Cloud API Registry** is the **definitive source of truth for tools** in your organization: a **centralized catalog of MCP servers** on Google Cloud (and custom). For **Vertex AI Agent Engine** it provides:

- **Unified discovery** — See which APIs and tools are available and their policies/restrictions.
- **Centralized governance** — One place to manage and govern tool access.
- **Simplified integration** — No manual wrapper code or per-integration credential setup.
- **Unified security** — Use configured credentials and standard **IAM** instead of local secrets.

## Developer workflow (“registry-first”)

1. **Standardized discovery** — Find tools via CLI: `gcloud beta api-registry mcp servers list --all --project=PROJECT_ID` (e.g. bigquery.googleapis.com, compute.googleapis.com).
2. **Zero boilerplate** — Enable a server: `gcloud beta api-registry mcp enable bigquery.googleapis.com --project=PROJECT_ID`; agent gets tools like `list_dataset_ids`, `get_table_info`, `execute_sql` without writing wrapper code.
3. **Unified security** — Rely on IAM (e.g. `roles/cloudapiregistry.viewer`, `roles/mcp.toolUser`, `roles/bigquery.user`, `roles/bigquery.dataViewer`) for the **Agent Engine service account**; no per-tool secret management.

## Building and deploying a Data Analyst Agent (summary)

1. **Find tools** — `gcloud beta api-registry mcp servers list --all`; enable BigQuery: `gcloud beta api-registry mcp enable bigquery.googleapis.com`. Tools appear in Vertex AI Agent Builder **Tools** view.
2. **Define agent (ADK)** — Use **ApiRegistry** in ADK: `tool_registry = ApiRegistry(PROJECT_ID, header_provider=...)`; `registry_tools = tool_registry.get_toolset(mcp_server_name=".../google-bigquery.googleapis.com-mcp")`; pass `tools=[registry_tools]` to **LlmAgent**. No BigQuery SDK imports or hand-written tool definitions.
3. **IAM for Agent Engine** — Grant the Agent Engine service account (`service-PROJECT_NUMBER@gcp-sa-aiplatform-re.iam.gserviceaccount.com`): `roles/cloudapiregistry.viewer`, `roles/mcp.toolUser`, `roles/bigquery.user`, `roles/bigquery.dataViewer`.
4. **Infrastructure hook** — Startup script (e.g. `check_api_registry.sh`) to verify MCP server is enabled/reachable during container build.
5. **Deploy** — `agent_engines.create(agent_engine=ModuleAgent(module_name="root_agent", agent_name="agent_app"), ...)`. Agent Engine installs ADK, runs startup script, deploys agent; agent connects to registry to fetch tools at runtime.

## Governance relevance

- **Observe:** Agent uses managed tools from the registry; access is via IAM and logged in Cloud.
- **Authentication/authorization:** **Cloud API Registry** + **IAM** replace ad-hoc secrets and manual token passing; Agent Engine service account is the identity for registry and BigQuery.
- **Lifecycle:** Registry-first workflow reduces custom code and keeps agents aligned with org-approved tools and policies.

## Reference

- **URL:** [Where is the MCP server? Deploy your agent with Cloud API Registry on Vertex AI Agent Engine](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)
- **Cited:** 2025-02-17
