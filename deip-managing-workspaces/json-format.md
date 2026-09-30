# Workspace JSON Import Format

Complete workspace JSON structure for import/export.

## Top-Level Structure

```json
{
  "name": "Workspace Name",
  "categories": [...],
  "subcategories": [...],
  "items": [...],
  "settings": {...},
  "canvases": [...]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Workspace name |
| `categories` | array | Yes | Category definitions |
| `subcategories` | array | Yes | Subcategory definitions |
| `items` | array | Yes | All workspace items |
| `settings` | object | Yes | Workspace configuration |
| `canvases` | array | No | Canvas definitions |

## Categories Array

```json
{
  "categories": [
    { "id": "uuid-1", "name": "Communication" },
    { "id": "uuid-2", "name": "Development" }
  ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | UUID identifier |
| `name` | string | Yes | Category name |

## Subcategories Array

```json
{
  "subcategories": [
    { "id": "sub-uuid-1", "name": "Chat", "categoryId": "uuid-1" },
    { "id": "sub-uuid-2", "name": "Video", "categoryId": "uuid-1" }
  ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | UUID identifier |
| `name` | string | Yes | Subcategory name |
| `categoryId` | string | Yes | Parent category UUID |

## Settings Object

```json
{
  "settings": {
    "baseCurrency": "USD",
    "amortizeOneTimeYears": 3,
    "fx": { "USD": 1, "EUR": 0.92, "GBP": 0.79 },
    "expectedTools": 50,
    "expectedAnnualCost": 100000,
    "savedFilters": [...],
    "activeFilterId": "filter-uuid",
    "itemsViewFields": [...],
    "itemsViewColumnWidths": {...},
    "itemsViewGroups": [...],
    "itemsViewSorts": [...],
    "advancedFilters": [...],
    "context": {...},
    "documentView": {...},
    "autosave": {...},
    "workspaceTabSettings": {...}
  }
}
```

### Core Settings

| Field | Type | Description |
|-------|------|-------------|
| `baseCurrency` | string | Base currency code (USD, EUR, GBP, PLN) |
| `amortizeOneTimeYears` | number | Years to spread one-time costs (default 3) |
| `fx` | object | Exchange rates relative to base currency |
| `expectedTools` | number | Expected number of tools (for comparison) |
| `expectedAnnualCost` | number | Expected annual cost (for comparison) |

### View Configuration

| Field | Type | Description |
|-------|------|-------------|
| `savedFilters` | array | Saved view configurations |
| `activeFilterId` | string | Currently active saved view |
| `itemsViewFields` | array | Column visibility and order |
| `itemsViewColumnWidths` | object | Custom column widths |
| `itemsViewGroups` | array | Active grouping configuration |
| `itemsViewSorts` | array | Active sorting configuration |
| `advancedFilters` | array | Active filter rules |

### Context Object

Company/business context for AI understanding:

```json
{
  "context": {
    "shortName": "Acme",
    "officialCompanyName": "Acme Corporation",
    "industry": "Technology",
    "niche": "B2B SaaS",
    "businessModel": "Subscription",
    "revenueModel": "Monthly recurring",
    "customerTypes": ["Enterprise", "Mid-market"],
    "teamSize": "51-200",
    "companyStage": "Growth",
    "fundingStage": "Series B",
    "toolPhilosophy": "Best-of-breed",
    "complianceRequirements": ["SOC2", "GDPR"]
  }
}
```

### Document View Settings

```json
{
  "documentView": {
    "layout": "side-by-side",
    "leftPaneWidth": 50,
    "showOutline": true
  }
}
```

### Autosave Settings

```json
{
  "autosave": {
    "enabled": true,
    "intervalMs": 30000
  }
}
```

### Workspace Tab Settings

```json
{
  "workspaceTabSettings": {
    "tabOrder": ["items", "calendar", "canvas", "document"],
    "hiddenTabs": []
  }
}
```

## Items Array

See [json-format.md](../deip-managing-items/json-format.md) for complete item schema.

```json
{
  "items": [
    {
      "id": "item-uuid",
      "name": "Slack",
      "type": "service",
      "subcategoryId": "sub-uuid-1",
      "quantity": 50,
      "priceValue": 8,
      "currency": "USD",
      "billingCycle": "monthly"
    }
  ]
}
```

## Canvases Array

See [json-format.md](../deip-building-canvases/json-format.md) for complete canvas schema.

```json
{
  "canvases": [
    {
      "name": "Integration Map",
      "display_order": 0,
      "nodes": [...],
      "edges": [...],
      "notes": [...],
      "media": [...],
      "groups": [...]
    }
  ]
}
```

## Full Example

```json
{
  "name": "Production Stack",
  "categories": [
    { "id": "cat-1", "name": "Communication" }
  ],
  "subcategories": [
    { "id": "sub-1", "name": "Chat", "categoryId": "cat-1" }
  ],
  "items": [
    {
      "id": "item-1",
      "name": "Slack",
      "type": "service",
      "subcategoryId": "sub-1",
      "quantity": 50,
      "priceValue": 8,
      "currency": "USD",
      "billingCycle": "monthly",
      "include": true,
      "isPaid": true
    }
  ],
  "settings": {
    "baseCurrency": "USD",
    "amortizeOneTimeYears": 3,
    "fx": { "USD": 1 },
    "expectedTools": 30,
    "expectedAnnualCost": 50000
  },
  "canvases": [
    {
      "name": "Overview",
      "display_order": 0,
      "nodes": [
        {
          "item_row_number": 1,
          "item_name": "Slack",
          "x": 100,
          "y": 100,
          "size_level": 4
        }
      ],
      "edges": [],
      "notes": [],
      "groups": []
    }
  ]
}
```
