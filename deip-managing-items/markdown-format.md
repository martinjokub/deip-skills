# Items Markdown Import Format

Items are organized by category and subcategory in markdown exports.

## Structure Overview

```markdown
# [Workspace Name]

## SETTINGS
- **Base Currency**: USD
- **Amortize One-Time Years**: 3
...

## [Category Name]

### [Subcategory Name]

#### [Item Name]
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

#### [Next Item Name]
...
```

## Item Fields

Items are represented as markdown sections with bullet-point fields.

| Field | Format | Description |
|-------|--------|-------------|
| Row # | `- **Row #**: N` | 1-based position (for parent references) |
| Type | `- **Type**: service` | Item type |
| Include | `- **Include**: Yes/No` | Include in calculations |
| Is Paid | `- **Is Paid**: Yes/No` | Paid vs free item |
| Quantity | `- **Quantity**: 50` | Number of units/seats |
| Price | `- **Price**: 8 USD` | Price with currency |
| Billing Cycle | `- **Billing Cycle**: monthly` | Payment frequency |
| Discount | `- **Discount**: 10%` | Discount percentage (if > 0) |
| VAT | `- **VAT**: 20%` | VAT percentage (if > 0) |
| Vendor | `- **Vendor**: Name` | Vendor name |
| Vendor URL | `- **Vendor URL**: https://...` | Vendor website |
| Purchase Date | `- **Purchase Date**: 2024-01-15` | ISO date |
| Next Payment Date | `- **Next Payment Date**: 2024-02-15` | ISO date |
| Renewal Date | `- **Renewal Date**: 2025-01-15` | ISO date |
| Description | `- **Description**: Text` | Rich description |
| Notes | `- **Notes**: Text` | Private notes |
| Custom Icon Name | `- **Custom Icon Name**: IconName` | Icon library name |
| Custom Icon URL | `- **Custom Icon URL**: https://...` | Custom icon |
| Parent Index | `- **Parent Index**: 1` | Parent's Row # |

## Parent-Child Linking

Child items reference their parent using the `Parent Index` field, which corresponds to the parent's `Row #`:

```markdown
#### Slack
- **Row #**: 1
- **Type**: service
...

#### Slack Mobile
- **Row #**: 2
- **Type**: mobileapp
- **Parent Index**: 1
...
```

## Optional Fields

Fields are only included if they have a value:
- Discount and VAT only appear if > 0
- Dates only appear if set
- Description and Notes only appear if not empty
- Custom icon fields only appear if set
- Parent Index only appears for child items

## Item Types

| Type | Description |
|------|-------------|
| `software` | Desktop applications |
| `service` | SaaS platforms, cloud services |
| `hardware` | Physical devices |
| `asset` | Digital assets, licenses |
| `human` | Team members, contractors |
| `ai` | AI tools and services |
| `module` | Sub-component (requires parent) |
| `feature` | Feature add-on (requires parent) |
| `mobileapp` | Mobile app (requires parent) |

## Full Example

```markdown
# My Workspace

## SETTINGS
- **Base Currency**: USD
- **Amortize One-Time Years**: 3
- **Exchange Rates**: {"USD":1,"EUR":0.92}

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
- **Description**: Team messaging and collaboration platform

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
- **Type**: service
- **Include**: Yes
- **Is Paid**: Yes
- **Quantity**: 10
- **Price**: 15 USD
- **Billing Cycle**: monthly
- **Vendor**: Zoom Video Communications
```

## CSV Format Alternative

For tabular data, a CSV format is also supported with the same fields as columns:

```csv
# Row numbers are 1-based. Parent Index references the Row # of the parent item.
Row #,Category,Subcategory,Name,Type,Include,Is Paid,Quantity,Price,Currency,Billing Cycle,Discount %,VAT %,Vendor,Vendor URL,Purchase Date,Next Payment Date,Renewal Date,Description,Notes,Custom Icon Name,Custom Icon URL,Parent Index
1,Communication,Chat,Slack,service,Yes,Yes,50,8,USD,monthly,0,0,Slack Technologies,https://slack.com,,,,,,,
2,Communication,Chat,Slack AI,feature,Yes,Yes,50,10,USD,monthly,0,0,,,,,,,,,1
```
