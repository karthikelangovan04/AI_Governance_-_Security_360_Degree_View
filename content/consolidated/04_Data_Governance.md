# Data Governance

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Risk Management pillar:** Proactively find and stop risks at every stage (data intake to final output). Tools: data quality monitoring, **PII detection and masking**, behavioral guardrails, compliance checks ([Tahir — Four Pillars](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)).
- **Data classification:**
- **Retention and deletion:**
- **PII and sensitive data:** Handling by agents; defense in depth across data layers.

## Google Cloud

- **Services:** (see [Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))
- **Classification & DLP:** Sensitive data detection; retention policies.
- **Agent data usage:** Vertex AI, data residency, CMEK.
- **PII in agent safety (ADK / Vertex AI):** **Gemini** non-configurable content filters block PII in outputs; **PII Redaction plugin** (ADK) redacts PII before tool execution or sending to external services (Before Tool Callback). Reduces PII leakage and unsafe actions ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).

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
| 1 | What is AI Agent Governance? The Four Pillars (Risk / Data, PII) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Safety and Security for AI Agents (ADK) — PII filters, PII Redaction plugin | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
