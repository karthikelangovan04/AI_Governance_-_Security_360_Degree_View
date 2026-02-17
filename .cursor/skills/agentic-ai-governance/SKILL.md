---
name: agentic-ai-governance
description: Understands and uses the Agentic AI Security Governance knowledge base. Use when the user or project involves production-grade agentic AI deployment, security governance (observe, authentication, 360° view), URL-to-markdown ingestion with citations, consolidation into General/Google Cloud/AWS/Azure docs, hyperscaler cloud service mapping, or creating markdown knowledge cards for training and architecture.
---

# Agentic AI Security Governance — Intent & Usage

## Intent of this repository

This repo is a **comprehensive governance knowledge base** for deploying **production-grade agentic AI** solutions:

- **General** principles first (cloud-agnostic).
- **Google Cloud** as the primary hyperscaler (filled from ingested content).
- **AWS** and **Azure** as aligned placeholders (fill when content is ingested).

**Goals:**

1. **Observe** — Logging, monitoring, tracing, alerts for agents and APIs.
2. **Authentication** — Identity, M2M, API keys, secrets for agents and users.
3. **360° view** — Consolidated docs (exec summary, observe, auth, authorization, data, lifecycle, compliance, risk, glossary) with **citations** (URL + reference) in every article.
4. **Hyperscaler mapping** — Which **cloud service** (GCP/AWS/Azure) each governance area uses → `content/consolidated/09_Hyperscaler_Cloud_Service_Mapping.md`.
5. **Knowledge cards** — From consolidated docs, create markdown knowledge cards for **training** and **architectural understanding**; each card cites the consolidated doc and source URL.

## Key rules for assistants

1. **Citations:** Every ingested article and every consolidated section must store **citation** (source URL) and **reference** (ingested file or doc). Follow `content/CITATION_STANDARD.md`.
2. **Document order:** Write content in order **General → Google Cloud → AWS → Azure** in consolidated docs (00–09). Use placeholders for AWS/Azure when no content exists.
3. **Cloud services:** When mentioning a cloud capability, name the **exact service** and get it from `09_Hyperscaler_Cloud_Service_Mapping.md` (e.g. Cloud Logging, Secret Manager, Bedrock, Azure Monitor).
4. **Ingestion:** When saving content from a URL as markdown, add the frontmatter and "Reference" block from `CITATION_STANDARD.md` and save under `content/ingested/<topic>/`.
5. **Consolidation:** When consolidating ingested content into the 360° view, map content to the right doc (01–09), fill General and Google Cloud first, leave AWS/Azure placeholders if no source, and **populate "Sources & citations"** at the end of each doc.
6. **Knowledge cards:** When creating training or architecture cards, use `content/Knowledge_Cards_Guide.md` (one concept per card, domain/scope, citation to consolidated doc + URL).

## Document map

| Path | Purpose |
|------|--------|
| `content/ingested/` | Markdown from URLs (observe, authentication, governance); each file has citation. |
| `content/consolidated/00–09` | 360° view: exec summary, observe, auth, authorization, data, lifecycle, compliance, risk, glossary, **hyperscaler mapping**. |
| `content/consolidated/09_Hyperscaler_Cloud_Service_Mapping.md` | **Which cloud service** (per hyperscaler) each governance area uses. |
| `content/Knowledge_Cards_Guide.md` | How to derive **markdown knowledge cards** for training and architecture. |
| `content/CITATION_STANDARD.md` | Citation and reference format for all articles. |
| `content/knowledge_cards/` | Output: one card per concept, with citation. |

## When to apply this skill

- User asks about agentic AI governance, observe, authentication, or 360° view.
- User provides URLs to ingest or asks to "consolidate" ingested content.
- User asks which cloud service (GCP/AWS/Azure) applies to a governance area.
- User wants to create knowledge cards for training or architectural understanding.
- User works in this repo or references "Agentic AI Security Governance" or "production-grade agentic AI."
