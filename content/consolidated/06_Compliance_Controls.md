# Compliance & Controls

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Frameworks in scope:** NIST, SOC2, ISO, etc.
- **Control mapping:** Control ID, description, evidence.

## Google Cloud

- **Services:** (see [Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))
- **Compliance:** Assured Workloads; compliance reports; shared responsibility.
- **Safety and security controls (ADK / Vertex AI):** **Guardrails** — in-tool guardrails (Tool Context, policy enforcement), **Gemini** content filters (non-configurable: e.g. CSAM, PII; configurable: hate, harassment, sexually explicit, dangerous), system instructions for safety and brand; **callbacks** (e.g. Before Tool Callback) and **plugins** (Gemini as Judge, **Model Armor**, **PII Redaction**); **sandboxed code execution** (Vertex Code Execution API, Code Interpreter Extension); **evaluation and tracing** (Vertex AI); **VPC Service Controls (VPC-SC)** to confine agent activity and reduce exfiltration. Escape model-generated content in UIs ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).
- **Model Armor for MCP:** **Model Armor** can sanitize MCP tool calls and responses; configure floor setting with `--add-integrated-services=GOOGLE_MCP_SERVER` and `--google-mcp-server-enforcement-type=INSPECT_AND_BLOCK`; enable MCP content security with `gcloud beta services mcp content-security add modelarmor.googleapis.com`. Protects against prompt injection, malicious URIs, and sensitive data disclosure in MCP traffic ([Model Armor MCP integration](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration)). **MCP security best practices:** Separate data from instructions (use XML delimiters); isolate resources between users/tenants; protect sensitive data (encryption); sanitize prompts with Model Armor; use deny policies to prevent read-write tool access to production; restrict MCP use at org/project/folder level ([MCP AI security and safety](https://docs.cloud.google.com/mcp/ai-security-safety)).

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
| 1 | What is AI Agent Governance? The Four Pillars (Risk / compliance) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Safety and Security for AI Agents (ADK) — guardrails, Model Armor, Gemini, VPC-SC | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
| 3 | Model Armor integration with Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration) | `content/ingested/governance/model-armor-mcp-integration.md` |
| 4 | AI security and safety (MCP) | [Google Cloud](https://docs.cloud.google.com/mcp/ai-security-safety) | `content/ingested/governance/gcp-mcp-ai-security-safety.md` |
