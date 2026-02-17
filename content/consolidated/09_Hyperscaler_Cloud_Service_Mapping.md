# Hyperscaler Cloud Service Mapping — Governance by Cloud

This document holds **which cloud service** (per hyperscaler) each governance area is part of or depends on. Use it when filling consolidated docs (Observe, Authentication, etc.) so GCP/AWS/Azure sections reference the correct services.

---

## Observe (logging, monitoring, tracing, alerts)

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **Logging** | Cloud Logging (Logging API, log buckets, sinks) | CloudWatch Logs | Azure Monitor Logs (Log Analytics) |
| **Metrics / monitoring** | Cloud Monitoring (Metrics, dashboards, Uptime) | CloudWatch Metrics, X-Ray | Azure Monitor (Metrics, Insights) |
| **Tracing** | Cloud Trace | X-Ray | Application Insights |
| **Alerting** | Cloud Monitoring Alerting, Notification channels | CloudWatch Alarms, SNS | Azure Monitor Alerts, Action groups |
| **Audit** | Cloud Audit Logs (Admin, Data, Access) | CloudTrail | Azure Activity Log, Azure AD sign-in logs |

---

## Authentication (identity, keys, secrets)

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **Identity / SSO** | IAM, **Identity Platform** (user sign-in: email, social, OIDC, SAML, MFA), Workforce Identity Federation | IAM, Cognito, IAM Identity Center | Azure AD / Entra ID, B2C |
| **Service accounts / workload identity** | IAM Service Accounts, Workload Identity (GKE, etc.) | IAM Roles, IRSA (EKS) | Managed identities, service principals |
| **Agent identity (per-agent)** | **Vertex AI Agent Engine** (per-agent IAM principal, CAA/mTLS; Secret Manager for OAuth/API keys) | _To be filled_ | _To be filled_ |
| **MCP (Model Context Protocol)** | **Google Cloud MCP servers** (ADC, IAM, Agent Engine, OAuth client ID, API keys); **Cloud API Registry** (MCP catalog, registry-first tools, IAM: cloudapiregistry.viewer, mcp.toolUser); **Cloud Run** (MCP runtime); **Apigee** (MCP front door, API Products, Developer Portal) | _To be filled_ | _To be filled_ |
| **LLM / agent API gateway** | **Apigee** (gateway, API Products, Key Value Map, Model Armor, Developer Portal, analytics); **Cloud Run** (integration layer or **agent API server**); **Vertex AI / Gemini** (LLM backend); **Model Armor** (prompt sanitization) | _To be filled_ | _To be filled_ |
| **API keys** | API Keys (Consumer), IAM for APIs | IAM + API Gateway / usage plans | API Management, Azure AD tokens |
| **Secrets** | Secret Manager | Secrets Manager, Parameter Store | Key Vault (secrets) |

---

## Authorization & access control

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **RBAC / policies** | IAM (roles, custom roles, org policies) | IAM (policies, roles), ABAC | Azure RBAC, Azure Policy |
| **RBAC for agents & MCP tools** | **IAM** (per-agent principal, principal set, custom roles, PAB, conditions); **Vertex AI Agent Engine** (agent identity); **Apigee** (API Products, **allowed_tools** for MCP); **Cloud API Registry** + IAM (registry + data roles per SA/agent); **ADK** (Tool Context, Before Tool Callback); **Identity Platform** (user claims) | _To be filled_ | _To be filled_ |
| **Resource hierarchy** | Org → Folder → Project | Organizations, OUs | Management groups, subscriptions, resource groups |
| **Network / perimeter** | VPC Service Controls, BeyondCorp | VPC, Security groups, IAM | VNet, NSGs, Azure Policy |

---

## Data governance (classification, retention, PII)

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **Classification / DLP** | Sensitive Data Protection (DLP API) | Macie, DLP patterns | Purview, Information Protection |
| **Retention** | Retention policies (Logging, BigQuery, etc.) | S3 lifecycle, Glacier, legal hold | Retention policies, Purview |
| **Encryption (CMEK/CBYOK)** | CMEK (Cloud KMS) | KMS, BYOK | Key Vault (keys), CMEK for services |
| **Agent / AI data** | Vertex AI (data location, CMEK), BigQuery | Bedrock, SageMaker (data) | Azure OpenAI, AI services (data residency) |

---

## Agent lifecycle (deploy, version, retire)

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **AI/ML runtime & agents** | Vertex AI (Agent Builder, models, endpoints) | Bedrock, SageMaker | Azure AI / Azure OpenAI, AI Studio |
| **Orchestration / pipelines** | Vertex AI Pipelines, Cloud Run | Step Functions, SageMaker Pipelines | Azure ML pipelines, Logic Apps |
| **CI/CD** | Cloud Build, Artifact Registry, Deployment Manager | CodePipeline, CodeBuild, ECR | Azure DevOps, GitHub Actions, ACR |
| **Versioning** | Model Registry, endpoint versions (Vertex) | SageMaker model registry, versioned endpoints | Azure ML model registry, deployment versions |

---

## Compliance & controls

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **Compliance programs** | Assured Workloads, Compliance reports | Artifact (SOC, ISO, etc.), Config | Compliance Manager, Trust Center |
| **Config / posture** | Security Command Center, Assured OSS | Config, Security Hub | Defender for Cloud, Defender CSPM |
| **Safety & security (agents)** | **Vertex AI** (Gemini content filters, system instructions, evaluation, tracing); **ADK** (callbacks, plugins, Tool Context, in-tool guardrails); **Model Armor** (content safety API); **Vertex AI Code Execution API** / **Code Interpreter Extension** (sandboxed execution); **VPC Service Controls (VPC-SC)** (perimeter, data exfiltration control) | _To be filled_ | _To be filled_ |

---

## How to use this mapping

- When writing **Google Cloud** sections in consolidated docs (01–07), name the **exact GCP service** from the table (e.g. Cloud Logging, Secret Manager).
- When adding **AWS** or **Azure** content, fill the corresponding cell and reference the same service in the consolidated doc.
- Keep this file as the single source of truth for "which cloud service this governance is part of" per hyperscaler.

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | Sign in with email (Identity Platform) | [Google Cloud](https://docs.cloud.google.com/identity-platform/docs/sign-in-user-email) | `content/ingested/authentication/gcp-identity-platform-sign-in-email.md` |
| 2 | Authentication methods at Google | [Google Cloud](https://docs.cloud.google.com/docs/authentication) | `content/ingested/authentication/gcp-authentication-methods.md` |
| 3 | RFC 6749 OAuth 2.0 | [IETF](https://datatracker.ietf.org/doc/html/rfc6749) | `content/ingested/authentication/rfc6749-oauth2-authorization-framework.md` |
| 4 | Agent identity (Vertex AI Agent Engine) | [Google Cloud](https://docs.cloud.google.com/agent-builder/agent-engine/agent-identity#create-agent-identity) | `content/ingested/authentication/gcp-vertex-ai-agent-engine-agent-identity.md` |
| 5 | Authenticate to Google and Google Cloud MCP servers | [Google Cloud](https://docs.cloud.google.com/mcp/authenticate-mcp) | `content/ingested/authentication/gcp-mcp-authenticate-mcp.md` |
| 6 | MCP OAuth implementation (Upstash; pattern only) | [Upstash](https://upstash.com/blog/mcp-oauth-implementation) | `content/ingested/authentication/upstash-mcp-oauth-implementation.md` |
| 7 | MCP with Cloud Run and Apigee | [Medium](https://medium.com/@apigeek/deploying-and-securing-mcp-servers-at-scale-with-google-cloud-run-and-apigee-d06ea39e4235) | `content/ingested/authentication/apigee-cloud-run-mcp-servers-securing.md` |
| 8 | Cloud API Registry + Vertex AI Agent Engine (MCP) | [Google Developer forums](https://discuss.google.dev/t/where-is-the-mcp-server-deploy-your-agent-with-cloud-api-registry-on-vertex-ai-agent-engine/298130) | `content/ingested/authentication/gcp-cloud-api-registry-mcp-vertex-ai-agent-engine.md` |
| 9 | Apigee as LLM/agent gateway (Part 1 & 2) | [Medium Part 1](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-part1-2-220d1f88b4b4), [Part 2](https://medium.com/@apigeek/harnessing-apigee-api-management-platform-as-an-llm-gateway-implementation-walkthrough-part-2-2-09afb4c4b093) | `content/ingested/governance/apigee-llm-gateway-cloud-run-part1.md` |
| 10 | Safety and Security for AI Agents (ADK) | [Google ADK docs](https://raw.githubusercontent.com/google/adk-docs/main/docs/safety/index.md) | `content/ingested/governance/google-adk-safety-security-ai-agents.md` |
