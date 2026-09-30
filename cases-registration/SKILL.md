# cases-registration

> Self-check before opening a `validation_error` case. Prevents false-positive cases like CASE-130 ("Invalid UUID for skill id") that misattribute errors to the wrong tool.

## When to read this skill

Before calling `register_case` with `type='validation_error'` — especially when the error message mentions:
- "Invalid UUID"
- "Validation failed"
- "must be a valid X format"

## Tools that DO enforce UUID format

Only the **api-v1 item/category/subcategory** family validates UUID input:

- `api_v1_get_item`, `api_v1_update_item`, `api_v1_delete_item`
- `api_v1_get_category`, `api_v1_update_category`, `api_v1_delete_category`
- `api_v1_get_subcategory`, `api_v1_update_subcategory`, `api_v1_delete_subcategory`

If your error mentions "Invalid UUID" and the tool you called is **NOT** in that list, the error is misattributed. Do not register a case — re-inspect the actual tool's schema.

## Tools that accept TEXT identifiers (NOT UUIDs)

These accept human-readable slugs / arbitrary text strings:

| Tool | ID field | Format |
|---|---|---|
| `create_internal_skill` | `id` | text slug, e.g. `deip-new-feature` |
| `delete_skill` | `skill_id` | text slug |
| `sync_skills_repo` | `repo_id` | UUID (but errors are surfaced as text, not "Invalid UUID") |
| `create_agent`, `update_agent`, `delete_agent` | `code` | text slug, e.g. `client-profiler` |
| `register_case`, `update_case` | `case_id` | UUID — but server resolves by `case_number` too |

The DB column `ai_agents_skills.id` is **`text`**, not `uuid`. Skill IDs can be any unique string.

## Required `error_context` fields for validation_error cases

When `type='validation_error'`, always include:

```json
{
  "tool_name": "<exact tool name you called>",
  "tool_args_sample": { "...redacted args..." },
  "raw_error": "<verbatim error string from the tool response>"
}
```

The server now **rejects** `validation_error` cases mentioning "Invalid UUID" if `error_context.tool_name` is missing or names a tool that doesn't UUID-validate (CASE-130 guardrail, v0.9.1.530).

## Self-check checklist

1. ✅ Did the tool actually return the error? (Don't infer from a prior conversation.)
2. ✅ Is the named tool in the UUID-validating list above? If not, the "Invalid UUID" attribution is wrong.
3. ✅ Did you include `tool_name` and `raw_error` in `error_context`?
4. ✅ Did you check fixed/declined cases first via `search_cases` to avoid duplicates?
5. ✅ For `tool_error` with `"Tool X does not exist"` or `"is not authorized to be called"`, check the **server alias map** (`LEADHUB_ALIASES` in `ai-agents-chat/index.ts`) and the target agent's `parent_agents` first — many of these are LLM hallucinations rewritten transparently or simple delegation-graph mismatches, not real bugs. Declined as `not_applicable` is the correct disposition.
6. ✅ For `vision_*` / image-hallucination cases, verify the user message in question actually carries `metadata.attachments` AND was sent **after** the most recent vision-related changelog entry (currently v0.9.2.5 base64 inlining). If either is false → declined as `not_applicable` with a pointer to the relevant version. CASE-134/135 are the canonical false positives.
7. ✅ For `vision_context` / "specialist couldn't see attached file" cases, verify the user message has `metadata.attachments` AND `metadata.vision_context` is missing or empty AND the message was sent **after** v0.9.2.8 (vision-context cache + sub-agent propagation). If either is false → declined as `not_applicable`. CASE-VisionContext is the canonical reference.
8. ✅ **ENFORCED SERVER-SIDE (v0.9.2.9):** Vision/PDF case registrations are pre-checked. If the *current* user message has no matching attachment in `metadata.attachments`, `register_case` returns `{ success: false, blocked: true, reason: 'vision_precheck_failed' }` (or `pdf_precheck_failed`) and **never inserts a row**. A `vision_precheck_failed` tool result is **final** — do **not** re-attempt the registration with a renamed title or different category. Reply to the user explaining the image/PDF is no longer attached and ask them to re-attach if the issue is real.
9. ✅ **ENFORCED SERVER-SIDE (v0.9.2.16):** `efficiency_auditor` case registrations are pre-checked. If `error_context.tool_name` is set and the named tool does NOT appear in the `tool_calls` of the message referenced by `error_context.source_assistant_message_id`, OR the named tool is not in the claimed `error_context.agent_code` agent's configured tool set, `register_case` is rejected. Do not infer tool failures from text — only register cases for tools that actually appear in the source message's `tool_calls`. CASE-136 is the canonical false positive (auditor invented three `update_item_api` failures against an Echo text-only reply; Echo only has the direct-DB `update_item`).

## Reference cases

- **CASE-114, 116, 118, 119** — real api-v1 UUID validation issues (fixed).
- **CASE-130** — declined false positive: agent reported "Invalid UUID for skill id" against `create_internal_skill`, which does not UUID-validate.
- **CASE-124** — declined duplicate of CASE-125 (register_case type validation already remapped server-side).
- **CASE-136** — declined false positive: efficiency_auditor reported three `update_item_api` failures against assistant message `d8a25298` whose `tool_calls` was NULL; the claimed agent (Echo) doesn't have `update_item_api` in its tool set at all.
