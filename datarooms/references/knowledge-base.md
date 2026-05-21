
# Data Room — Knowledge Base

Knowledge entries are the founder's persistent memory for a room. AskFounder uses them at runtime alongside block data (entries tagged `internal` or `sensitive` are filtered out before reaching investors).

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/agent-tool-dataroom-update-knowledge`

Auth: user JWT, role `editor`+.

## Operations (pick exactly one per call)

### `appendEntry` — atomic note (preferred)

```json
{
  "projectId": "uuid",
  "appendEntry": {
    "title": "Why we picked Stripe Treasury",
    "content": "...up to 10 000 chars...",
    "tags": ["internal","decision"],
    "sourceRef": "optional URL or doc id"
  }
}
```

### `updateEntry` — only entries the agent itself created (`source = 'fundr'`)

```json
{ "projectId": "uuid", "updateEntry": { "id": "uuid", "content": "…", "title": "…", "tags": ["…"] } }
```

### `appendMarkdown` / `replaceMarkdown` — free-form blob on the project row

```json
{ "projectId": "uuid", "appendMarkdown": "## Founder note\n…" }
{ "projectId": "uuid", "replaceMarkdown": "# Compact knowledge base\n…" }
```

Hard cap **200 000 chars** for the markdown blob. Prefer `appendEntry` for atomic notes — it scales better and shows up individually in the UI.

### `deleteEntry`

```json
{ "projectId": "uuid", "deleteEntry": "entry-uuid" }
```

If the entry was authored by the founder (`source != 'fundr'`) the call fails unless you pass `confirmDeleteUserEntry: true` — only do this when the founder explicitly asked you to remove it.

## Tag semantics

| Tag | Effect |
| --- | --- |
| `internal` | Hidden from AskFounder investor context |
| `sensitive` | Hidden from AskFounder investor context |
| anything else | Visible to AskFounder; quoteable to investors |

Max 12 tags per entry. Each tag must be a string.

## Response

```json
{ "success": true, "entry": { "id": "...", "content": "...", "tags": ["..."] } }
```

On error returns `{success:false, error:"..."}` with HTTP 200.

## Related

- [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md) — for structured fields, not free-form notes.
- [askfounder-data-room-grounding](../askfounder-data-room-grounding/SKILL.md) — explains how entries are surfaced to AskFounder.
