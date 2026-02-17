---
source_url: "https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis"
source_title: "Register MCP APIs | Apigee | Google Cloud Documentation"
ingested_date: "2025-02-17"
topic: "governance"
google_services:
  - "Apigee API hub"
  - "Cloud API Registry"
---

# Register MCP APIs (Apigee API hub)

**Google Cloud service:** [Apigee API hub](https://cloud.google.com/apigee/docs/apihub) — supports Model Context Protocol (MCP) as a first-class API style alongside REST, gRPC, etc.

You can import, register, and manage MCP APIs and their associated tools directly within API hub, creating a centralized registry for your agentic APIs and services.

## Register an MCP API

### Console

1. Go to **API hub > APIs** page
2. Provide required API details
3. Under **System attributes**, set **API style** to **MCP**
4. Click **Register**

### REST

Use the Create API REST API with `api_style` set to `mcp-api`:

```json
{
  "display_name": "DISPLAY_NAME",
  "description": "DESCRIPTION",
  "api_style": {
    "enum_values": {
      "values": [{"id": "mcp-api"}]
    }
  }
}
```

## View MCP APIs

Filter APIs by style to find MCP APIs — select the **MCP** filter tag in the API list view.

## Reference

- **URL:** [Register MCP APIs](https://docs.cloud.google.com/apigee/docs/apihub/register-mcp-apis)
- **Cited:** 2025-02-17
