# Canvas Edges

Edges are connections between canvas elements showing relationships.

## Connection Points

Edges can connect:
- Node ↔ Node
- Node ↔ Note
- Node ↔ Media
- Note ↔ Note
- Note ↔ Media
- Media ↔ Media

## Creating Edges

```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid",
  "from_node_id": "node-uuid-1",
  "to_node_id": "node-uuid-2",
  "relationship_type": "integration",
  "label": "Syncs contacts",
  "description": "Bidirectional contact sync via API every 6 hours"
}
```

## Endpoint Fields

| From | To |
|------|-----|
| `from_node_id` | `to_node_id` |
| `from_note_id` | `to_note_id` |
| `from_media_id` | `to_media_id` |

Use one from-field and one to-field per edge.

## Relationship Types

| Type | Use When | Direction Matters |
|------|----------|-------------------|
| `parent_child` | Parent to modules/features | Yes |
| `integration` | Tools sync/share data | Less important |
| `dependency` | Tool A requires Tool B | Yes |
| `alternative` | Interchangeable tools | No |
| `data_flow` | Data moves A to B | Yes |
| `custom` | Other relationships | Varies |

See [connection-types.md](./connection-types.md) for details.

## Recommended Style Conventions

Based on observed patterns across real canvases:

| Relationship Type | Line Style | Animated | Use Case |
|-------------------|-----------|----------|----------|
| `dependency` | `dotted` | yes | Active dependencies, required tools |
| `dependency` | `dotted` | no | Static/optional dependencies |
| `data_flow` | `solid` | yes | Primary data flows |
| `data_flow` | `dashed` | yes | Secondary/indirect data flows |
| `parent_child` | `dashed` | yes or no | Hierarchy (auto-created for item relationships) |
| `integration` | `solid` | no | Static tool connections |
| `alternative` | `dotted` | no | Interchangeable options |

### Note-to-Note Edges (Schema Patterns)

When connecting notes used as schema cells:
- Use `data_flow` or `dependency` type
- `solid` line style, not animated
- Represents foreign key or data relationships
- Connect from the referencing cell to the referenced cell

## Direction Semantics

For directional relationships:
- **dependency**: `from` = dependent, `to` = dependency
- **data_flow**: `from` = source, `to` = destination
- **parent_child**: `from` = parent, `to` = child

## Labels and Descriptions

The combination of `relationship_type` + `label` + `description` creates the full semantic meaning of a connection.

| Field | Purpose |
|-------|---------|
| `relationship_type` | Category of relationship (e.g., `integration`, `dependency`) |
| `label` | Short text displayed on edge — the visible semantic summary |
| `description` | Detailed context about the relationship mechanics (not displayed) |

### Interpreting Edge Meaning

Read all three fields together to understand WHY two objects are connected:

| Type | Label | Description | Interpretation |
|------|-------|-------------|----------------|
| `integration` | "syncs contacts" | "Bidirectional contact sync via API every 6 hours" | These tools share data automatically |
| `dependency` | "requires auth" | "App cannot function without SSO provider" | Source structurally depends on target |
| `data_flow` | "sends leads" | "Marketing-qualified leads pushed to CRM hourly" | Data moves from source to destination |

### Best Practice

Always populate all three fields when creating edges to ensure AI agents can fully understand every connection.

## Styling Properties

| Property | Description |
|----------|-------------|
| `line_color` | Hex color (overrides type default) |
| `line_width` | Stroke width in pixels |
| `line_style` | `solid`, `dashed`, `dotted` |
| `is_animated` | Enable flow animation |
| `animation_shape` | Animation style |

## Label Positioning

| Property | Description |
|----------|-------------|
| `label_offset_x` | Horizontal offset from center |
| `label_offset_y` | Vertical offset from center |

## Anchor Points

| Property | Values |
|----------|--------|
| `from_anchor` | `top`, `bottom`, `left`, `right`, `auto` |
| `to_anchor` | `top`, `bottom`, `left`, `right`, `auto` |

Default is `auto` (system determines best anchor).

## System Edges

Edges with `is_system: true` are auto-generated for:
- Parent-child item relationships
- Cannot be deleted while relationship exists
