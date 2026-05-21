
# Data Room — Settings

Strictly limited surface. Anything not in the allow-list is silently dropped.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/agent-tool-dataroom-update-settings`

Auth: user JWT, role `editor`+.

## Request

```json
{
  "projectId": "uuid",
  "patch": {
    "name": "Acme",
    "logo_url": "https://…",
    "brand_color": "#1a1a2e",
    "secondary_color": "#e94560",
    "cover_image_url": "https://…",
    "default_portal_intro": "Welcome to our data room…"
  }
}
```

## Allowed keys

| Key | Type | Notes |
| --- | --- | --- |
| `name` | string | Project name |
| `logo_url` | url | |
| `brand_color` | hex | Primary brand color |
| `secondary_color` | hex | Accent |
| `cover_image_url` | url | Investor portal hero |
| `default_portal_intro` | string | Markdown intro shown to investors |

**Not allowed here**: tagline, sector, currency, slug, custom domain, founder bio, financials, anything else. Those live in blocks (see [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md)) or are managed by the founder in the UI.

## Response

```json
{ "success": true, "project": { "...": "..." }, "applied": ["name","brand_color"] }
```

If `patch` contains no allowed keys: `{ success:false, error:"no allowed fields in patch" }`.

## Related

- [dataroom-managing-blocks](../dataroom-managing-blocks/SKILL.md) — for everything that is not an infrastructure setting.
