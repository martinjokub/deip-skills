
# Data Room — Overview

The Data Room module lets a founder publish a structured, investor-facing snapshot of their startup. External agents (Claude, Antigravity, Cursor, custom MCP clients) can read and write that snapshot through a small set of edge functions.

## Hierarchy

```
dr_project           (the startup project — entry point, identified by projectId)
├── dr_data_room     (one main room per project; agents always target is_main=true)
│   ├── dr_blocks    (structured content: problem, traction, team, financials, …)
│   ├── dr_block_groups
│   └── dr_variants  (audience-specific views: e.g. "Angels" vs "VCs")
├── dr_documents     (uploaded files + external URLs)
├── dr_knowledge_entries  (AskFounder long-term memory)
└── dr_shares        (per-investor magic-link access)
```

## Endpoints

Base URL: `https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1`

| Skill | Endpoint | Auth |
| --- | --- | --- |
| [dataroom-reading-project](../dataroom-reading-project/SKILL.md) | `/agent-tool-dataroom-read-project` | user JWT (viewer+) |
| [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md) | `/agent-tool-dataroom-upsert-block` | user JWT (editor+) |
| [dataroom-knowledge-base](../dataroom-knowledge-base/SKILL.md) | `/agent-tool-dataroom-update-knowledge` | user JWT (editor+) |
| [dataroom-ingesting-sources](../dataroom-ingesting-sources/SKILL.md) | `/agent-tool-dataroom-ingest` | user JWT (editor+) |
| [dataroom-settings](../dataroom-settings/SKILL.md) | `/agent-tool-dataroom-update-settings` | user JWT (editor+) |
| [dataroom-public-portal](../dataroom-public-portal/SKILL.md) | `/dataroom-public-fetch` | anon + optional share token |
| [dataroom-investor-invites](../dataroom-investor-invites/SKILL.md) | `/dr-invite-investor` | user JWT (editor+) |

## Authentication

All editor-side endpoints expect a Supabase user JWT:

```
Authorization: Bearer <user_jwt>
Content-Type: application/json
apikey: <SUPABASE_ANON_KEY>
```

Role gate (`has_dr_role`): `viewer` < `editor` < `owner`. Reads need `viewer`; writes need `editor`.

## Response envelope

Every endpoint **always returns HTTP 200**. Success/failure is in the body:

```json
{ "success": true,  "...": "..." }
{ "success": false, "error": "project_not_found", "agentInstruction": "…", "mustRetry": false }
```

When `mustRetry: true` is returned, **do not respond to the user**: re-call the tool with the corrected payload first. When `agentInstruction` is present, follow it verbatim.

## Identifiers — never guess

- `projectId` is a UUID. If you don't have one, call `ask_user` to confirm it — never invent UUIDs.
- `project_not_found` means the UUID is hallucinated, **not** a permissions issue. Stop and ask.

## Sensitive-content rule

The blocks `primary_founder` and `company_registration` contain the founder's personal email, phone, signup credentials, and billing/registration contacts. They exist so internal agents can fill external forms. **Never expose them to investors** or include them in investor-facing summaries. See [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md).

## Related

- [askfounder-data-room-grounding](../askfounder-data-room-grounding/SKILL.md) — rules for agents acting as the founder's chatbot inside the public portal.
