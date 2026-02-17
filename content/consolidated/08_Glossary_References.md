# Glossary & References

<!-- All ingested sources with URL citation and reference. -->

## Glossary

| Term | Definition |
|------|-------------|
| Agent governance | Integrated framework of policies, processes, and technical controls that provides command and transparency over AI systems throughout their entire existence ([1](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e)). |
| Four pillars (agent governance) | Lifecycle Management (separation of duties), Risk Management (defense in depth), Security (least privilege), Observability (audit everything). |
| Lifecycle Management (pillar) | Governs how an agent is built, updated, and maintained; separation of duties; dev/staging/prod with no direct production push. |
| Risk Management (pillar) | Defense in depth; data quality, PII masking, guardrails, compliance checks, model validation. |
| Security (pillar) | Least privilege access; SSO, MFA, API keys, service principals, secret management. |
| Observability (pillar) | Audit everything; full traceability via audit logs, inference logs, monitoring, lineage. |
| Identity Platform (GCP) | Google Cloud service for user authentication: email/password, social, OIDC, SAML, MFA ([2](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email)). |
| Application Default Credentials (ADC) | GCP strategy for auth libraries to find credentials by environment (user, service account, etc.) ([3](https://docs.cloud.google.com/docs/authentication)). |
| Agent identity (Vertex AI) | Per-agent IAM principal on Vertex AI Agent Engine; SPIFFE-based; CAA/mTLS; least privilege for agents ([5](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity)). |
| OAuth 2.0 | Authorization framework (RFC 6749) for delegated/limited access; grant types: authorization code, client credentials, etc. ([4](https://datatracker.ietf.org/doc/html/rfc6749)). |
| MCP (Model Context Protocol) | Open standard for LLM–tool/API communication; MCP servers expose tools; MCP clients discover and invoke them. |
| Google Cloud MCP servers | GCP product for remote MCP servers; auth via ADC, IAM, Agent Engine agent identity, OAuth client ID, or API keys ([6](https://docs.cloud.google.com/mcp/authenticate-mcp)). |
| Apigee | Google Cloud API management: auth (API key, OAuth 2.0, JWT, SSO), API Products, Developer Portal, quotas, analytics; used as front door for MCP with tool-level access ([8](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235)). |
| Jump Start Solution | Pre-built Google Cloud reference solution (e.g. eCommerce platform with serverless); deploy via Console or Terraform; uses multiple GCP products ([9](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless)). |
| eCommerce Platform Serverless | Jump Start Solution: Cloud Run, Cloud SQL, Cloud Storage, Firebase Hosting, Secret Manager, Cloud Logging, Cloud Trace, Error Reporting; sign-in via Google Account → Cloud Console → solution page ([9](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless)). |
| Cloud API Registry | Centralized catalog of MCP servers and tools on Google Cloud; registry-first workflow for Vertex AI Agent Engine; discover/enable via gcloud beta api-registry mcp; ADK ApiRegistry.get_toolset(); IAM roles: cloudapiregistry.viewer, mcp.toolUser ([10](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130)). |
| Model Armor | Google’s prompt sanitization for LLM/agent inputs; used with Apigee (e.g. Advanced API product) to filter harmful or inappropriate content ([11](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4)). |
| Apigee Key Value Map | Encrypted key-value store in Apigee for credentials (e.g. LLM API keys); centralizes key management and avoids exposing keys to developers or end-users ([11](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4)). |
| ADK (Agent Development Kit) | Google’s framework for building AI agents; supports callbacks, plugins, Tool Context, in-tool guardrails; runs on Vertex AI; Python/TypeScript/Go/Java ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| Agent-Auth (ADK) | Tool uses the agent’s own identity (e.g. service account); agent identity authorized in external systems; appropriate when all users share same access ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| User Auth (ADK) | Tool uses the controlling user’s identity (e.g. OAuth token); agent only performs actions the user could perform; reduces abuse; often combined with guardrails ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| Tool Context (ADK) | Developer-set context passed to tools (e.g. from session state); used for in-tool guardrails (e.g. allowed tables, SELECT-only policy) ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| In-tool guardrails | Designing tools with policy enforced via Tool Context; validate model-set arguments against deterministic policy to limit allowed actions ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| Gemini as Judge | ADK plugin using Gemini Flash Lite to screen inputs/outputs for appropriateness, prompt injection, jailbreak; returns predetermined response if unsafe ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| PII Redaction plugin (ADK) | Plugin that redacts PII before tool execution or sending to external services; used in Before Tool Callback ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| VPC Service Controls (VPC-SC) | GCP perimeter control; confines API calls to resources within the perimeter; reduces data exfiltration and impact radius ([12](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md)). |
| Apigee API hub | Platform for managing organization's APIs, including MCP servers and tools; Cloud API Registry imports metadata from Apigee API hub; register MCP APIs by setting API style to MCP ([14](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis)). |
| Model Armor (MCP integration) | Sanitizes MCP tool calls and responses; mitigates prompt injection, malicious URIs, sensitive data disclosure; configure floor setting with GOOGLE_MCP_SERVER integration; enable MCP content security per project ([15](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration)). |
| MCP AI security and safety | Guidance for securing MCP server use: agent identity/permissions, protect against malicious prompts (separate data from instructions, isolate resources, sanitize with Model Armor), protect against malicious tool use (verify sources, restrict permissions, deny policies), data recovery strategy ([16](https://docs.cloud.google.com/mcp/ai-security-safety)). |

## Ingested sources (URLs) — citation required

| # | Title | URL | Ingested file |
|---|-------|-----|----------------|
| 1 | What is AI Agent Governance? The Four Pillars of AI Agent Governance | [Medium](https://medium.com/@tahirbalarabe2/what-is-ai-agent-governance-the-four-pillars-of-ai-agent-governance-d9b045475b3e) | `content/ingested/governance/four-pillars-ai-agent-governance-tahir-medium.md` |
| 2 | Sign in a user with an email (Identity Platform) | [Google Cloud](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) | `content/ingested/authentication/gcp-identity-platform-sign-in-email.md` |
| 3 | Authentication methods at Google | [Google Cloud](https://docs.cloud.google.com/docs/authentication) | `content/ingested/authentication/gcp-authentication-methods.md` |
| 4 | RFC 6749 — OAuth 2.0 Authorization Framework | [IETF](https://datatracker.ietf.org/doc/html/rfc6749) | `content/ingested/authentication/rfc6749-oauth2-authorization-framework.md` |
| 5 | Use agent identity with Vertex AI Agent Engine | [Google Cloud](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) | `content/ingested/authentication/gcp-vertex-ai-agent-engine-agent-identity.md` |
| 6 | Authenticate to Google and Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/mcp/authenticate-mcp) | `content/ingested/authentication/gcp-mcp-authenticate-mcp.md` |
| 7 | Implementing MCP OAuth (Upstash) | [Upstash](https://upstash.com/blog/mcp-oauth-implementation) | `content/ingested/authentication/upstash-mcp-oauth-implementation.md` |
| 8 | Deploying and Securing MCP Servers with Cloud Run and Apigee | [Medium](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) | `content/ingested/authentication/apigee-cloud-run-mcp-servers-securing.md` |
| 9 | eCommerce Platform Serverless — flow and Google Cloud services | [Google Cloud](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless) | `content/ingested/governance/gcp-ecommerce-platform-serverless-flow-and-services.md` |
| 10 | Deploy agent with Cloud API Registry on Vertex AI Agent Engine | [Google Developer forums](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) | `content/ingested/authentication/gcp-cloud-api-registry-mcp-vertex-ai-agent-engine.md` |
| 11 | Apigee as LLM gateway Part 1 (applies to agent API server on Cloud Run) | [Medium](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4) | `content/ingested/governance/apigee-llm-gateway-cloud-run-part1.md` |
| 12 | Apigee as LLM gateway Part 2 — implementation | [Medium](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093) | Implementation walkthrough; Part 1 ingested doc references Part 2 |
| 13 | Safety and Security for AI Agents (ADK) | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
| 14 | Register MCP APIs (Apigee API hub) | [Google Cloud](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis) | `content/ingested/governance/apigee-api-hub-register-mcp-apis.md` |
| 15 | Model Armor integration with Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration) | `content/ingested/governance/model-armor-mcp-integration.md` |
| 16 | AI security and safety (MCP) | [Google Cloud](https://docs.cloud.google.com/mcp/ai-security-safety) | `content/ingested/governance/gcp-mcp-ai-security-safety.md` |
| 17 | Cloud API Registry overview | [Google Cloud](https://docs.cloud.google.com/api-registry/docs/overview) | `content/ingested/governance/gcp-cloud-api-registry-overview.md` |

Every article in this repo stores **citation** (URL) and **reference** (ingested file path). See [CITATION_STANDARD.md](../CITATION_STANDARD.md).
