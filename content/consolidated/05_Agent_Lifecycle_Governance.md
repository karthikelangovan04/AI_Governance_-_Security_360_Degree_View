# Agent Lifecycle Governance

<!-- Consolidation: General → Google Cloud → AWS → Azure. Every article: citation + URL reference. -->

## General

- **Lifecycle Management pillar:** *Separation of duties* — every change reviewed, tested, and approved in a controlled manner ([Tahir — Four Pillars](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)). Distinct dev/staging/prod; no direct push to production without review. Tools: version control (e.g. Git), CI/CD, deployment with rollback.
- **Design and approval:**
- **Deploy and change:** Promote change dev → staging → production; reviews mandatory at each stage.
- **Versioning and rollback:** Instant rollback of bad changes.
- **Retire / decommission:**

## Google Cloud

- **Services:** (see [Hyperscaler_Cloud_Service_Mapping](09_Hyperscaler_Cloud_Service_Mapping.md))
- **Vertex AI:** Model and agent deployment; versions; endpoints.
- **CI/CD and approval:** Cloud Build; deployment gates.

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
| 1 | What is AI Agent Governance? The Four Pillars (Lifecycle) | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
