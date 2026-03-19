---
name: deip-managing-items
description: Creates and manages items (tools, services, people, assets) in DEIP workspaces. Supports bulk operations and parent-child relationships. Use when adding or modifying ecosystem components.
---

# Managing DEIP Items

Items are the core entities in DEIP representing tools, services, people, and assets in a digital ecosystem.

## REST API Endpoints

Base URL: `https://api.deip.app`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/items` | List items with optional filters |
| `GET` | `/workspaces/{id}/items/{item_id}` | Get item with full details |
| `POST` | `/workspaces/{id}/items` | Create item(s) — single or bulk |
| `PUT` | `/workspaces/{id}/items/{item_id}` | Update item properties |
| `DELETE` | `/workspaces/{id}/items/{item_id}` | Delete item and canvas placements |
| `GET` | `/workspaces/{id}/items/search?q=term` | Search items by name/vendor |

### Example REST Calls

```bash
# List all items
curl -H "X-API-Key: deip_xxx" https://api.deip.app/workspaces/{id}/items

# Create item
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"name":"Slack","type":"service","subcategoryId":"sub-uuid"}' \
  https://api.deip.app/workspaces/{id}/items

# Bulk create
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"items":[{"name":"Slack","type":"service","subcategoryId":"sub-uuid"},{"name":"Notion","type":"service","subcategoryId":"sub-uuid"}]}' \
  https://api.deip.app/workspaces/{id}/items

# Filter: software items over $100
curl -H "X-API-Key: deip_xxx" \
  "https://api.deip.app/workspaces/{id}/items?filter[type]=software&filter[priceValue][gt]=100"
```

## MCP Tools (Alternative)

Two tool variants exist for items — **direct-DB** (JSONB) and **API proxy** (REST). Each agent is configured with the correct variant. Use the tool names assigned to your agent.

### Direct-DB Tools (JSONB — used by Echo)

| Tool | Description |
|------|-------------|
| `list_items` | List all items with optional filters |
| `search_items` | Search items by name/vendor |
| `add_item` | Create item(s) using `subcategory_name` |
| `update_item` | Update item properties |
| `delete_item` | Delete item and canvas placements |

### API Proxy Tools (REST — used by specialists)

| Tool | Description |
|------|-------------|
| `list_items_api` | List all items with optional filters |
| `get_item_api` | Get single item with full details |
| `create_item` | Create item(s) using `subcategory_id` |
| `update_item_api` | Update item properties |
| `delete_item_api` | Delete item and canvas placements |

> **Note:** There is no runtime aliasing. Each tool name maps to a specific implementation. Use only the tools assigned to your agent.

## Item Types

| Type | Description | Requires Parent |
|------|-------------|-----------------|
| `software` | Desktop applications, installed software | No |
| `service` | SaaS platforms, cloud services | No |
| `hardware` | Physical devices, equipment | No |
| `asset` | Digital assets, licenses | No |
| `human` | Team members, contractors | No |
| `ai` | AI tools and services | No |
| `module` | Sub-component of parent item | Yes |
| `feature` | Feature add-on of parent item | Yes |
| `mobileapp` | Mobile app linked to parent | Yes |

See [item-types.md](./item-types.md) for detailed descriptions.

## Duplicate Prevention (MANDATORY)

Before creating any item, you **MUST** check if an item with the same or very similar name already exists in the workspace by calling `search_items` or `list_items`. If a match is found:
1. **Inform the user** about the existing item (name, type, vendor).
2. **Ask** whether to update the existing item, skip it, or create a duplicate.
3. **NEVER silently create a duplicate.**

The runtime also enforces this: `add_item` will return a `DUPLICATE DETECTED` error and `bulk_add_items` will skip duplicates automatically, returning them in `skipped_duplicates`.

## Bulk Creation (Preferred)

Create multiple items in one request:
```json
{
  "workspace_id": "uuid",
  "items": [
    { "name": "Slack", "type": "service", "subcategoryId": "sub-123" },
    { "name": "Notion", "type": "service", "subcategoryId": "sub-123" },
    { "name": "Figma", "type": "service", "subcategoryId": "sub-456" }
  ]
}
```

## Filter Operators

| Operator | Example | Description |
|----------|---------|-------------|
| `eq` | `{ "type": { "eq": "software" } }` | Exact match |
| `neq` | `{ "type": { "neq": "hardware" } }` | Not equal |
| `contains` | `{ "name": { "contains": "slack" } }` | Text search |
| `gt`, `gte` | `{ "priceValue": { "gt": 100 } }` | Greater than |
| `lt`, `lte` | `{ "priceValue": { "lt": 50 } }` | Less than |
| `in` | `{ "type": { "in": ["software", "service"] } }` | In array |
| `is_empty` | `{ "vendor": { "is_empty": true } }` | Field empty |

## Key Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Item name (required) |
| `vendor` | string | Vendor/provider name |
| `type` | string | Item type (see above) |
| `subcategoryId` | string | Parent subcategory UUID |
| `priceValue` | number | Price amount |
| `currency` | string | Currency code |
| `billingCycle` | string | Billing frequency |
| `quantity` | number | Number of units/seats |
| `isPaid` | boolean | Paid vs free |
| `include` | boolean | Include in calculations |
| `parentItemId` | string | Parent item (for module/feature/mobileapp) |

See [financial-fields.md](./financial-fields.md) for pricing details.

## Import File Formats

For file-based data transfer instead of MCP tools:
- [json-format.md](./json-format.md) - JSON import structure
- [markdown-format.md](./markdown-format.md) - Markdown import structure

## Parent-Child Relationships

Child types (`module`, `feature`, `mobileapp`) require `parentItemId`:
```json
{
  "name": "Slack Mobile",
  "type": "mobileapp",
  "parentItemId": "slack-item-uuid"
}
```

See [parent-child.md](./parent-child.md) for relationship patterns.

## Response Format

Items include context for display:
```json
{
  "id": "uuid",
  "name": "Slack",
  "type": "service",
  "priceValue": 10,
  "currency": "USD",
  "billingCycle": "monthly",
  "_context": {
    "categoryName": "Communication",
    "subcategoryName": "Chat",
    "parentItemName": null
  }
}
```
