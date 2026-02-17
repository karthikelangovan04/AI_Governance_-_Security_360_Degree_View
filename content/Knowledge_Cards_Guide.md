# Knowledge Cards — Training & Architectural Understanding

**Objective:** From the **comprehensive consolidated documents** in this repo, create **markdown knowledge cards** for:

- **Training** — Onboarding, upskilling, and role-based learning on agentic AI security governance.
- **Architectural understanding** — Quick reference for designing and reviewing production-grade agentic AI (general and hyperscaler-specific).

---

## What is a knowledge card?

A **knowledge card** is a short, self-contained markdown document that:

1. Covers **one concept, control, or decision** (one card = one idea).
2. Includes **citation** (source URL and reference to consolidated doc).
3. Is **scannable**: title, 2–5 bullet points or a short table, optional diagram/text.
4. Can be consumed standalone or linked from a curriculum/index.

---

## Card structure (template)

```markdown
# [Card title — one concept]

**Domain:** Observe | Authentication | Authorization | Data | Lifecycle | Compliance | Risk  
**Scope:** General | Google Cloud | AWS | Azure

## In one sentence
[Single-sentence summary.]

## Key points
- 
- 
- 

## Reference
- **Consolidated doc:** [01_Observe](consolidated/01_Observe.md) (section: ...)
- **Source URL:** [Title](url)
```

---

## Where to create cards

Suggested folder:

```
content/
  knowledge_cards/
    observe/
    authentication/
    authorization/
    data_governance/
    lifecycle/
    compliance/
    risk/
```

One file per card, e.g. `content/knowledge_cards/observe/cloud-logging-retention.md`.

---

## Deriving cards from consolidated docs

| Consolidated doc | Example cards |
|------------------|----------------|
| 01_Observe | "What to log for agents", "Cloud Logging retention", "Alert escalation path" |
| 02_Authentication | "Service account vs user identity", "Secret Manager usage", "M2M auth pattern" |
| 03_Authorization_Access_Control | "Least privilege for agents", "IAM custom roles for AI" |
| 04_Data_Governance | "PII handling by agents", "CMEK for model data" |
| 05_Agent_Lifecycle_Governance | "Vertex AI deployment checklist", "Rollback procedure" |
| 06_Compliance_Controls | "SOC2 control mapping for agents", "Assured Workloads scope" |
| 07_Risk_Register | One card per risk or per risk category |
| 09_Hyperscaler_Cloud_Service_Mapping | "Observe stack: GCP vs AWS vs Azure", "Auth stack by cloud" |

---

## Citation in every card

Every knowledge card **must** include:

- **Consolidated doc:** Path and section (e.g. `01_Observe.md`, section "Google Cloud").
- **Source URL:** Original source from the consolidated doc’s "Sources & citations" table.

This keeps training and architecture material traceable to the comprehensive docs and to the original URLs.
