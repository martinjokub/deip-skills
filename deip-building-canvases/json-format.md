# Canvas JSON Import Format

Canvases are included in workspace JSON exports under the `canvases` array.

## Canvas Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Canvas name |
| `display_order` | number | No | Sort order (0-based), default 0 |
| `settings` | object | No | Canvas configuration (zoom, pan, grid) |
| `nodes` | array | No | Items placed on canvas |
| `edges` | array | No | Connections between elements |
| `notes` | array | No | Text boxes |
| `media` | array | No | Images and YouTube videos |
| `groups` | array | No | Edge filter groups |

## Node Object

Each node places a workspace item on the canvas.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `item_row_number` | number | Yes | 1-based row from items list |
| `item_name` | string | Yes | Item name (fallback if row doesn't match) |
| `x` | number | Yes | X position in pixels |
| `y` | number | Yes | Y position in pixels |
| `size_level` | number | No | 1-7, default 3 |
| `icon_override_type` | string | No | `"library"` or `"upload"` |
| `icon_override_value` | string | No | Icon name or URL |

### Size Level Reference

| Level | Dimensions | Use Case |
|-------|------------|----------|
| 1 | 44px | Compact overview |
| 2 | 56px | Dense layout |
| 3 | 72px | Default |
| 4 | 92px | Emphasis |
| 5 | 116px | Important items |
| 6 | 144px | Major items |
| 7 | 176px | Hero items |

## Edge Object

Edges connect nodes, notes, or media elements.

| Field | Type | Description |
|-------|------|-------------|
| `from_item_row` | number | Source node (1-based row number) |
| `from_item_name` | string | Source item name (for reference) |
| `to_item_row` | number | Target node (1-based row number) |
| `to_item_name` | string | Target item name (for reference) |
| `from_note_index` | number | Alt source: note (0-based index) |
| `to_note_index` | number | Alt target: note (0-based index) |
| `from_media_index` | number | Alt source: media (0-based index) |
| `to_media_index` | number | Alt target: media (0-based index) |
| `relationship_type` | string | `parent_child`, `integration`, `dependency`, `alternative`, `data_flow`, `custom` |
| `line_style` | string | `solid`, `dashed`, `dotted` |
| `line_width` | number | Width in pixels (default 2) |
| `line_color` | string | Hex color (e.g., `#666666`) |
| `is_animated` | boolean | Animation enabled |
| `animation_shape` | string | `dot`, `circle`, `square`, `arrow` |
| `is_system` | boolean | Auto-generated parent-child edge |
| `label` | string | Edge label text |
| `description` | string | Extended description |
| `label_offset_x` | number | Label position 0-1 along edge |
| `label_offset_y` | number | Label perpendicular offset |
| `from_anchor` | string | `top`, `bottom`, `left`, `right`, `auto` |
| `to_anchor` | string | `top`, `bottom`, `left`, `right`, `auto` |
| `groups` | array | Group names for filtering |

### Edge Endpoint Resolution

Each edge must have exactly one `from_*` and one `to_*` field:
- `from_item_row` / `to_item_row` → Node (item on canvas)
- `from_note_index` / `to_note_index` → Note (text box)
- `from_media_index` / `to_media_index` → Media (image/video)

## Note Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `x` | number | Yes | X position |
| `y` | number | Yes | Y position |
| `width` | number | No | Width in pixels (default 200) |
| `height` | number | No | Height in pixels (default 150) |
| `z_index` | number | No | Layer order (negative = behind nodes) |
| `background_color` | string | No | Hex color (default `#FFFDE7`) |
| `font_color` | string | No | Hex color (default `#333333`) |
| `markdown_content` | string | No | Note text (supports markdown) |
| `text_align` | string | No | `left`, `center`, `right` |
| `vertical_align` | string | No | `top`, `middle`, `bottom` |

## Media Object

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `media_type` | string | Yes | `"image"` or `"youtube"` |
| `media_url` | string | Yes | Image URL or YouTube video ID |
| `title` | string | No | Title for AI context |
| `title_position` | string | No | `none`, `above`, `below`, `overlay` |
| `title_align` | string | No | `left`, `center`, `right` |
| `title_bg` | string | No | `dark`, `light`, `none` |
| `x` | number | Yes | X position |
| `y` | number | Yes | Y position |
| `width` | number | No | Width in pixels (default 200) |
| `height` | number | No | Height in pixels (default 150) |
| `z_index` | number | No | Layer order |
| `border_radius` | number | No | Corner radius (default 8) |
| `opacity` | number | No | 0-1 (default 1) |
| `play_inline` | boolean | No | Play video inline (default false) |
| `link_url` | string | No | Click-through URL |

## Group Object

Groups are used for edge filtering.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Group name |
| `color` | string | No | Hex color (default `#666666`) |

## Example

```json
{
  "canvases": [
    {
      "name": "Integration Map",
      "display_order": 0,
      "nodes": [
        {
          "item_row_number": 1,
          "item_name": "Slack",
          "x": 100,
          "y": 100,
          "size_level": 4
        },
        {
          "item_row_number": 2,
          "item_name": "Notion",
          "x": 300,
          "y": 100,
          "size_level": 4
        }
      ],
      "edges": [
        {
          "from_item_row": 1,
          "from_item_name": "Slack",
          "to_item_row": 2,
          "to_item_name": "Notion",
          "relationship_type": "integration",
          "line_style": "solid",
          "line_color": "#4F46E5",
          "label": "Notifications"
        }
      ],
      "notes": [
        {
          "x": 50,
          "y": 50,
          "width": 350,
          "height": 200,
          "z_index": -1,
          "background_color": "#E3F2FD",
          "markdown_content": "## Communication Stack"
        }
      ]
    }
  ]
}
```
