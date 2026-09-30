---
name: deip-building-canvases
description: Creates visual diagrams (canvases) showing relationships between ecosystem components. Handles nodes, notes, media, and edges. Use when visualizing tool integrations, dependencies, or data flows.
---

# Building DEIP Canvases

Canvases are visual diagrams for mapping relationships between tools in a digital ecosystem.

## Canvas Structure

```
Canvas
├── Nodes (items placed on canvas)
├── Notes (text boxes for grouping/context)
├── Media (images, YouTube videos)
└── Edges (connections between elements)
```

## Critical: Non-Overlap Rule

**Canvas elements must never visually overlap, except Notes used as grouping backgrounds.**

- Nodes must not overlap other nodes or media
- Media must not overlap other media or nodes
- Notes used as content cards must not overlap other elements
- **Only exception**: Notes with `z_index < 0` used as visual group backgrounds may sit behind nodes, media, and other notes

See [layout-patterns.md](./layout-patterns.md) for spacing guidelines and [nodes.md](./nodes.md) for minimum distances.

## Critical: Node Uniqueness Constraint

**Each workspace item can only appear ONCE per canvas.**

See [nodes.md](./nodes.md) for the mandatory check-before-add workflow.

## REST API Endpoints

Base URL: `https://api.deip.app`

### Canvas Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/canvases` | List canvases |
| `GET` | `/workspaces/{id}/canvases/{canvas_id}` | Get canvas with all elements |
| `POST` | `/workspaces/{id}/canvases` | Create canvas |
| `PUT` | `/workspaces/{id}/canvases/{canvas_id}` | Update canvas |
| `DELETE` | `/workspaces/{id}/canvases/{canvas_id}` | Delete canvas |

### Node Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/canvases/{canvas_id}/nodes` | List nodes |
| `POST` | `/workspaces/{id}/canvases/{canvas_id}/nodes` | Add node(s) |
| `PUT` | `/workspaces/{id}/canvases/{canvas_id}/nodes/{node_id}` | Update node |
| `DELETE` | `/workspaces/{id}/canvases/{canvas_id}/nodes/{node_id}` | Delete node |

### Note Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/workspaces/{id}/canvases/{canvas_id}/notes` | Create note |
| `PUT` | `/workspaces/{id}/canvases/{canvas_id}/notes/{note_id}` | Update note |
| `DELETE` | `/workspaces/{id}/canvases/{canvas_id}/notes/{note_id}` | Delete note |

### Media Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/workspaces/{id}/canvases/{canvas_id}/media` | Add media |
| `PUT` | `/workspaces/{id}/canvases/{canvas_id}/media/{media_id}` | Update media |
| `DELETE` | `/workspaces/{id}/canvases/{canvas_id}/media/{media_id}` | Delete media |

### Edge Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/workspaces/{id}/canvases/{canvas_id}/edges` | Create edge |
| `PUT` | `/workspaces/{id}/canvases/{canvas_id}/edges/{edge_id}` | Update edge |
| `DELETE` | `/workspaces/{id}/canvases/{canvas_id}/edges/{edge_id}` | Delete edge |

### Connection Type Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/workspaces/{id}/connection-types` | List connection types |
| `POST` | `/workspaces/{id}/connection-types` | Create custom type |
| `PUT` | `/workspaces/{id}/connection-types/{type_id}` | Update type styling |

### Example REST Call

```bash
# Get canvas with all elements
curl -H "X-API-Key: deip_xxx" \
  https://api.deip.app/workspaces/{id}/canvases/{canvas_id}

# Add a node to canvas
curl -X POST -H "X-API-Key: deip_xxx" -H "Content-Type: application/json" \
  -d '{"workspace_item_id":"item-uuid","x":100,"y":200}' \
  https://api.deip.app/workspaces/{id}/canvases/{canvas_id}/nodes
```

## MCP Tools (Alternative)

### Canvas Management
| Tool | Description |
|------|-------------|
| `list_canvases` | List all canvases in workspace |
| `get_canvas` | Get canvas with all elements |
| `create_canvas` | Create new canvas |
| `update_canvas` | Update canvas name/settings |
| `delete_canvas` | Delete canvas and all elements |

### Node Operations
| Tool | Description |
|------|-------------|
| `list_canvas_nodes` | List nodes with full item data |
| `add_canvas_node` | Add item(s) to canvas |
| `update_canvas_node` | Update node position/size |
| `delete_canvas_node` | Remove node from canvas |

### Note Operations
| Tool | Description |
|------|-------------|
| `add_canvas_note` | Create text box |
| `update_canvas_note` | Update note content/style |
| `delete_canvas_note` | Delete note |

### Media Operations
| Tool | Description |
|------|-------------|
| `add_canvas_media` | Add image or YouTube video |
| `update_canvas_media` | Update media properties |
| `delete_canvas_media` | Delete media |

### Edge Operations
| Tool | Description |
|------|-------------|
| `add_canvas_edge` | Create connection |
| `update_canvas_edge` | Update connection properties |
| `delete_canvas_edge` | Delete connection |

### Connection Types
| Tool | Description |
|------|-------------|
| `list_connection_types` | List built-in + custom types |
| `add_custom_connection_type` | Create custom type |
| `update_connection_type_style` | Modify type styling |

## Element Types

### Nodes
Items from the workspace placed visually. Include full item data with notes and descriptions.
**Semantic context**: Always read `_item.notes` and `_item.description` to understand what each item does and why it matters.

### Notes
Text boxes for:
- Grouping related nodes visually (background containers)
- Adding context and labels
- Creating diagram sections
- Standalone content cards (no nodes needed)
- Schema/data field cells connected by edges
**Semantic context**: Grouping notes (`z_index < 0`) provide context for all objects placed on top of them.

### Media
Images and YouTube videos with title and description fields for AI context.

### Edges
Connections between elements with relationship types:
- `parent_child`, `integration`, `dependency`, `alternative`, `data_flow`, `custom`
**Semantic context**: Combine `relationship_type` + `label` + `description` to understand the full meaning of each connection.

See [connection-types.md](./connection-types.md) for semantics and [edges.md](./edges.md) for recommended style conventions.

## Import File Formats

For file-based data transfer instead of MCP tools:
- [json-format.md](./json-format.md) - JSON import structure
- [markdown-format.md](./markdown-format.md) - Markdown import structure

## Related Documentation

- [nodes.md](./nodes.md) - Node placement and uniqueness constraint
- [notes.md](./notes.md) - Text boxes, grouping, content cards, schema cells
- [media.md](./media.md) - Images and videos
- [edges.md](./edges.md) - Connections, relationships, and style conventions
- [layout-patterns.md](./layout-patterns.md) - Visual organization and non-overlap rules
- [connection-types.md](./connection-types.md) - Relationship types
- [reading-canvas-semantics.md](./reading-canvas-semantics.md) - How to interpret canvas data semantically

## Item Enrichment

Before creating items for the canvas, check the **Global Item Catalog** for pre-existing metadata (vendor, description, icons, pricing). Use the `catalog_search` MCP tool or refer to the [deip-global-catalog](../deip-global-catalog/SKILL.md) skill for details.
