---
source_url: "https://docs.cloud.google.com/docs/authentication"
source_title: "Authentication methods at Google | Google Cloud Documentation"
ingested_date: "2025-02-17"
topic: "authentication"
---

# Authentication methods at Google (Google Cloud)

**Google Cloud scope:** General authentication for **Google Cloud services** (APIs, gcloud CLI, client libraries, REST). Not a single product—documentation that covers ADC, API keys, OAuth, service accounts, tokens, and identity products.

## Introduction

- **Authentication** = proving identity (credentials).
- Google APIs use and extend **OAuth 2.0**.
- Use the doc to choose the right method for: app users, gcloud, REST, client libraries, Vertex AI express mode, ID tokens, etc.

## Choosing authentication method

Decision flow (summary):

1. **Single-user dev (workstation, Cloud Shell)?** → Then: need service account? If no → user credentials; if yes → impersonate service account.
2. **Running in Google Cloud?** → GKE? Use Workload Identity for GKE; else attach service account to resource.
3. **On-prem or other cloud?** → Workload Identity Federation supported? If yes → use it; else → service account key (last resort).

## Authorization (Google Cloud)

- **IAM** — primary: granular by principal and resource.
- **OAuth 2.0 scopes** — e.g. `https://www.googleapis.com/auth/cloud-platform` (full) or limited scopes per API.

## Application Default Credentials (ADC)

- Strategy for auth libraries to **find credentials automatically** by environment.
- Same code can run in dev or production; credentials provided to ADC vary by environment (user, attached service account, etc.).
- Must **set up ADC** for the environment (local, container, Cloud, on-prem).

## Credentials (created in Google Cloud)

- **API keys** — for APIs that accept them; project for billing/quota; optionally bound to service account (e.g. express mode).
- **OAuth Client IDs** — for 3LO (access resources on behalf of end users).
- **Service account keys** — identify service account + project; **security risk**; prefer attached service account, impersonation, or Workload Identity over keys.

## Principals

- **User accounts** — Google Account, Cloud Identity, or federated (Workforce Identity Federation). Used for gcloud, ADC with user creds, impersonation.
- **Service accounts** — non-human; for apps and workloads. Prefer: (1) attach service account to resource + ADC, (2) impersonation, (3) Workload Identity Federation, (4) default service account (not recommended), (5) service account key.

## Tokens

- Tokens prove that credentials were validated; not credentials themselves.
- Types: access, refresh, ID tokens; see Token types doc.

## Workload vs workforce

- **Workload** — programmatic access (service accounts, Workload Identity Federation).
- **Workforce** — human users (Workforce Identity Federation with external IdP).

## Reference

- **URL:** [Authentication methods at Google](https://docs.cloud.google.com/docs/authentication)
- **Cited:** 2025-02-17
