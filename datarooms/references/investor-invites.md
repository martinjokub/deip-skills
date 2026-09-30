
# Data Room — Investor Invites

Sends a branded email to the recipient of an existing `dr_shares` row. The share itself (and its access token) must be created beforehand via the founder UI — this tool does not provision new shares.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/dr-invite-investor`

Auth: user JWT, role `editor`+ on the project that owns the share (enforced via RLS on `dr_shares`).

## Request

```json
{
  "shareId": "uuid-of-dr_shares-row",
  "message": "Optional personal note up to 1000 chars"
}
```

## Response

```json
{ "success": true, "sent": true, "to": "investor@example.com" }
```

Error shapes:
- `unauthorized` — missing/invalid JWT.
- `missing_share_id` — `shareId` not provided.
- `share_not_found` — share doesn't exist or RLS hid it from the caller.
- `no_recipient_email` — share has no recipient email yet; ask the user to add one in the UI first.
- `project_not_found` — share points at a deleted project.

## Email behavior

- Sender + branding pulled from `dr_projects` (`name`, `startup_name`, `brand_color`, `founder_names`).
- Link points at the project's active custom domain (`dr_domains.status = 'active'`) if one exists, otherwise the default `dr.deip.app` origin.
- Includes the access token; treat the URL as a credential and never log it.

## When to call

- After the founder finalizes a share in the UI and asks the agent to "send the invite".
- For re-sends after the recipient lost the original email.

Do **not** call in a loop to "warm up" investors — one send per share per request.

## Related

- [dataroom-public-portal](../dataroom-public-portal/SKILL.md) — the destination of the magic link.
