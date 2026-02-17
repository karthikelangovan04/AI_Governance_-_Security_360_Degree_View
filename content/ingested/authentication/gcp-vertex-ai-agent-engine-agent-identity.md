---
source_url: "https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity"
source_title: "Use agent identity with Vertex AI Agent Engine | Vertex AI Agent Builder | Google Cloud"
ingested_date: "2025-02-17"
topic: "authentication"
---

# Use agent identity with Vertex AI Agent Engine

**Google Cloud service:** [Vertex AI Agent Builder](https://cloud.google.com/vertex-ai/agent-builder) — **Agent Engine** (runtime). This page covers **agent identity** (per-agent IAM principal) for agents deployed on Agent Engine.

## Overview

- **Agent identity** = per-agent IAM principal; enables **least-privilege**; tied to agent lifecycle; more secure than using a shared service account.
- Credentials secured by **Context-Aware Access (CAA)** with **mTLS binding** — certificate-bound tokens only usable from the intended runtime (e.g. Cloud Run), reducing credential theft and ATO risk.
- Covers: creating agent with identity, authorizing access to GCP APIs and other agents (A2A), OAuth for third-party, API keys via Secret Manager, logging agent activity, listing agents/identities, opting out of CAA (not recommended).

## Limitations

- Agent identities **cannot** get Legacy Bucket roles on Cloud Storage.
- **Recommendation:** use agent identity in test environments only (as of doc date).

## Create an agent with agent identity

- Create **Agent Engine instance** with `identity_type: AGENT_IDENTITY` (Vertex AI SDK v1beta1).
- Option 1: Create instance without code, then add code via `agent_engine.update(...)`.
- Option 2: Deploy agent code with `identity_type=types.IdentityType.AGENT_IDENTITY` in config.
- Identity format: `principal://TRUST_DOMAIN/NAMESPACE/AGENT_NAME` (e.g. `principal://agents.global.org-ORG_ID.system.id.goog/resources/aiplatform/projects/PROJECT_NUMBER/locations/LOCATION/reasoningEngines/AGENT_ENGINE_ID`).
- **SPIFFE**-based; x509 certificate auto-provisioned for the agent.
- If identity not set, instance uses **service account** (backward compatible).

## Access Google Cloud APIs with agent identity

- Use **IAM allow/deny** policies with the agent principal.
- Recommended roles: `roles/aiplatform.expressUser`, `roles/serviceusage.serviceUsageConsumer`, `roles/browser`; add others (logging, metrics, Cloud API Registry, etc.) as needed.
- Grant to **one agent**: `--member="principal://agents.global.org-ORG_ID.system.id.goog/resources/aiplatform/.../reasoningEngines/AGENT_ENGINE_ID"`.
- Grant to **all agents in project**: `principalSet://agents.global.org-ORG_ID.system.id.goog/attribute.platformContainer/aiplatform/projects/PROJECT_NUMBER` (or project-level trust domain if no org).
- **Deny:** IAM deny policy or **Principal Access Boundary (PAB)** to limit resources.

## Third-party: OAuth (delegated) and API keys

- Store **OAuth client ID/secret** or **API keys** in **Secret Manager** in the consumer project.
- Restrict secret access by **agent identity** IAM (`roles/secretmanager.secretAccessor` for the principal).
- At runtime, agent uses ADC (agent identity) to access Secret Manager and retrieve credentials; then use OAuth flow (e.g. ADK `AuthCredential`, `OAuth2Auth`) or API key in tools.
- For deployed agents, build custom frontend for OAuth redirect/callback if needed.

## Logging

- With **Cloud Logging**, logs show **agent identity** (and **user identity** when agent acts on user’s behalf).

## List agents and identities

- **Console:** Vertex AI Agent Engine → Identity column.
- **REST API:** Agent Engine instance response includes `spec.effectiveIdentity`.

## Opt out of CAA

- Not recommended. Set env `GOOGLE_API_PREVENT_AGENT_TOKEN_SHARING_FOR_GCP_SERVICES: False` when creating instance to opt out (increases credential theft risk).

## Reference

- **URL:** [Use agent identity with Vertex AI Agent Engine](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)
- **Cited:** 2025-02-17
