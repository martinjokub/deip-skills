# Items JSON Import Format

Items are included in workspace JSON exports under the `items` array.

## Item Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | No | UUID (auto-generated on import) |
| `name` | string | Yes | Item name, max 200 chars |
| `type` | string | No | Item type (default: `software`) |
| `subcategoryId` | string | Yes | Links to subcategory UUID |
| `quantity` | number | No | 1-100000 (default 1) |
| `priceValue` | number | No | 0-1000000 (default 0) |
| `currency` | string | No | Currency code (default: workspace base) |
| `billingCycle` | string | No | Billing frequency |
| `discountPct` | number | No | 0-100 (default 0) |
| `vatPct` | number | No | 0-100 (default 0) |
| `vendor` | string | No | Vendor/provider name |
| `vendorUrl` | string | No | Valid HTTPS URL |
| `purchaseDate` | string | No | ISO date (YYYY-MM-DD) |
| `nextPaymentDate` | string | No | ISO date |
| `renewalDate` | string | No | ISO date |
| `description` | string | No | Rich text description |
| `notes` | string | No | Private notes |
| `include` | boolean | No | Include in calculations (default true) |
| `isPaid` | boolean | No | Is a paid item (default true) |
| `parentItemId` | string | No | UUID of parent item |
| `customIconName` | string | No | Icon library name |
| `customIconUrl` | string | No | Custom icon URL |

## Item Types

| Type | Requires Parent | Description |
|------|-----------------|-------------|
| `software` | No | Desktop applications |
| `service` | No | SaaS platforms, cloud services |
| `hardware` | No | Physical devices |
| `asset` | No | Digital assets, licenses |
| `human` | No | Team members, contractors |
| `ai` | No | AI tools and services |
| `module` | Yes | Sub-component of parent |
| `feature` | Yes | Feature add-on |
| `mobileapp` | Yes | Mobile app companion |

## Billing Cycles

| Value | Description |
|-------|-------------|
| `monthly` | Monthly payment |
| `quarterly` | Every 3 months |
| `annual` | Yearly payment |
| `weekly` | Weekly payment |
| `daily` | Daily payment |
| `one_time` | One-time purchase |

## Parent-Child Linking

Child types (`module`, `feature`, `mobileapp`) require `parentItemId`:

```json
{
  "name": "Slack Mobile",
  "type": "mobileapp",
  "parentItemId": "uuid-of-slack-item"
}
```

On import, parent items must be processed before children. Use the items array order to ensure parents appear before their children.

## Example

```json
{
  "items": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440001",
      "name": "Slack",
      "type": "service",
      "subcategoryId": "550e8400-e29b-41d4-a716-446655440010",
      "quantity": 50,
      "priceValue": 8,
      "currency": "USD",
      "billingCycle": "monthly",
      "vendor": "Slack Technologies",
      "vendorUrl": "https://slack.com",
      "include": true,
      "isPaid": true,
      "description": "Team messaging platform"
    },
    {
      "id": "550e8400-e29b-41d4-a716-446655440002",
      "name": "Slack AI",
      "type": "feature",
      "subcategoryId": "550e8400-e29b-41d4-a716-446655440010",
      "parentItemId": "550e8400-e29b-41d4-a716-446655440001",
      "quantity": 50,
      "priceValue": 10,
      "currency": "USD",
      "billingCycle": "monthly",
      "include": true,
      "isPaid": true
    }
  ]
}
```

## Categories and Subcategories

Items reference subcategories, which reference categories:

```json
{
  "categories": [
    { "id": "cat-uuid-1", "name": "Communication" }
  ],
  "subcategories": [
    { "id": "sub-uuid-1", "name": "Chat", "categoryId": "cat-uuid-1" }
  ],
  "items": [
    { "name": "Slack", "subcategoryId": "sub-uuid-1", ... }
  ]
}
```
