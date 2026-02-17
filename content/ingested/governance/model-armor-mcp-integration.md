---
source_url: "https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration"
source_title: "Model Armor integration with Google Cloud MCP servers | Google Cloud Documentation"
ingested_date: "2025-02-17"
topic: "governance"
google_services:
  - "Model Armor"
  - "Google Cloud MCP servers"
  - "Cloud Logging"
---

# Model Armor integration with Google Cloud MCP servers

**Google Cloud service:** [Model Armor](https://cloud.google.com/model-armor) — integration with Google Cloud MCP servers for sanitizing MCP tool calls and responses.

**Preview.** Model Armor helps secure agentic AI applications by sanitizing MCP tool calls and responses, mitigating risks such as prompt injection and sensitive data disclosure.

## Before you begin

1. Enable the MCP servers you want to use
2. Enable the Model Armor API in your project
3. If you have data residency requirements, configure a log sink before enabling Cloud Logging

## Configure protection for Google and Google Cloud remote MCP servers

1. **Set up Model Armor floor setting** with MCP sanitization enabled:
```bash
gcloud model-armor floorsettings update \
  --full-uri='projects/PROJECT_ID/locations/global/floorSetting' \
  --enable-floor-setting-enforcement=TRUE \
  --add-integrated-services=GOOGLE_MCP_SERVER \
  --google-mcp-server-enforcement-type=INSPECT_AND_BLOCK \
  --enable-google-mcp-server-cloud-logging \
  --malicious-uri-filter-settings-enforcement=ENABLED \
  --add-rai-settings-filters='[{"confidenceLevel": "HIGH", "filterType": "DANGEROUS"}]'
```

2. **Enable Model Armor protection** for remote MCP servers:
```bash
gcloud beta services mcp content-security add modelarmor.googleapis.com --project=PROJECT_ID
```

3. **Verify** Google MCP traffic is sent to Model Armor:
```bash
gcloud beta services mcp content-security get --project=PROJECT_ID
```

## Verify Model Armor protection

1. Call an MCP tool with a harmful value (e.g., phishing test URL)
2. Verify the call is blocked
3. Check Cloud Logging for `modelarmor.googleapis.com/detection` logs

## Disable Model Armor

- **Disable in project:** `gcloud beta services mcp content-security remove modelarmor.googleapis.com --project=PROJECT_ID`
- **Disable scanning MCP traffic:** Remove `GOOGLE_MCP_SERVER` from floor setting integrated services

## Reference

- **URL:** [Model Armor integration with Google Cloud MCP servers](https://docs.cloud.google.com/model-armor/model-armor-mcp-google-cloud-integration)
- **Cited:** 2025-02-17
