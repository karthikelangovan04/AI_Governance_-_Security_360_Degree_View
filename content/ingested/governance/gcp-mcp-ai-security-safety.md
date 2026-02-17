---
source_url: "https://docs.cloud.google.com/mcp/ai-security-safety"
source_title: "AI security and safety | Google Cloud MCP servers | Google Cloud Documentation"
ingested_date: "2025-02-17"
topic: "governance"
google_services:
  - "Google Cloud MCP servers"
  - "Model Armor"
  - "Vertex AI Agent Engine (agent identity)"
  - "IAM"
  - "Workload Identity Federation"
  - "Sensitive Data Protection (DLP)"
---

# AI security and safety (Google Cloud MCP servers)

**Google Cloud service:** [Google Cloud MCP servers](https://cloud.google.com/mcp) — security and safety guidance for using MCP servers with AI agents.

**Preview.** MCP servers enable AI applications to take actions on behalf of users and make changes that might not be reversible. This document outlines risks and mitigations.

## Agent operation types

| Type | Description | Risks |
|------|-------------|-------|
| **Human-in-the-Middle (HitM)** | Agent suggests actions; human must approve each action | Human oversight reduces risk but vulnerable to human error approving malicious/destructive actions |
| **Agent-Only (AO)** | Agent takes action without waiting for approval | Vulnerable to prompt injection, insecure tool chaining, naive error handling |

## Design agents with security in mind

### Agent identity and permissions

- Create **agent identity** (service account or Vertex AI Agent Engine agent identity)
- Follow **least privilege** — grant only necessary roles/permissions
- Use **IAM attributes** to prevent read-write MCP tool use on important resources
- For on-prem/other cloud: use **Workload Identity Federation**
- For services without IAM: use **API keys** with application/API restrictions

### Protect against malicious prompts

- **Separate data from instructions** — treat user-provided content as data, not instructions; use strong delimiters (XML tags) and explicit instructions
- **Isolate resources** — isolate agent memory/state between users, tenants, or agents
- **Protect sensitive data** — encrypt sensitive data in transit and in memory
- **Sanitize incoming prompts** — use **Model Armor** to screen LLM prompts and responses

### Protect against malicious or unexpected MCP tool use

| Scenario | Mitigation |
|----------|------------|
| **Malicious or masquerading tool** | Investigate/verify source; periodically review tool list; only allow specific tool use; use **Model Armor**; use deny policy to prevent read-write tool access to production |
| **Dynamic tools** (trusted servers add new tools silently) | Periodically review tool list; only allow specific tool use; restrict permissions; restrict MCP use at org/project/folder level; use deny policy for read-write on production |

### Create a data recovery strategy

Enable and configure data recovery for services you use.

## Recommended Model Armor floor settings

```bash
gcloud model-armor floorsettings update \
  --full-uri='projects/PROJECT_ID/locations/global/floorSetting' \
  --mcp-sanitization=ENABLED \
  --malicious-uri-filter-settings-enforcement=ENABLED \
  --pi-and-jailbreak-filter-settings-enforcement=ENABLED \
  --pi-and-jailbreak-filter-settings-confidence-level=MEDIUM_AND_ABOVE
```

## Recommended de-identification template settings

Create a sensitive data de-identify template (mask names, emails, phones, credit cards, SSNs, street addresses) using **Sensitive Data Protection (DLP)**.

## Reference

- **URL:** [AI security and safety (MCP)](https://docs.cloud.google.com/mcp/ai-security-safety)
- **Cited:** 2025-02-17
