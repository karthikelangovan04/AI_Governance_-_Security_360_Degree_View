---
source_url: "https://docs.cloud.google.com/mcp/authenticate-mcp"
source_title: "Authenticate to Google and Google Cloud MCP servers | Google Cloud Documentation"
ingested_date: "2025-02-17"
topic: "authentication"
google_services:
  - "Google Cloud MCP servers (product)"
  - "Application Default Credentials (ADC)"
  - "IAM (Identity and Access Management)"
  - "Vertex AI Agent Engine (agent identity)"
  - "OAuth 2.0 / OAuth client ID (Google API Console)"
  - "API keys (APIs & Services > Credentials)"
  - "Secret Manager (for credentials)"
  - "Workload Identity Federation"
  - "Compute Engine (service account attach)"
  - "gcloud CLI"
---

# Authenticate to Google and Google Cloud MCP servers

**Google Cloud product:** [Google Cloud MCP servers](https://cloud.google.com/mcp) — documentation for authenticating AI applications (agents) to Google and Google Cloud **remote Model Context Protocol (MCP)** servers.

**Preview.** Implements MCP authorization spec for HTTP-based transports. Some endpoints don’t require authentication; services that require IAM don’t support standard API key credentials; services that don’t use IAM (e.g. Google Maps) can use API keys.

Terms *AI application* and *agent* are used interchangeably; MCP client = any program that can instantiate an MCP client.

## How tool calling and authentication work

When the AI application calls an MCP tool, it authenticates to the MCP server using the method you supply. With your credentials, actions are attributed to you and the app has your permissions. For **production**, choose:

- **Separate identity for the AI application:** create a **service account** or use **agent identity** (Vertex AI Agent Engine).
- **App authenticates on your behalf without sharing credentials:** create an **OAuth client ID and credentials**.
- **Services that don’t require a principal:** use **API keys**.

## User identity (testing)

- **Local dev:** User credentials + **ADC** — run `gcloud auth application-default login`; ADC then authenticates to Google Cloud services including MCP servers. Or **service account impersonation** (e.g. `gcloud config set auth/impersonate_service_account SERVICE_ACCT_EMAIL` or `gcloud auth application-default login --impersonate-service-account=SERVICE_ACCT_EMAIL` for Go, Java, Node.js, Python).
- **OAuth client ID:** Create OAuth client ID and client secret so the AI app can access resources as you (OAuth tokens; credentials not shared). See [Obtain OAuth 2.0 credentials from the Google API Console](https://developers.google.com/identity/protocols/oauth2).

## Agent identity (production)

For production, use a **separate agent identity** for observability and security. Grant minimum permissions; consider IAM attributes to prevent read-write MCP tool use on sensitive resources.

- **Service account:** If the app is on **Google Cloud**, attach a service account to the compute resource (e.g. **Compute Engine** VM); use metadata server for tokens. Create SA with `gcloud iam service-accounts create`; grant roles; attach to VM with `--service-account=SERVICE_ACCOUNT_EMAIL`.
- **Agent identity:** If using **Vertex AI Agent Engine** to deploy the agent, create an **agent identity** (per-agent IAM principal).
- **On Google Cloud:** Prefer credentials of the **service account attached** to the compute resource (Cloud Run, GKE, Compute Engine, etc.). Most services require attaching the SA at resource creation.
- **On-prem or other cloud:** Prefer **Workload Identity Federation**; see [Set up ADC for on-premises or another cloud provider](https://cloud.google.com/docs/authentication/set-up-adc-on-premises-or-another-cloud-provider).

## API keys (services without IAM principal)

For services that don’t require a principal (e.g. Google Maps):

1. **APIs & Services > Credentials** → Create credentials → **API key**.
2. Edit **Application restrictions** and **API restrictions**.
3. Follow [best practices for API keys](https://cloud.google.com/docs/authentication/api-keys).

## Reference

- **URL:** [Authenticate to Google and Google Cloud MCP servers](https://docs.cloud.google.com/mcp/authenticate-mcp)
- **Cited:** 2025-02-17
