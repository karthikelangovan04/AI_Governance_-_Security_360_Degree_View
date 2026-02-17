---
source_url: "https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md"
source_title: "Safety and Security for AI Agents | Google ADK Documentation"
ingested_date: "2025-02-17"
topic: "governance"
google_services:
  - "Vertex AI (Generative AI)"
  - "Gemini (Vertex AI Gemini models)"
  - "Model Armor API"
  - "VPC Service Controls (VPC-SC)"
  - "Vertex AI Code Execution API / Code Interpreter Extension"
  - "Agent Development Kit (ADK)"
  - "Vertex AI (evaluation, tracing)"
---

# Safety and Security for AI Agents (Google ADK)

**Source:** [Safety and Security for AI Agents](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) — Google ADK (Agent Development Kit) documentation. **Google Cloud:** [Vertex AI](https://cloud.google.com/vertex-ai/generative-ai/docs/overview) provides a multi-layered approach to agent safety and security.

**Google services:** **Vertex AI**, **Gemini** (models, content filters, system instructions), **Model Armor API**, **VPC Service Controls (VPC-SC)**, **Vertex AI Code Execution API / Code Interpreter Extension**, **ADK** (callbacks, plugins, Tool Context), **Vertex AI** (evaluation, tracing).

---

## Risk sources and categories

**Sources of risk:** Vague instructions, model hallucination, jailbreaks and prompt injection (adversarial users), indirect prompt injection via tool use.

**Risk categories:**
- **Misalignment & goal corruption** — Unintended or proxy goals, reward hacking, misinterpreting ambiguous instructions.
- **Harmful content generation / brand safety** — Toxic, hateful, biased, sexually explicit, discriminatory, or illegal content; off-brand or off-topic outputs.
- **Unsafe actions** — Damaging commands, unauthorized purchases/transactions, PII leakage, data exfiltration.

---

## Multi-layered safety (Vertex AI / ADK)

### 1. Identity and authorization

- **Agent-Auth:** Tool uses **agent’s identity** (e.g. service account). Agent identity must be authorized in external systems (e.g. IAM on DB for read-only). Constrains actions to what the developer intended; **appropriate when all users share the same access**. Logs must attribute actions to users (actions appear as from agent).
- **User Auth:** Tool uses **controlling user’s identity** (e.g. OAuth token from frontend). Agent only performs actions the user could perform; reduces abuse risk. OAuth scopes are often broader than needed — combine with guardrails below.

### 2. Guardrails (inputs and outputs)

- **In-Tool Guardrails:** Design tools defensively; use **Tool Context** (developer-set) to enforce policies (e.g. allowed tables, SELECT-only). Validate model-set arguments against deterministic policy from context.
- **Built-in Gemini safety:** **Content filters** (non-configurable: e.g. CSAM, PII; configurable: hate, harassment, sexually explicit, dangerous — thresholds on Vertex AI); **system instructions** for safety and brand guidelines.
- **Callbacks:** **Before Tool Callback** — pre-validate tool and model I/O; access agent state, tool, params; block or allow.
- **Plugins (reusable):** Apply at runner level across agents. Examples: **Gemini as a Judge** (Gemini Flash Lite screens input/output for appropriateness, prompt injection, jailbreak); **Model Armor** plugin (Model Armor API for content safety); **PII Redaction** plugin (Before Tool Callback to redact PII before tool/external service).

### 3. Sandboxed code execution

- **Vertex AI:** [Vertex Gemini Enterprise API code execution](https://cloud.google.com/vertex-ai/generative-ai/docs/multimodal/code-execution-api) (server-side, sandboxed); ADK **Code Executor** tool → [Vertex Code Interpreter Extension](https://cloud.google.com/vertex-ai/generative-ai/docs/extensions/code-interpreter).
- **Custom executor:** Hermetic environment: no network/API; full cleanup to avoid cross-user exfiltration.

### 4. Evaluation and tracing

- **Evaluation:** Assess quality, relevance, correctness of agent output (see [Evaluate Agents](https://github.com/google/adk-docs/blob/main/docs/evaluate/index.md)).
- **Tracing:** Visibility into agent steps, tool choices, strategies, efficiency (Vertex AI / ADK tracing).

### 5. Network controls and VPC-SC

- **VPC Service Controls (VPC-SC):** Confine agent activity inside a perimeter; API calls only to resources within perimeter; reduces data exfiltration and impact radius.

### 6. Other

- **UI:** Escape model-generated content in UIs (HTML/JS) to prevent execution and exfiltration (e.g. indirect prompt injection via img/URL).

---

## Google services summary

| Service | Role in safety/security |
|---------|-------------------------|
| **Vertex AI** | Generative AI platform; evaluation; tracing; code execution API; Code Interpreter Extension. |
| **Gemini** | Models with content filters and system instructions; Gemini as Judge plugin (Flash Lite). |
| **Model Armor API** | Content safety checks; Model Armor plugin in ADK. |
| **VPC Service Controls** | Network perimeter; limit API calls to within perimeter. |
| **ADK** | Callbacks (Before Tool Callback), plugins, Tool Context, Code Executor; runs on Vertex AI Agent Engine / Cloud Run etc. |

---

## Reference

- **URL:** [Safety and Security for AI Agents (ADK)](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)
- **Vertex AI overview:** [Vertex AI Generative AI](https://cloud.google.com/vertex-ai/generative-ai/docs/overview)
- **Cited:** 2025-02-17
