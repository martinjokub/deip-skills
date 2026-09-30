# DEIP Error Handling & Agent Cases Register

## Overview

The Agent Cases Register (`ai_agents_cases` table) is a structured log for tracking, diagnosing, and resolving errors and edge cases encountered during agent execution. The `error_handler` sub-agent is responsible for managing this register.

## Cases Register Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | UUID | Case ID |
| `case_number` | integer | Sequential case number (CASE-001, CASE-002, ...) |
| `workspace_id` | UUID | Workspace where error occurred |
| `conversation_id` | UUID | Conversation that triggered the error |
| `user_id` | UUID | User who experienced the error |
| `type` | text | `tool_error`, `api_error`, `provider_error`, `validation_error`, `edge_case`, `timeout` |
| `severity` | text | `low`, `medium`, `high`, `critical` |
| `status` | text | `new`, `in_review`, `need_dev`, `solution_found`, `fixed`, `failed_to_fix`, `on_hold`, `declined` |
| `title` | text | Short summary |
| `error_message` | text | Raw error message |
| `error_context` | jsonb | Full context: `{ agent_code, tool_name, tool_args, stack_trace }` |
| `case_category` | text | `system_error` (default), `user_query`, or `lovable_task` (for dev bridge items) |
| `lovable_prompt_template` | text | Structured instructions for Lovable developer (when `case_category` is `lovable_task`) |
| `lovable_output_log` | text | Developer result / verification notes |
| `related_case_ids` | jsonb | Array of related case IDs |
| `solution` | text | Resolution steps |
| `solution_status` | text | `proposed`, `verified`, `failed` |
| `auto_resolve_pattern` | text | Pattern for automatic future resolution |
| `subscriber_user_ids` | jsonb | Users to notify when resolved |
| `resolved_at` | timestamptz | When marked as `fixed` or `declined` |
| `learning_status` | text | Academy status: `pending_curation`, `needs_review`, `learning_in_progress`, `skill_adopted`, `not_applicable` |
| `learning_confidence` | integer | Academy confidence score (0-100). How confident the system is in the learning. |
| `learning_explainer` | text | Academy explainer: what was learned from this case, in plain language. |

## Available Tools

### register_case (write)
Create a new case in the register.
```
register_case({
  type: "tool_error",
  severity: "medium",
  title: "Canvas node creation failed - duplicate key",
  error_message: "duplicate key value violates unique constraint",
  error_context: {
    agent_code: "canvas_manager",
    tool_name: "add_canvas_node",
    tool_args: { canvas_id: "...", item_id: "..." }
  }
})
```

For lovable_task cases (developer bridge):
```
register_case({
  type: "edge_case",
  severity: "medium",
  status: "need_dev",
  case_category: "lovable_task",
  title: "Fix sidebar collapse on mobile",
  error_message: "Sidebar does not collapse properly on viewports < 768px",
  lovable_prompt_template: "Fix the CategorySidebar collapse behavior on mobile..."
})
```

For cases with initial Academy triage:
```
register_case({
  type: "tool_error",
  severity: "medium",
  title: "Duplicate key on canvas node insert",
  error_message: "duplicate key value violates unique constraint",
  learning_status: "pending_curation",
  learning_confidence: 40,
  learning_explainer: "Possible pattern: pre-check with list_canvas_nodes before adding"
})
```

### search_cases (read)
Search for past cases matching criteria.
```
search_cases({ type: "tool_error", status: "fixed" })
```

### get_case (read)
Get a single case by ID.
```
get_case({ id: "case-uuid" })
```

### list_cases (read)
List cases for the workspace.
```
list_cases({ status: "new", limit: 20 })
```

### update_case (write)
Update case status, add solution, link cases.
```
update_case({
  id: "case-uuid",
  status: "fixed",
  solution: "Call list_canvas_nodes first to check for existing nodes before adding",
  solution_status: "verified",
  auto_resolve_pattern: "duplicate key.*canvas_nodes"
})
```

For Academy learning updates:
```
update_case({
  id: "case-uuid",
  learning_status: "skill_adopted",
  learning_confidence: 95,
  learning_explainer: "Always pre-check with list_canvas_nodes before add_canvas_node to avoid duplicate key errors. Verified across 3 workspaces."
})
```

For failed fix retries:
```
update_case({
  id: "case-uuid",
  status: "failed_to_fix",
  lovable_output_log: "Previous fix caused regression in sidebar rendering",
  lovable_prompt_template: "Revised instructions: ..."
})
```

### subscribe_to_case (write)
Subscribe user for notifications.
```
subscribe_to_case({ id: "case-uuid" })
```

## Error Handler Workflow

1. **Receive error context** from orchestrator (failing tool, args, error message, agent code)
2. **Search for matching past cases**: `search_cases({ type, status: "fixed" })`
3. **If match found with `auto_resolve_pattern`**: Attempt automatic fix, report result
4. **If match found without auto-resolve**: Report case number and status to user
5. **If no match**: Register new case with `status: new`, offer subscription
6. **Always report** case number (CASE-XXX) and current status back to orchestrator
7. **Academy triage**: After resolving or registering, set appropriate `learning_status`:
   - `pending_curation` — newly registered, needs review for learning potential
   - `needs_review` — has a solution but needs human verification before adoption
   - `learning_in_progress` — actively being tested/validated
   - `skill_adopted` — verified learning, integrated into agent behavior
   - `not_applicable` — no reusable learning (one-off error, user-specific)

## Auto-Resolve Pattern Syntax

The `auto_resolve_pattern` field contains a regex-like pattern matched against `error_message`. When a new error matches an existing `fixed` case's pattern, the error_handler can attempt the stored `solution` automatically.

Examples:
- `duplicate key.*canvas_nodes` → Pre-check with `list_canvas_nodes` before adding
- `Item not found with id` → Delegate to orchestrator to resolve the correct item ID via workspace item listing
- `Rate limit exceeded` → Wait 60 seconds and retry

## Severity Guidelines

| Severity | When to use |
|----------|-------------|
| `low` | Cosmetic issues, non-blocking warnings |
| `medium` | Tool failed but alternative path exists |
| `high` | Core operation failed, user task blocked |
| `critical` | Data integrity risk, security concern, system-wide impact |

## Status Lifecycle

```
new → in_review → need_dev → fixed
               → solution_found → fixed
               → failed_to_fix → need_dev (retry with new instructions)
               → on_hold (waiting for external fix)
new → declined (not a real issue / duplicate)
```

**Status definitions:**
- `new` — just registered
- `in_review` — being investigated
- `need_dev` — Lovable instructions ready, waiting for developer action
- `solution_found` — solution described but not yet implemented
- `fixed` — successfully implemented and verified, ticket closed
- `failed_to_fix` — fix attempt failed, new instructions needed
- `on_hold` — parked, waiting for external dependency
- `declined` — won't fix / duplicate

**Resolved logic:** Only `fixed` and `declined` count as closed. Everything else is open.

## Academy Learning Lifecycle

The Academy system tracks what the agent learns from resolved cases. Every case starts with `learning_status: pending_curation` (set automatically on registration).

**When to update Academy fields:**
- After setting `status: fixed` with a verified `solution` → set `learning_status: skill_adopted`, provide `learning_confidence` (0-100) and `learning_explainer`
- After setting `status: declined` (duplicate/not-a-bug) → set `learning_status: not_applicable`
- When a solution exists but needs more validation → set `learning_status: needs_review`

**Confidence guidelines:**
| Score | Meaning |
|-------|---------|
| 0-30 | Hypothesis only, untested |
| 31-60 | Tested once, may not generalize |
| 61-80 | Tested multiple times, likely reliable |
| 81-100 | Verified pattern, safe to auto-apply |

## Academy Triage Guidelines

When evaluating a case for Academy learning potential, use these decision criteria:

### Mark as `not_applicable`
- **User-specific config errors**: wrong API key, missing env var, typo in input
- **One-off issues**: unique data corruption, singular edge case unlikely to recur
- **Timeout/rate-limit retries**: transient failures resolved by simple retry
- **Duplicate cases**: already captured by an existing case with a solution
- **External dependency failures**: third-party outage, not actionable by agents

### Mark as `skill_adopted` (with confidence ≥80)
- **Recurring tool patterns**: "always call list_X before add_X to avoid duplicate key"
- **API quirks**: specific parameter ordering, undocumented field requirements
- **Error-recovery sequences**: validated multi-step recovery procedures
- **Delegation strategies**: proven routing patterns (e.g., "delegate canvas errors to canvas_manager first")
- **Data validation rules**: discovered constraints not documented in schemas

### Mark as `pending_curation` (needs more data)
- Solution exists but only tested once (confidence 30-60)
- Pattern seems reusable but hasn't been verified across workspaces
- Workaround found but root cause unknown

### Who triages?
1. **error_handler** (reactive): Evaluates learning potential immediately when diagnosing a new error
2. **Adman** (proactive): Sweeps resolved cases (`status: fixed`) still at `pending_curation` during idle capacity or when asked about Academy status

## Autonomous Academy Pipeline (CASE-047)

When a case is updated to `status: fixed` via `update_case`, the system automatically evaluates the learning:

### Auto-Adoption (confidence ≥ 80)
If `learning_confidence >= 80` AND `learning_explainer` is present AND `error_context.agent_code` identifies a valid agent:
1. The `learning_explainer` text is appended to the agent's `system_prompt` as a `## Learned Rule`
2. An idempotency marker (`<!-- ACADEMY CASE-NNN -->`) prevents duplicate appends
3. `learning_status` is set to `skill_adopted`
4. An `academy_history` log entry is created with action `skill_adopted`

### Flagged for Review (confidence < 80)
If confidence is below 80 or the explainer is missing:
1. `learning_status` is set to `needs_review`
2. An `academy_history` log entry is created with action `flagged_for_review`

### Academy History Table
All autonomous actions are logged in `academy_history`:
| Field | Type | Description |
|-------|------|-------------|
| `case_id` | UUID | Reference to the case |
| `case_number` | integer | CASE-NNN number |
| `agent_code` | text | Agent whose prompt was updated |
| `action` | text | `skill_adopted` or `flagged_for_review` |
| `details` | text | Human-readable summary |
| `learning_explainer` | text | The learning that was adopted |
| `learning_confidence` | integer | Confidence score at time of action |
