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
| **Identity / SSO** | IAM, Identity Platform, Workforce Identity Federation | IAM, Cognito, IAM Identity Center | Azure AD / Entra ID, B2C |
| **Service accounts / workload identity** | IAM Service Accounts, Workload Identity (GKE, etc.) | IAM Roles, IRSA (EKS) | Managed identities, service principals |
| **API keys** | API Keys (Consumer), IAM for APIs | IAM + API Gateway / usage plans | API Management, Azure AD tokens |
| **Secrets** | Secret Manager | Secrets Manager, Parameter Store | Key Vault (secrets) |

---

## Authorization & access control

| Governance area | Google Cloud | AWS | Azure |
|-----------------|--------------|-----|--------|
| **RBAC / policies** | IAM (roles, custom roles, org policies) | IAM (policies, roles), ABAC | Azure RBAC, Azure Policy |
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

---

## How to use this mapping

- When writing **Google Cloud** sections in consolidated docs (01–07), name the **exact GCP service** from the table (e.g. Cloud Logging, Secret Manager).
- When adding **AWS** or **Azure** content, fill the corresponding cell and reference the same service in the consolidated doc.
- Keep this file as the single source of truth for "which cloud service this governance is part of" per hyperscaler.

## Sources & citations

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
|   |       |     |                           |
