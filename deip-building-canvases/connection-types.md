# Connection Types

Relationship semantics for canvas edges.

## Built-in Types

| Type | Style | Color | Use When |
|------|-------|-------|----------|
| `parent_child` | Dashed | Blue #3B82F6 | Parent item to modules/features |
| `integration` | Solid | Green #10B981 | Tools that sync/share data |
| `dependency` | Solid | Orange #F59E0B | Tool A requires Tool B |
| `alternative` | Dotted | Purple #8B5CF6 | Interchangeable tools |
| `data_flow` | Solid+Animated | Cyan #06B6D4 | Data flows from A to B |
| `custom` | Solid | Grey #666666 | General purpose |

## Type Details

### parent_child
For hierarchical relationships:
- Software → Module
- Service → Feature
- Service → Mobile App

Direction: Parent → Child
Auto-created when items with parent-child relationship are both on canvas.

### integration
For tools that connect or sync:
- "Zapier integrates Slack with HubSpot"
- "API connection between CRM and Marketing"

Direction: Less important (often bidirectional conceptually)

### dependency
For required relationships:
- "App depends on AWS"
- "Website requires CDN"

Direction: Dependent → Dependency

### alternative
For interchangeable tools:
- "Notion as alternative to Confluence"
- "Slack vs Teams"

Direction: Either direction (symmetric)

### data_flow
For data movement:
- "CRM sends leads to Analytics"
- "ETL pipeline from source to warehouse"

Direction: Source → Destination
Often animated to show flow.

### custom
For any other relationship. Can be styled and labeled freely.

## Creating Custom Types

```json
{
  "workspace_id": "uuid",
  "name": "billing_flow",
  "color": "#4CAF50",
  "line_style": "solid",
  "is_animated": true
}
```

Custom type ideas:
- `auth_flow` - Authentication connections
- `backup_chain` - Backup relationships
- `notification` - Alert/notification paths
- `ownership` - Who owns/manages what
- `cost_driver` - Cost dependencies

## Styling Properties

| Property | Options | Default |
|----------|---------|---------|
| `color` | Hex color | Type default |
| `line_style` | `solid`, `dashed`, `dotted` | Type default |
| `line_width` | Number (pixels) | 2 |
| `is_animated` | boolean | false |

## List Connection Types

Use `list_connection_types` to see all built-in and custom types with their styles:

```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid"
}
```

Returns:
```json
{
  "builtin_types": [...],
  "custom_types": [...],
  "combined": [...]
}
```

## Best Practices

1. **Use semantic types** for clarity
2. **Be consistent** with direction conventions
3. **Add labels** for complex relationships
4. **Use descriptions** for AI context
5. **Create custom types** for domain-specific patterns
