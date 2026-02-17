# Citation & Reference Standard

Every ingested article and every consolidated document **must** store the **citation** (URL) and a **reference** to the source.

## For ingested articles (URL → Markdown)

At the **top** of each ingested markdown file, include this block:

```markdown
---
source_url: "https://example.com/article"
source_title: "Article Title"
ingested_date: "YYYY-MM-DD"
topic: "observe" | "authentication" | "governance"
---
```

At the **end** of the file:

```markdown
## Reference
- **URL:** [Article Title](https://example.com/article)
- **Cited:** YYYY-MM-DD
```

## For consolidated documents

Each consolidated doc ends with **Sources & citations** in this format:

| # | Title | URL | Ingested file / Reference |
|---|-------|-----|---------------------------|
| 1 | ... | [link](url) | `content/ingested/.../filename.md` |

- **Title:** Short title of the source.
- **URL:** Full source URL (as markdown link).
- **Reference:** Path to ingested file or short note (e.g. "Official doc", "Internal wiki").

## Inline citations

When a specific claim or requirement comes from a source, cite it inline:

`Requirement X applies to agent deployments ([Source Title](url)).`

Or: `([1](url))` and list [1] in the Sources table.
