# Agentic AI Security Governance

Comprehensive governance for **production-grade agentic AI**: **Observe**, **Authentication**, and a **360° view** across policies, risks, and controls. Content is **general** first, then **Google Cloud**–specific, with **AWS** and **Azure** placeholders. Every article uses **citations** (URL + reference). From these docs you create **markdown knowledge cards** for training and architectural understanding.

---

## Quick start

1. **Plan:** [PLAN_Agentic_AI_Security_Governance.md](./PLAN_Agentic_AI_Security_Governance.md)
2. **Ingest:** Put markdown from URLs in `content/ingested/` **with citation** ([CITATION_STANDARD](content/CITATION_STANDARD.md)).
3. **Consolidate:** Map ingested content into `content/consolidated/00–09` (General → Google Cloud → AWS → Azure; Sources & citations).
4. **Cloud services:** Use [09_Hyperscaler_Cloud_Service_Mapping](content/consolidated/09_Hyperscaler_Cloud_Service_Mapping.md) for which GCP/AWS/Azure service applies.
5. **Knowledge cards:** Use [Knowledge_Cards_Guide](content/Knowledge_Cards_Guide.md) to create cards from consolidated docs (training & architecture).

---

## Structure

```
content/
  ingested/           # Markdown from URLs (observe, authentication, governance) — with citation
  consolidated/       # 360° view: 00–09 (exec summary … glossary + hyperscaler mapping)
  knowledge_cards/    # Training & architecture cards (one concept per card; cite doc + URL)
  CITATION_STANDARD.md
  Knowledge_Cards_Guide.md

.cursor/skills/agentic-ai-governance/   # Skill so assistants understand intent, citations, GCP/AWS/Azure, knowledge cards
```

---

## Document alignment (General → GCP → AWS → Azure)

| Doc | Purpose |
|-----|--------|
| 00_Executive_Summary | Overview; scope general + GCP + AWS/Azure |
| 01_Observe | Logging, monitoring, tracing, alerts |
| 02_Authentication | Identity, M2M, secrets |
| 03_Authorization_Access_Control | RBAC, agent permissions |
| 04_Data_Governance | Classification, PII, retention |
| 05_Agent_Lifecycle_Governance | Design, deploy, retire |
| 06_Compliance_Controls | Frameworks and evidence |
| 07_Risk_Register | Risks and mitigation |
| 08_Glossary_References | Terms and all source URLs |
| **09_Hyperscaler_Cloud_Service_Mapping** | **Which cloud service (GCP/AWS/Azure) each governance area uses** |

---

## Citations

- **Ingested files:** Frontmatter (`source_url`, `source_title`, `ingested_date`, `topic`) + Reference block at end.
- **Consolidated docs:** “Sources & citations” table (Title, URL, Ingested file / Reference) at end of each doc.
- **Knowledge cards:** Reference to consolidated doc + source URL. See [content/CITATION_STANDARD.md](content/CITATION_STANDARD.md).

---

## Assistant skill

The project skill **agentic-ai-governance** (`.cursor/skills/agentic-ai-governance/SKILL.md`) teaches assistants to:

- Understand intent (prod-grade agentic AI, observe, auth, 360° view).
- Apply citations to every article.
- Use General → Google Cloud → AWS → Azure and the hyperscaler cloud service mapping.
- Create knowledge cards using the guide (with citations).

---

## Creating articles from URLs

- Ask: *“Fetch this URL and save as markdown in content/ingested/&lt;topic&gt;/ with citation (frontmatter + reference block).”*
- Or paste content and add the citation block from `content/CITATION_STANDARD.md`.
