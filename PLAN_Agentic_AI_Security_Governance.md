# Agentic AI Security Governance — Plan & Guide

## Overview

This project builds a **comprehensive understanding** for deploying **production-grade agentic AI** solutions:

- **General** (cloud-agnostic) first.
- **Google Cloud** as the primary hyperscaler (content filled from ingested URLs).
- **AWS** and **Azure** as aligned placeholders (fill when content is ingested).

**Pillars:** Observe | Authentication | 360° governance (policies, risks, controls, compliance).

**Rules:**

- Every article stores **citation** (URL) and **reference** (ingested file or doc).
- **Hyperscaler cloud service mapping** is the single source for which cloud service (GCP/AWS/Azure) each governance area uses.
- From the comprehensive docs you create **markdown knowledge cards** for **training** and **architectural understanding**.

---

## Phase 1: Content Ingestion (URL → Markdown)

### What you do

1. **Collect URLs** — General and Google Cloud first; then AWS/Azure as needed.
2. **Convert to markdown** — Fetch each URL and save as markdown with **citation**: source URL, title, date, topic.
3. **Store** — Under `content/ingested/` (observe, authentication, governance).

### Citation for ingested files

Every ingested file **must** include (see `content/CITATION_STANDARD.md`):

- **Frontmatter:** `source_url`, `source_title`, `ingested_date`, `topic`.
- **Reference block** at end: URL (as link), cited date.

### How to get articles from URLs as markdown

- Share a URL and ask: “Fetch this URL and save as markdown in `content/ingested/<topic>/` **with citation** (frontmatter + reference block).”
- Use MCP fetch to get content; convert to markdown and write with citation.
- Manual: paste into `.md` and add the citation block.

---

## Phase 2: Document Structure (General → GCP → AWS → Azure)

Consolidated docs follow this order in **every** document (01–09):

1. **General** — Cloud-agnostic principles and controls.
2. **Google Cloud** — GCP services (from [09_Hyperscaler_Cloud_Service_Mapping](content/consolidated/09_Hyperscaler_Cloud_Service_Mapping.md)).
3. **AWS** — Placeholder until AWS content is ingested.
4. **Azure** — Placeholder until Azure content is ingested.
5. **Sources & citations** — Table: Title, URL, Ingested file / Reference.

### Consolidated document set (360° view)

| Document | Purpose |
|----------|--------|
| `00_Executive_Summary` | One-page overview; scope (general + GCP + AWS/Azure placeholders); link to hyperscaler mapping. |
| `01_Observe` | Logging, monitoring, tracing, alerts — General / GCP / AWS / Azure. |
| `02_Authentication` | Identity, M2M, API keys, secrets — General / GCP / AWS / Azure. |
| `03_Authorization_Access_Control` | RBAC, least privilege, agent permissions — General / GCP / AWS / Azure. |
| `04_Data_Governance` | Classification, PII, retention — General / GCP / AWS / Azure. |
| `05_Agent_Lifecycle_Governance` | Design, deploy, version, retire — General / GCP / AWS / Azure. |
| `06_Compliance_Controls` | Frameworks and evidence — General / GCP / AWS / Azure. |
| `07_Risk_Register` | Risks and mitigation — General / GCP / AWS / Azure. |
| `08_Glossary_References` | Terms and **all ingested sources with URL citation**. |
| `09_Hyperscaler_Cloud_Service_Mapping` | **Which cloud service** (per hyperscaler) each governance area uses. |

---

## Phase 3: Hyperscaler Cloud Service Mapping

**File:** `content/consolidated/09_Hyperscaler_Cloud_Service_Mapping.md`

- Holds the **exact cloud service** per hyperscaler for: Observe, Authentication, Authorization, Data governance, Agent lifecycle, Compliance.
- Use it when writing or consolidating: always name the **service** (e.g. Cloud Logging, Secret Manager, Bedrock, Azure Monitor).
- Update when new services or clouds are in scope.

---

## Phase 4: Consolidation Workflow

**Input:** Markdown in `content/ingested/` (with citations) + any extra notes.

**Process:**

1. Read ingested content.
2. Map to the right consolidated doc (01–09) and section (General / Google Cloud / AWS / Azure).
3. Fill General and Google Cloud from content; leave AWS/Azure as placeholders if no source.
4. Populate **Sources & citations** at the end of each doc (Title, URL, reference).
5. When referencing a cloud, use the service name from `09_Hyperscaler_Cloud_Service_Mapping.md`.

**Output:** Updated `content/consolidated/00–09` with citations and hyperscaler alignment.

---

## Phase 5: Knowledge Cards (Training & Architecture)

**Objective:** From the comprehensive consolidated docs, create **markdown knowledge cards** for:

- **Training** — Onboarding and upskilling on agentic AI security governance.
- **Architectural understanding** — Quick reference for production-grade agentic AI (general and hyperscaler).

**Guide:** `content/Knowledge_Cards_Guide.md`

- One card = one concept; include domain (observe, auth, etc.) and scope (General / GCP / AWS / Azure).
- **Every card** cites: consolidated doc (path + section) and source URL.
- Output folder: `content/knowledge_cards/` (by domain: observe, authentication, authorization, data_governance, lifecycle, compliance, risk).

---

## Assistant skill

**Path:** `.cursor/skills/agentic-ai-governance/SKILL.md`

Any assistant that loads this skill will:

- Understand the **intent** of the repo (production-grade agentic AI governance, observe, auth, 360° view).
- Apply **citations** (URL + reference) to every article.
- Follow **General → Google Cloud → AWS → Azure** and use **09_Hyperscaler_Cloud_Service_Mapping** for cloud services.
- Use **Knowledge_Cards_Guide** when creating training or architecture cards.

---

## Your checklist

- [ ] Ingest general and Google Cloud URLs with citation (frontmatter + reference).
- [ ] Consolidate into 00–09; fill General and GCP; leave AWS/Azure placeholders.
- [ ] Keep `09_Hyperscaler_Cloud_Service_Mapping.md` as source of truth for cloud services.
- [ ] Add AWS/Azure content when available; fill placeholders and citations.
- [ ] Create knowledge cards from consolidated docs for training and architecture (with citations).

---

## Summary

| Step | You do | Result |
|------|--------|--------|
| 1 | Collect URLs; ingest as markdown **with citation** | `content/ingested/` with source_url, reference |
| 2 | Consolidate | `content/consolidated/00–09` — General → GCP → AWS → Azure; Sources & citations |
| 3 | Use hyperscaler mapping | Correct **cloud service** name per governance area (GCP/AWS/Azure) |
| 4 | Create knowledge cards | `content/knowledge_cards/` — training & architecture; each card cites doc + URL |
