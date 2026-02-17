---
source_url: "https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless"
source_title: "Jump Start Solution: Ecommerce platform with serverless computing | Cloud Architecture Center | Google Cloud"
ingested_date: "2025-02-17"
topic: "governance"
related_urls:
  - "https://console.cloud.google.com/products/solutions/details/ecommerce-platform-serverless"
  - "https://accounts.google.com/v3/signin/identifier?continue=...&service=cloudconsole&flowName=GlifWebSignIn&flowEntry=ServiceLogin"
google_services:
  - "Google Account / accounts.google.com (sign-in)"
  - "Cloud Console (console.cloud.google.com)"
  - "Error Reporting"
  - "Cloud Trace"
  - "Cloud Logging"
  - "Firebase Hosting"
  - "Cloud Storage"
  - "Secret Manager"
  - "Cloud SQL"
  - "Cloud Run"
  - "IAM"
  - "Cloud Build (console deployment)"
  - "Resource Manager (projects)"
  - "Artifact Registry / Container Registry (images)"
  - "Firebase (Hosting, console)"
---

# Sign-in flow and eCommerce Platform Serverless solution — flow and Google Cloud services

This document captures (1) the **user flow** from the provided sign-in URL to the solution, and (2) the **eCommerce Platform Serverless** Jump Start Solution: all Google Cloud services, request flow, and deployment flow, with citations to the official architecture guide.

**Primary source:** [Jump Start Solution: Ecommerce platform with serverless computing](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless) (Cloud Architecture Center). Console solution page: [Ecommerce platform with serverless computing](https://console.cloud.google.com/products/solutions/details/ecommerce-platform-serverless).

---

## Part 1: Sign-in and console flow (from the URL you provided)

The URL you shared is the **Google Account sign-in page** (`accounts.google.com/v3/signin/identifier`) with a **continue** parameter that redirects to the Cloud Console after successful authentication.

### Flow

1. **User opens link** → Browser loads `accounts.google.com` sign-in (Email or phone → Next → password/2FA if required).
2. **Parameters in URL (relevant):**
   - `service=cloudconsole` — session is for Google Cloud Console.
   - `flowName=GlifWebSignIn`, `flowEntry=ServiceLogin` — web sign-in flow.
   - `continue=https://console.cloud.google.com/products/solutions/details/ecommerce-platform-serverless` — after sign-in, user is redirected to the **eCommerce Platform Serverless** solution page in Cloud Console.
3. **After successful sign-in** → User lands on **Cloud Console** → **Products** → **Solutions** → **Ecommerce platform with serverless computing** (solution details: cost estimate, deployment time, **Deploy** button).
4. **Optional: Deploy through console** → User clicks **Deploy** → Step-by-step configuration → **Cloud Build** deploys the solution; status on **Solution deployments** page (Deploying → Deployed). Alternatively, deploy via **Terraform CLI** (e.g. from Cloud Shell).

### Google services in this flow

| Step | Google service / product |
|------|---------------------------|
| Sign-in | **Google Account** (accounts.google.com); **Cloud Console** (console.cloud.google.com) |
| Solution catalog | **Cloud Console** — Products → Solutions |
| Console deployment | **Cloud Build** (runs deployment); **Solution deployments** (console UI) |
| Post-deploy | All solution services (see Part 2) |

---

## Part 2: eCommerce Platform Serverless — Google Cloud products and request flow

From the [official architecture guide](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless).

### Products used by the solution

| Product | Role |
|---------|------|
| **Error Reporting** | Aggregates and displays errors from cloud services; groups by root cause. |
| **Cloud Trace** | Distributed tracing; request latency, RPC timing; OpenTelemetry integration. |
| **Cloud Logging** | Store, search, analyze, monitor, alert on log data from GCP and other clouds. |
| **Firebase Hosting** | Fully managed hosting for web app and static content (e.g. Lit-based frontend). |
| **Cloud Storage** | Object storage for static assets (e.g. product images); geo-redundant. |
| **Secret Manager** | Store and access secrets (DB passwords, API keys, TLS certs) at runtime. |
| **Cloud SQL** | Managed PostgreSQL — relational backend for the application. |
| **Cloud Run** | Serverless containerized API server (Django REST Framework). |

### Request flow (application)

1. **Cloud Logging, Cloud Trace, Error Reporting** — Ingest logs, OpenTelemetry traces, and errors from Cloud Run and other components for monitoring and troubleshooting.
2. **Cloud SQL (PostgreSQL)** — Relational database for the Python application.
3. **Cloud Storage** — Static assets for the application.
4. **Secret Manager** — Configuration and secrets for the Python application.
5. **Cloud Run** — Web client calls API backend (Django on Cloud Run).
6. **Firebase Hosting** — Serves the client frontend (Lit/web components).

### Deployment flow

- **Console:** Cloud Console → Solution details → **Deploy** → configuration pane → **Cloud Build** deploys resources; temporary **service account** used for deploy (roles: Cloud Storage Admin, Secret Manager Admin, Cloud Run Admin, Project IAM Admin, Service Account User/Admin, Firebase Hosting Admin, Compute Engine Admin/Network Admin, Cloud SQL Admin, etc.); permissions revoked after deploy/delete.
- **Terraform:** Terraform config in GitHub; deploy via Terraform CLI (e.g. Cloud Shell); resources not shown in Console Solution deployments page.
- **IAM:** To deploy, user needs e.g. Service Usage Admin, Service Account Admin, Project IAM Admin, Cloud Infrastructure Manager Admin (for `config.deployments.*`), Service Account User; project needs Project Creator for new project.

### Observability (governance relevance)

- **Logging:** Cloud Run sends logs to **Cloud Logging**; log-based alerts supported.
- **Tracing:** **Cloud Trace** with OpenTelemetry (auto-instrumentation for Django); spans for request lifetime.
- **Errors:** **Error Reporting** aggregates errors from the app and services.

---

## Reference

- **Architecture guide:** [Jump Start Solution: Ecommerce platform with serverless computing](https://docs.cloud.google.com/architecture/application-development/ecommerce-serverless)
- **Console solution page:** [Ecommerce platform with serverless computing](https://console.cloud.google.com/products/solutions/details/ecommerce-platform-serverless)
- **Sign-in (flow entry):** Google Account sign-in with `continue` to the solution URL uses `service=cloudconsole`; see [Google Account](https://accounts.google.com/) and [Cloud Console](https://console.cloud.google.com/).
- **Cited:** 2025-02-17
