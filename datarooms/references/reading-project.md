
# Data Room — Read Project

Use this **first**, before any write. It returns everything the agent needs to know what already exists, what is empty, and which field keys the catalog accepts for each block.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/agent-tool-dataroom-read-project`

Auth: user JWT, role `viewer`+.

## Request

```json
{ "projectId": "uuid-of-dr_projects-row" }
```

## Response (success)

```json
{
  "success": true,
  "project":   { "id": "...", "name": "...", "startup_name": "...", "brand_color": "...", "currency": "EUR", "slug": "...", "knowledge_base": "..." },
  "room":      { "id": "...", "name": "Main", "status": "draft" },
  "blocks":    [ { "block_key": "startup_snapshot", "data": {...}, "status": "ready", "position": 0, "portal_visible": true } ],
  "blockGroups": [ { "id": "...", "name": "Foundation", "position": 0 } ],
  "documents": [ { "id": "...", "title": "Pitch deck v3", "type": "deck", "description": "...", "block": "media_gallery" } ],
  "knowledgeEntries": [ { "id": "...", "title": "...", "content": "...", "tags": ["..."], "source": "fundr" } ],
  "blockCatalog": [ { "key": "startup_snapshot", "fields": [...] } ]
}
```

## Error shapes

- `project_not_found` — UUID is wrong. Stop and ask the user, do not guess.
- `forbidden` — caller has no role on this project.

## How to use the response

1. **Catalog first — classify yourself.** `blockCatalog[].fields[].key` is the *only* allowed write surface for built-in blocks. Use the catalog (block key + field key + label + type + aliases) to **decide on your own** which block and field every piece of founder input belongs to. **Do NOT ask the founder which block or field to use** — that is your job, not theirs. Only ask a targeted clarifying question when no catalog field plausibly fits.
2. **Skip filled fields.** Block `data` already contains values — never overwrite verbatim founder content unless asked. Use `merge: true` on upsert.
3. **Documents are titles, not URLs.** `documents[].title` is what you may quote to the investor. **Never** fabricate a URL or claim to attach a file.
4. **Knowledge entries** are the founder's persistent memory for this room — entries tagged `internal` or `sensitive` are filtered out of investor-facing AskFounder context.

## Example

```bash
curl -X POST "$BASE/agent-tool-dataroom-read-project" \
  -H "Authorization: Bearer $JWT" -H "apikey: $ANON" -H "Content-Type: application/json" \
  -d '{"projectId":"62e06830-202e-442f-8111-1c50780e29df"}'
```

```ts
const { data } = await supabase.functions.invoke("agent-tool-dataroom-read-project", { body: { projectId } });
if (!data.success) throw new Error(data.error);
```

## Related

- [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md) — write blocks once you know the catalog.
- [dataroom-public-portal](../dataroom-public-portal/SKILL.md) — read-only view for investor-side agents (no JWT needed).
