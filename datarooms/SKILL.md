---
name: datarooms
description: DEIP Data Room module — read a founder's room snapshot, write/update blocks, ingest URLs or documents, manage AskFounder knowledge entries, update room settings, send investor invites, and read published rooms as an investor. Use for any task involving a startup Data Room.
---

# Data Rooms

The Data Room module lets a founder publish a structured, investor-facing snapshot of their startup. External agents (Claude, Antigravity, Cursor, custom MCP clients) read and write that snapshot through a small set of edge functions.

Start here, then load the one reference file that matches your task.

## Always start with this

Before any write, read [`references/reading-project.md`](references/reading-project.md) and call the read endpoint. You need the current snapshot to know what already exists, what's empty, and which field keys the catalog accepts for each block.

## Task → reference file

| Task | Read |
| --- | --- |
| Understand hierarchy, auth, base URL, error shape | [`references/overview.md`](references/overview.md) |
| Read a full Data Room snapshot before doing anything | [`references/reading-project.md`](references/reading-project.md) |
| Update structured block content (problem, traction, team, financials, media, …) | [`references/managing-blocks.md`](references/managing-blocks.md) |
| Extract text from a URL or uploaded document (chain into blocks/knowledge) | [`references/ingesting-sources.md`](references/ingesting-sources.md) |
| Append / update / replace / delete AskFounder long-term knowledge entries (decisions, FAQs, persistent context) | [`references/knowledge-base.md`](references/knowledge-base.md) |
| Change room name, logo, brand color, intro copy | [`references/settings.md`](references/settings.md) |
| Send a branded magic-link email invite to a specific investor | [`references/investor-invites.md`](references/investor-invites.md) |
| Act as an investor browsing a published room (slug / share token / custom domain) | [`references/public-portal.md`](references/public-portal.md) |

## Common conventions

- **Base URL**: `https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/`
- **Auth**: user JWT for founder/editor actions, anonymous `apikey` only for the public portal.
- **Roles**: `viewer` reads, `editor`+ writes. All writes are RLS-enforced per project.
- **Project identity**: every write call requires `projectId` (the `dr_projects.id`).
- **Errors**: every endpoint returns HTTP 200 with `{ success: false, error: "..." }` on failure.

See `references/overview.md` for the full hierarchy and endpoint conventions.
