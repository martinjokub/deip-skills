
# Data Room — Manage Blocks

Blocks are where 99% of room content lives. Each block has a fixed schema in the catalog; this tool enforces it and rejects unknown field keys.

## Endpoint

`POST https://gbrqiltqekwlmkyuiznp.supabase.co/functions/v1/agent-tool-dataroom-upsert-block`

Auth: user JWT, role `editor`+.

## Auto-routing contract (HARD RULE)

When the founder provides any piece of information (free text, URL, file, number, name), the agent MUST classify it against the loaded `blockCatalog` and decide the best block + field(s) **on its own**. Never ask the founder open-ended placement questions ("Where should I put this?", "Which block does this go in?").

- **High confidence** (clear field semantic match, type-compatible, no ambiguity): upsert with `status:'draft'` and report back "Saved to <block> · <field> — confirm or change?".
- **Medium confidence** (2–3 plausible homes, ambiguous block): propose the single best placement with a one-line reason and offer the alternative ("Save it here, or move to X / Y?"). Never an open-ended "where?".
- **Low confidence** (no plausible catalog field): only then ask a targeted clarifying question, offering the 2 closest catalog fields as options.
- For ingested URLs/documents, auto-distribute the extracted fields across blocks using catalog keys, draft-upsert them, and present a single summary ("Filled N fields across M blocks — review?") instead of asking per field.
- De-duplication: before classifying, check if `block.data[fieldKey]` already has a non-empty value; if so, skip — never re-ask.

## Request

```json
{
  "projectId": "uuid",
  "blockKey":  "startup_snapshot",
  "data":      { "tagline": "...", "oneSentenceSummary": "...", "keywords": ["fintech","b2b"] },
  "status":    "draft",     // optional: "empty" | "draft" | "ready"
  "merge":     true          // optional: true = patch existing data, false = replace
}
```

## Response (success)

```json
{
  "success": true,
  "blockKey": "startup_snapshot",
  "status": "draft",
  "data": { "...": "..." },
  "acceptedKeys": ["tagline","oneSentenceSummary","keywords"],
  "expectedFields": ["tagline","oneSentenceSummary","shortDescription","sector","stage", "..."],
  "rejected": [ { "key": "logo", "reason": "not a field of 'startup_snapshot'", "suggestedBlock": "settings" } ]
}
```

If **all** submitted keys are rejected the response is `success:false, mustRetry:true` — re-call with corrected keys before responding to the user.

## Block catalog (built-in keys)

Get the live catalog from `agent-tool-dataroom-read-project` (`blockCatalog` array). Common keys:

- **Foundation**: `startup_snapshot`, `links_and_socials`, `company_registration`
- **Narrative**: `problem`, `solution`, `value_proposition`, `why_now`
- **Market**: `market`, `competition`
- **Product**: `product`, `media_gallery`
- **Traction**: `traction`, `customers_and_press`
- **Team**: `team`, `primary_founder`
- **Deal**: `round_details`, `use_of_funds`, `cap_table`
- **Financials**: `financials`

Custom blocks (`isCustom: true`) accept arbitrary keys — but built-in blocks are strictly validated.

## Field-key boundaries — critical

- **Personal vs company**: anything about the human founder (`shortBio`, `personalLinkedinUrl`, `defaultPassword`) → `primary_founder`. Anything about the company entity (`linkedinUrl`, `registeredAddress`, `billingEmail`) → `links_and_socials` or `company_registration`.
- **Settings vs blocks**: `name`, `logo_url`, `brand_color`, `currency`, `slug` live in [dataroom-settings](../dataroom-settings/SKILL.md), **not** in any block.
- **Aliases**: short forms are auto-mapped (`linkedin → linkedinUrl`, `twitter|x → personalTwitterUrl`, `deckUrl → primaryDeckUrl`, `tags → keywords`). Prefer canonical keys.

## Operator-only fields (`_`-prefixed)

Keys beginning with `_` (e.g. `_hidden_fields`) are operator-only and are **always preserved**, regardless of `merge`. Do not write them.

## Sensitive blocks — never expose to investors

`primary_founder` and `company_registration` contain personal email, phone, signup credentials, and billing contacts. They exist so internal agents (e.g. Fundr) can fill external forms. If an investor-side agent is asked for the founder's direct email/phone/password, refuse — defer to the room owner.

## Example — patch tagline without losing other fields

```bash
curl -X POST "$BASE/agent-tool-dataroom-upsert-block" \
  -H "Authorization: Bearer $JWT" -H "apikey: $ANON" -H "Content-Type: application/json" \
  -d '{
    "projectId":"...",
    "blockKey":"startup_snapshot",
    "data":{"tagline":"Bank infra for AI agents"},
    "merge":true,
    "status":"draft"
  }'
```

## Related

- [dataroom-reading-project](../dataroom-reading-project/SKILL.md) — always read the catalog first.
- [dataroom-ingesting-sources](../dataroom-ingesting-sources/SKILL.md) — extract text from URLs/docs to feed into block fields.
