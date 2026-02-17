# Compliance & Controls

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Frameworks in scope:** NIST, SOC2, ISO, etc.
- **Control mapping:** Control ID, description, evidence.

## Google Cloud

- **Services:** (see [Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))
- **Compliance:** Assured Workloads; compliance reports; shared responsibility.
- **Safety and security controls (ADK / Vertex AI):** **Guardrails** — in-tool guardrails (Tool Context, policy enforcement), **Gemini** content filters (non-configurable: e.g. CSAM, PII; configurable: hate, harassment, sexually explicit, dangerous), system instructions for safety and brand; **callbacks** (e.g. Before Tool Callback) and **plugins** (Gemini as Judge, **Model Armor**, **PII Redaction**); **sandboxed code execution** (Vertex Code Execution API, Code Interpreter Extension); **evaluation and tracing** (Vertex AI); **VPC Service Controls (VPC-SC)** to confine agent activity and reduce exfiltration. Escape model-generated content in UIs ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).

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
