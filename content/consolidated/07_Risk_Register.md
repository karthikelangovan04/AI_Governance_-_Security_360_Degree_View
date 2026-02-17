# Risk Register — Agentic AI & Security

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Risk Management pillar:** *Defense in depth* — multiple, overlapping layers of defense from data intake to final output; if one layer fails, another catches the problem ([Tahir — Four Pillars](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)). Data validation, content filters, output guardrails.
- **Governance debt risk:** Treating governance as afterthought leads to 8+ month retrofit; build governance in from day one.
- **ADK / Vertex AI risk sources:** Vague instructions, model hallucination, jailbreaks and prompt injection, indirect prompt injection via tool use. **Risk categories:** Misalignment & goal corruption; harmful content (toxic, hateful, biased, brand safety); unsafe actions (system damage, unauthorized transactions, PII leakage, data exfiltration) ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).

| ID | Risk | Likelihood | Impact | Mitigation | Owner |
|----|------|------------|--------|------------|-------|
|    |      |            |        |            |       |

## Google Cloud

- **Mitigations (Vertex AI / ADK):** Identity and authorization (Agent-Auth vs User Auth); guardrails (in-tool, Gemini content filters, system instructions, callbacks, plugins — Gemini as Judge, Model Armor, PII Redaction); sandboxed code execution (Vertex Code Execution API, Code Interpreter); evaluation and tracing; **VPC-SC** to confine API calls within perimeter; escape model output in UIs ([ADK Safety and Security](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)).

## AWS

<!-- Placeholder -->
- _To be filled._

## Azure

<!-- Placeholder -->
- _To be filled._

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | What is AI Agent Governance? The Four Pillars (Risk Management) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Safety and Security for AI Agents (ADK) — risk sources, categories, mitigations | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
