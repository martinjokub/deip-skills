
# Data Room — Public Portal

Use this when the agent is acting as an **investor** browsing a published room, not as the founder editing it.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/dataroom-public-fetch`

Auth: anonymous (`apikey: $ANON`). A user JWT is optional; if present and the caller is the project owner, the room is returned even without a share.

## Request — three resolution modes

```json
// 1. By public slug
{ "slug": "acme" }

// 2. By share token (magic-link invites)
{ "shareToken": "tok_…" }

// 3. By custom domain (resolves the project from the host header)
{ "host": "investors.acme.com" }
```

## Response (shape)

```json
{
  "success": true,
  "project": { "name": "Acme", "brand_color": "#…", "logo_url": "…", "currency": "EUR" },
  "variant": { "id": "…", "name": "Angels", "introCopy": "…", "ctaCopy": "…" },
  "blocks":  [ { "block_key": "problem", "data": { "...": "..." }, "portal_visible": true } ],
  "documents": [ { "id": "...", "title": "Pitch deck", "type": "deck" } ],
  "askFounder": { "enabled": true, "personaPrompt": "…" }
}
```

## What is filtered out

- Blocks with `portal_visible: false`.
- Blocks listed in `variant.visibilityRules.hiddenBlockKeys`.
- Always: `primary_founder` and `company_registration` (sensitive — see overview).
- Knowledge entries tagged `internal` or `sensitive`.

## Custom-domain flow

1. Founder configures a custom domain (e.g. `investors.acme.com`).
2. `dataroom-resolve-host` maps the host to a `projectId`.
3. `dataroom-validate-host` confirms DNS/cert.
4. `dataroom-public-fetch` returns the room when called with `{ "host": "investors.acme.com" }`.

Agents acting on a custom domain should pass the literal `Host` header value in the `host` field — never strip subdomains.

## Related

- [askfounder-data-room-grounding](../askfounder-data-room-grounding/SKILL.md) — rules for chat agents grounded in the fetched payload.
- [dataroom-investor-invites](../dataroom-investor-invites/SKILL.md) — how share tokens are issued.
