# Canvas Nodes

Nodes represent workspace items placed on a canvas for visualization.

## Critical: Uniqueness Constraint

**Each workspace item can only appear ONCE per canvas.**

Database enforces unique constraint on `(canvas_id, workspace_item_id)`.

### Mandatory Workflow

1. Call `list_canvas_nodes` to get existing items
2. Compare intended `item_id`s with `workspace_item_id` values
3. Split operations:
   - **New items** → `add_canvas_node`
   - **Existing items** → `update_canvas_node`

### Example Safe Workflow

```text
// 1. Check existing nodes
Agent: list_canvas_nodes { workspace_id: "...", canvas_id: "..." }
Result: { nodes: [
  { workspace_item_id: "item-A", id: "node-1" },
  { workspace_item_id: "item-B", id: "node-2" }
]}

// 2. User wants to add items A, C, D
Existing: item-A (already on canvas - use node-1)
New: item-C, item-D (safe to add)

// 3. Add only new items
Agent: add_canvas_node {
  workspace_id: "...",
  canvas_id: "...",
  nodes: [
    { item_id: "item-C", x: 100, y: 200 },
    { item_id: "item-D", x: 300, y: 200 }
  ]
}

// 4. Reposition existing if needed
Agent: update_canvas_node {
  workspace_id: "...",
  canvas_id: "...",
  node_id: "node-1",
  x: 500,
  y: 200
}
```

## Critical: Non-Overlap Positioning

**Nodes must never visually overlap other nodes or media.**

### Minimum Spacing (Center-to-Center)

| Size Level | Pixel Size | Min Spacing |
|------------|------------|-------------|
| 1 | 44px | 120px |
| 3 | 72px | 140px |
| 4 | ~94px | 160px |
| 5 | 116px | 180px |
| 7 | 176px | 240px |

### Inside Grouping Notes

When placing nodes inside a background note:
- Maintain **20-40px padding** from note edges
- Maintain standard spacing between nodes
- Example: a 520×240px note can fit 3 nodes (size_level 3) in a row with 140px spacing

### Overlap Check

Before placing a new node at `(x, y)`, verify:
1. No existing node center is within the minimum spacing distance
2. No media element bounding box intersects with the node area
3. The node fits within its parent grouping note (if applicable)

## Size Levels

| Level | Dimensions | Use Case |
|-------|------------|----------|
| 1 | 44px | Compact/tiny nodes |
| 3 | 72px | Default size |
| 5 | 116px | Important items |
| 7 | 176px | Hero/focal items |

## Positioning

- Canvas origin (0,0) is top-left
- Typical spacing: 140-200px between nodes
- Group related items close together (inside a grouping note)
- Use notes as visual backgrounds for groups

## Adding Nodes

### Single Node
```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid",
  "item_id": "item-uuid",
  "x": 100,
  "y": 200,
  "size_level": 3
}
```

### Bulk Add (Preferred)
```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid",
  "nodes": [
    { "item_id": "uuid-1", "x": 100, "y": 100 },
    { "item_id": "uuid-2", "x": 250, "y": 100 },
    { "item_id": "uuid-3", "x": 175, "y": 250 }
  ]
}
```

## Node Data

Nodes include full item context:
```json
{
  "id": "node-uuid",
  "workspace_item_id": "item-uuid",
  "x": 100,
  "y": 200,
  "size_level": 3,
  "_item": {
    "name": "Slack",
    "type": "service",
    "vendor": "Salesforce",
    "notes": "Primary team communication",
    "description": "Business messaging platform"
  }
}
```

## Parent-Child Auto-Connections

When items with parent-child relationships are both on canvas:
- System automatically creates `parent_child` edge
- Edge marked as `is_system: true`
- Visual link shows hierarchy
