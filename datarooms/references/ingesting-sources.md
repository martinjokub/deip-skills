
# Data Room — Ingest Sources

Pure extraction tool. It does **not** persist anything. Chain it into `agent-tool-dataroom-upsert-block` (with `merge:true`) or `agent-tool-dataroom-update-knowledge`.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/agent-tool-dataroom-ingest`

Auth: user JWT, role `editor`+.

## Request

```json
{ "projectId": "uuid", "kind": "url" | "document_id", "value": "https://… | dr_documents.id" }
```

### Kinds

- **`url`** — fetches the page, strips HTML/CSS/JS, returns up to **20 000 chars** of plain text.
- **`document_id`** — must reference a `dr_documents.id` that belongs to the same `projectId`. Returns the document title + type + description (does not OCR or download the file).

## Response

```json
{
  "success": true,
  "kind": "url",
  "value": "https://example.com/about",
  "summary": "Fetched … (12345 chars of text).",
  "extractedText": "…raw text…",
  "hint": "Use this text to fill block fields via agent-tool-dataroom-upsert-block (merge:true) and Settings via agent-tool-dataroom-update-settings."
}
```

## Recommended chains

1. **Enrich a block from a public page**
   `ingest(url) → summarize → upsert-block(blockKey, data, merge:true)`
2. **Capture a long-form note from a doc**
   `ingest(document_id) → update-knowledge(appendEntry, tags:["internal"])`
3. **Bulk URL crawl**
   Loop `ingest(url)` over a list, dedupe text, then write one consolidated entry instead of many tiny ones.

## Limits

- Text is hard-capped at 20 000 chars per call.
- HTML-only; no PDF/OCR. For PDFs, ask the user to upload via the founder UI and then call with `kind: "document_id"`.
- User-Agent is `Mozilla/5.0 FundrBot/1.0`; sites that block bots will return non-200.

## Related

- [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md)
- [dataroom-knowledge-base](../dataroom-knowledge-base/SKILL.md)
