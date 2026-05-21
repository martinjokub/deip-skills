# DEIP Error Handling & Recovery

## Mandatory Recovery Pattern

When receiving an MCP error, **DO NOT** immediately show it to the user. Instead:

1. Identify the error type from the message
2. Attempt automatic recovery (see patterns below)
3. Only show errors to users if recovery fails after 2 attempts

## Common Errors

| Error Message | Cause | Resolution |
|---------------|-------|------------|
| "Workspace not found or access denied" | Invalid workspace_id or no access | Call `list_workspaces`, find matching workspace, retry with correct UUID |
| "Item not found with id '...'" | Invalid item_id in workspace | Use your agent's item listing tool (`list_items` or `list_items_api`) to see available items and their IDs |
| "Invalid workspace_id format" | Sent workspace NAME instead of UUID | Use `resolve_workspace` to get UUID, then retry |
| "Rate limit exceeded" | Too many requests | Wait 60 seconds and retry |
| "Insufficient permissions" | API key lacks write access | Inform user; cannot proceed with write operations |

## Canvas-Specific Errors

| Error Message | Cause | Resolution |
|---------------|-------|------------|
| "duplicate key value violates unique constraint" (code 23505) | Item already on canvas | Call `list_canvas_nodes` first, use `update_canvas_node` for existing items |
| "Item(s) already exist on this canvas" | Same as above | Check existing nodes, filter out duplicates, update position if needed |

## Recovery Flow Example

```text
User: "Show my marketing tools"

Agent thinking: I need the workspace ID...
Agent action: list_workspaces
Result: [{ name: "Acme Corp", id: "abc-123-..." }]

Agent action: list_items_api with workspace_id: "abc-123-..."
Result: Success!
```

## Workspace ID Best Practices

- **Always use UUIDs**, never workspace names for `workspace_id`
- **Verify from memory**: Confirm workspace ID is valid UUID format before each call
- **Refresh stale data**: If workspace list not fetched this session, call `list_workspaces` first
- **Use resolve_workspace**: When you have a name but need an ID

## Never Do These

- ❌ Show raw MCP error codes (-32603, etc.) to users
- ❌ Use workspace NAME where workspace_id (UUID) is required
- ❌ Give up after first error without attempting recovery
- ❌ Assume a cached workspace ID is still valid without checking
