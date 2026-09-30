---
name: deip-timebook
description: Read Time Book projects and log time entries / paid hours through the DEIP REST API or MCP tools, instead of uploading CSV/XLSX files.
---

# DEIP Time Book

Auth: personal API key (`x-api-key` header for REST; same key for MCP). Base: `https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/api-v1`.

Permissions follow your project role: owner/editor can write, viewer/commenter read only.

| MCP tool | REST |
|---|---|
| `timebook_list_projects` | `GET /timebook/projects` |
| `timebook_get_project` | `GET /timebook/projects/:id` (rates, tariffs, monthly delivered/paid/balance) |
| `timebook_list_entries` | `GET /timebook/projects/:id/entries?from=&to=&task_name=&billable=&limit=&offset=` |
| `timebook_add_entries` | `POST /timebook/projects/:id/entries` (`?dry_run=true` to preview) |
| `timebook_update_entry` | `PATCH /timebook/projects/:id/entries/:entryId` |
| `timebook_delete_entry` | `DELETE /timebook/projects/:id/entries/:entryId` |
| `timebook_list_ledger` | `GET /timebook/projects/:id/ledger` |
| `timebook_add_ledger_entry` | `POST /timebook/projects/:id/ledger` |

## Adding entries

```json
{
  "source_label": "ManicTime sync 2026-09",
  "entries": [
    { "task_name": "Client X — design", "start_at": "2026-09-29T09:00:00+02:00",
      "end_at": "2026-09-29T10:30:00+02:00", "notes": "wireframes", "billable": true,
      "external_id": "manictime-12345" }
  ]
}
```

- 1–1,000 entries per call; each at most 24 h; `end_at` after `start_at`.
- `duration_seconds` optional (computed from start/end).
- Duplicates are skipped — same hash as the file import, so re-sending is safe. `external_id` gives a stable retry key.
- Hourly rate and tariff are resolved automatically (exact task-name tariff first, then the project rate period).
- Response: `{ batchId, received, inserted, duplicates, entryIds }`. Dry run returns `{ new, duplicates, preview }`.
- Always run a dry run first for large batches.

## Ledger

`{ "period_month": "2026-09", "hours": 40, "kind": "payment", "note": "invoice 112" }` — `kind` is `payment` or `adjustment` (hours may be negative).

## Errors

`{ success: false, error: { code, message } }` — `VALIDATION_ERROR`, `NOT_FOUND`, `FORBIDDEN` (read-only role), `DB_ERROR`.

## Required key scopes
- Read tools: `timebook:read` (or `ep:timebook_list_projects`, `ep:timebook_get_project`, `ep:timebook_list_entries`, `ep:timebook_list_ledger`)
- `timebook_add_entries`, `timebook_update_entry`, `timebook_add_ledger_entry`: `timebook:write` or the matching `ep:<tool>`
- `timebook_delete_entry`: `timebook:delete` or `ep:timebook_delete_entry`
The project role (viewer/editor) still applies on top.
