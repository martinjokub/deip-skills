# Workspace Markdown Import Format

Complete workspace markdown structure for import/export.

## Structure Overview

```markdown
# [Workspace Name]

## SETTINGS
[Workspace configuration]

## COMPANY INFO
[Business context]

## [Category Name]

### [Subcategory Name]

#### [Item Name]
[Item fields]

## RELATIONS CANVASES
[Canvas definitions]
```

## Settings Section

```markdown
## SETTINGS

- **Base Currency**: USD
- **Amortize One-Time Years**: 3
- **Exchange Rates**: {"USD":1,"EUR":0.92}
- **Expected Tools**: 50
- **Expected Annual Cost**: 100000
- **Saved Views**: 3 saved view(s)
  - View 1: "All Items"
    - Filters:
      - Type is any of service, software
    - Sorts:
      - Name (asc)
    - Visible Fields: name, type, price, vendor
  - View 2: "Paid Only"
    ...
- **Default View**: All Items
- **Visible Fields**: name, type, price, currency, vendor
```

### Settings Fields

| Field | Description |
|-------|-------------|
| Base Currency | Workspace currency code |
| Amortize One-Time Years | Years to spread one-time costs |
| Exchange Rates | JSON object with rates |
| Expected Tools | Target tool count |
| Expected Annual Cost | Target annual spend |
| Saved Views | Count and details of saved views |
| Default View | Active view name |
| Visible Fields | Currently visible columns |

## Company Info Section

```markdown
## COMPANY INFO

- **Short Name**: Acme
- **Official Company Name**: Acme Corporation
- **Industry**: Technology
- **Niche**: B2B SaaS
- **Business Model**: Subscription
- **Revenue Model**: Monthly recurring
- **Customer Types**: Enterprise, Mid-market
- **Team Size**: 51-200
- **Company Stage**: Growth
- **Funding Stage**: Series B
- **Tool Philosophy**: Best-of-breed
- **Compliance**: SOC2, GDPR
```

## Categories and Items

Items are grouped under category headers (H2) and subcategory headers (H3):

```markdown
## Communication

### Chat

#### Slack
- **Row #**: 1
- **Type**: service
- **Include**: Yes
- **Is Paid**: Yes
- **Quantity**: 50
- **Price**: 8 USD
- **Billing Cycle**: monthly
- **Vendor**: Slack Technologies
- **Vendor URL**: https://slack.com
- **Description**: Team messaging platform

#### Slack AI
- **Row #**: 2
- **Type**: feature
- **Include**: Yes
- **Is Paid**: Yes
- **Quantity**: 50
- **Price**: 10 USD
- **Billing Cycle**: monthly
- **Parent Index**: 1

### Video

#### Zoom
- **Row #**: 3
...
```

See [markdown-format.md](../deip-managing-items/markdown-format.md) for complete item field reference.

## Relations Canvases Section

```markdown
## RELATIONS CANVASES

### Canvas: Integration Map
**Display Order:** 0

#### Nodes
| Row | Name | X | Y | Size | Icon Override |
|-----|------|---|---|------|---------------|
| 1 | Slack | 100 | 100 | 4 | - |
| 3 | Zoom | 300 | 100 | 4 | - |

#### Notes
| Index | X | Y | Width | Height | BgColor | FontColor | Align | VAlign | Content |
|-------|---|---|-------|--------|---------|-----------|-------|--------|---------|
| 0 | 50 | 50 | 350 | 200 | #E3F2FD | #333333 | left | top | ## Communication |

#### Media
| Index | Type | X | Y | Width | Height | URL | Title |
|-------|------|---|---|-------|--------|-----|-------|
| 0 | image | 400 | 300 | 200 | 150 | https://example.com/arch.png | Architecture |

#### Edges
| From | To | Type | Style | Color | Animated | Label | Description | FromAnchor | ToAnchor |
|------|----|------|-------|-------|----------|-------|-------------|------------|----------|
| Row 1 | Row 3 | integration | solid | #4F46E5 | No | Sync | - | right | left |

#### Groups
| Name | Color |
|------|-------|
| Core | #4F46E5 |

---
```

See [markdown-format.md](../deip-building-canvases/markdown-format.md) for complete canvas format reference.

## Full Example

```markdown
# Production Stack

## SETTINGS

- **Base Currency**: USD
- **Amortize One-Time Years**: 3
- **Exchange Rates**: {"USD":1,"EUR":0.92}
- **Expected Tools**: 30
- **Expected Annual Cost**: 50000

## COMPANY INFO

- **Short Name**: Acme
- **Industry**: Technology
- **Team Size**: 51-200

## Communication

### Chat

#### Slack
- **Row #**: 1
- **Type**: service
- **Include**: Yes
- **Is Paid**: Yes
- **Quantity**: 50
- **Price**: 8 USD
- **Billing Cycle**: monthly
- **Vendor**: Slack Technologies
- **Vendor URL**: https://slack.com

### Video

#### Zoom
- **Row #**: 2
- **Type**: service
- **Include**: Yes
- **Is Paid**: Yes
- **Quantity**: 10
- **Price**: 15 USD
- **Billing Cycle**: monthly

## RELATIONS CANVASES

### Canvas: Overview
**Display Order:** 0

#### Nodes
| Row | Name | X | Y | Size | Icon Override |
|-----|------|---|---|------|---------------|
| 1 | Slack | 100 | 100 | 4 | - |
| 2 | Zoom | 300 | 100 | 4 | - |

#### Edges
| From | To | Type | Style | Color | Animated | Label | Description | FromAnchor | ToAnchor |
|------|----|------|-------|-------|----------|-------|-------------|------------|----------|
| Row 1 | Row 2 | integration | dashed | #666666 | No | - | - | - | - |

---
```

## Import Notes

1. **Row numbers** are 1-based and sequential in document order
2. **Parent Index** references the parent's Row # for child items
3. **Canvas node rows** reference the item's Row # from the items section
4. **Category/subcategory IDs** are generated on import based on names
5. **UUIDs** are auto-generated for all entities on import
