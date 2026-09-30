---
name: deip-managing-workspaces
description: Creates and manages DEIP workspaces including categories, subcategories, settings, and currencies. Use when setting up new ecosystems or modifying workspace structure.
---

# Managing DEIP Workspaces

Workspaces are containers for digital ecosystems. Each workspace contains categories, subcategories, items, and canvases.

## REST API Endpoints

Base URL: `https://api.deip.app`

### Workspace Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces` | List all accessible workspaces |
| `GET` | `/workspaces/{id}` | Get workspace with full details |
| `POST` | `/workspaces` | Create new workspace |
| `PUT` | `/workspaces/{id}` | Update workspace name or settings |
| `DELETE` | `/workspaces/{id}` | Delete workspace and ALL contents |
| `GET` | `/workspaces/{id}/summary` | Get workspace statistics |
| `GET` | `/workspaces/{id}/settings` | Get workspace configuration |
| `PUT` | `/workspaces/{id}/settings` | Update workspace settings |
| `GET` | `/workspaces/{id}/currencies` | List currencies with FX rates |
| `POST` | `/workspaces/{id}/currencies` | Add currency with exchange rate |
| `DELETE` | `/workspaces/{id}/currencies/{code}` | Remove currency |
| `GET` | `/workspaces/{id}/context` | Get company/business context |
| `PUT` | `/workspaces/{id}/context` | Update company information |
| `GET` | `/workspaces/{id}/knowledge-base` | Get AI knowledge base |
| `PUT` | `/workspaces/{id}/knowledge-base` | Update knowledge base |

### Category Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/categories` | List categories |
| `GET` | `/workspaces/{id}/categories/{cat_id}` | Get category with subcategories |
| `POST` | `/workspaces/{id}/categories` | Create category |
| `PUT` | `/workspaces/{id}/categories/{cat_id}` | Update category |
| `DELETE` | `/workspaces/{id}/categories/{cat_id}` | Delete category (destructive) |

### Subcategory Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/subcategories` | List subcategories |
| `GET` | `/workspaces/{id}/subcategories/{sub_id}` | Get subcategory with items |
| `POST` | `/workspaces/{id}/subcategories` | Create subcategory |
| `PUT` | `/workspaces/{id}/subcategories/{sub_id}` | Update subcategory |
| `DELETE` | `/workspaces/{id}/subcategories/{sub_id}` | Delete subcategory (destructive) |

### Example REST Calls

```bash
# List workspaces
curl -H "X-API-Key: deip_xxx" https://api.deip.app/workspaces

# Create workspace
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"name": "Production Environment"}' \
  https://api.deip.app/workspaces

# Create category
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"name": "Marketing Tools"}' \
  https://api.deip.app/workspaces/{id}/categories
```

## MCP Tools (Alternative)

### Workspace Operations

| Tool | Description |
|------|-------------|
| `list_workspaces` | List all accessible workspaces with optional filters |
| `get_workspace` | Get workspace with full details (settings, categories, items) |
| `create_workspace` | Create new workspace with default categories |
| `update_workspace` | Update workspace name or settings |
| `delete_workspace` | Delete workspace and ALL contents (destructive) |
| `resolve_workspace` | Find workspace ID by name (partial match) |

### Category Operations

| Tool | Description |
|------|-------------|
| `list_categories` | List top-level categories in workspace |
| `get_category` | Get category with its subcategories |
| `create_category` | Create new category |
| `update_category` | Rename category |
| `delete_category` | Delete category and ALL nested content (destructive) |

### Subcategory Operations

| Tool | Description |
|------|-------------|
| `list_subcategories` | List subcategories (optionally filter by category) |
| `get_subcategory` | Get subcategory with its items |
| `create_subcategory` | Create subcategory under a category |
| `update_subcategory` | Rename or move subcategory |
| `delete_subcategory` | Delete subcategory and ALL items (destructive) |

### Settings & Currency Operations

| Tool | Description |
|------|-------------|
| `get_workspace_settings` | Get workspace configuration |
| `update_workspace_settings` | Update workspace settings |
| `list_workspace_currencies` | List configured currencies with FX rates |
| `add_workspace_currency` | Add currency with exchange rate |
| `remove_workspace_currency` | Remove currency from workspace |

## Workspace Settings

Key settings properties:
- `baseCurrency`: Base currency code (EUR, USD, GBP, etc.)
- `fx`: Exchange rates object (e.g., `{ EUR: 1, USD: 1.1, GBP: 0.85 }`)
- `expectedTools`: Expected number of tools (for comparison)
- `expectedAnnualCost`: Expected annual cost (for comparison)
- `amortizeOneTimeYears`: Years to amortize one-time costs (default: 3)

## Filter Operators

Filters support these operators:
- Exact match: `{ "name": "Production" }` or `{ "name": { "eq": "Production" } }`
- Not equal: `{ "name": { "neq": "Test" } }`
- Contains: `{ "name": { "contains": "prod" } }`
- Is empty: `{ "name": { "is_empty": true } }`

## Example Workflows

### Create New Workspace
```json
{ "name": "Production Environment" }
// Returns workspace with default categories
```

### Resolve Workspace by Name
```json
{ "name": "production" }
// Returns: { matches: [{ id: "uuid", name: "Production Environment" }] }
```

### Add Category and Subcategory
```json
// 1. Create category
{ "workspace_id": "uuid", "name": "Marketing Tools" }

// 2. Create subcategory
{ "workspace_id": "uuid", "category_id": "cat-uuid", "name": "Email Marketing" }
```

## Import File Formats

For file-based data transfer instead of MCP tools:
- [json-format.md](./json-format.md) - JSON import structure
- [markdown-format.md](./markdown-format.md) - Markdown import structure

## Related Documentation

- [Structure Patterns](./structure-patterns.md) - Organization best practices
- [Financial Setup](./financial-setup.md) - Currency and FX configuration
