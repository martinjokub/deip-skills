---
name: deip-analytics-context
description: Retrieves workspace summaries, company context, and manages the AI knowledge base. Use when analyzing ecosystem costs, understanding business context, or storing learnings.
---

# DEIP Analytics & Context

Tools for understanding workspace data and managing persistent AI knowledge.

## REST API Endpoints

Base URL: `https://api.deip.app`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/summary` | Get aggregated statistics |
| `GET` | `/workspaces/{id}/context` | Get company/business context |
| `PUT` | `/workspaces/{id}/context` | Update company information |
| `GET` | `/workspaces/{id}/knowledge-base` | Get AI knowledge base |
| `PUT` | `/workspaces/{id}/knowledge-base` | Update knowledge base |

### Example REST Calls

```bash
# Get workspace summary
curl -H "X-API-Key: deip_xxx" https://api.deip.app/workspaces/{id}/summary

# Update knowledge base (append mode)
curl -X PUT -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"content":"### [INTEGRATION] Slack + Jira\n**Date:** 2026-03-05\nSlack notifications trigger from Jira tickets.","mode":"append"}' \
  https://api.deip.app/workspaces/{id}/knowledge-base
```

## MCP Tools (Alternative)

| Tool | Description |
|------|-------------|
| `get_workspace_summary` / `get_workspace_summary_api` | Get aggregated statistics (use whichever is assigned to your agent) |
| `get_workspace_context` | Get company/business context |
| `update_workspace_context` | Update company information |
| `get_knowledge_base` | Retrieve accumulated AI knowledge |
| `update_knowledge_base` | Add new learnings to knowledge base |

## Workspace Summary

Use your workspace summary tool (`get_workspace_summary` or `get_workspace_summary_api`) for statistics—don't calculate manually:

```json
{
  "workspace_id": "uuid"
}
```

Returns:
- Total items count
- Items by type breakdown
- Total annual cost
- Cost by category
- Payment timeline
- Currency distribution

## Company Context

Business context stored in workspace settings:

| Field | Description |
|-------|-------------|
| `officialCompanyName` | Legal company name |
| `shortName` | Short/brand name |
| `industry` | Industry sector |
| `teamSize` | Number of employees |
| `headquarters` | Location |
| `website` | Company website |

Access via `get_workspace_context`, update via `update_workspace_context`.

## Knowledge Base

Persistent storage for AI-accumulated learnings. Survives across conversations.

### Key Principle

**Always call `get_knowledge_base` first** when starting a conversation to understand accumulated context.

### When to Update

✅ **DO UPDATE when:**
- User shares tool relationship info
- You discover integration patterns
- User corrects assumptions
- Business rules mentioned
- Contract/renewal information shared
- Cost anomalies identified
- User confirms or explains a canvas edge/connection meaning
- You learn why two tools are connected (relationship semantics)

❌ **DON'T UPDATE when:**
- Information is transient
- Data belongs in item fields
- Information is speculative (ask the user first!)
- Conversation is off-topic

### Structured Format

Always use structured markdown:

```markdown
### [CATEGORY] Title

**Date:** YYYY-MM-DD
**Related Items:** Tool names if applicable

Description of the learning.

- Bullet points for details
```

### Categories

| Prefix | When to Use |
|--------|-------------|
| `[INTEGRATION]` | Tool connections, data flows |
| `[RULE]` | Business rules affecting decisions |
| `[PREFERENCE]` | User/team preferences |
| `[EXCEPTION]` | Special cases, exclusions |
| `[INSIGHT]` | Patterns, observations |
| `[RENEWAL]` | Contract terms, pricing history |
| `[CONTACT]` | Vendor contacts, support info |
| `[RELATIONSHIP]` | Connection meanings, dependency context, confirmed relationship semantics |

See [knowledge-base.md](./knowledge-base.md) for detailed format guide.

## Mode Parameter

For `update_knowledge_base`:
- `mode: "append"` (default) - Adds to existing with separator
- `mode: "overwrite"` - Replaces ALL content (use only when requested)
