# Canvas Notes

Notes are text boxes for grouping, labeling, and adding context to canvases. They serve multiple roles depending on size, z_index, and content.

## Note Patterns

### 1. Grouping Background

Large notes placed behind nodes to visually group related items.

- `z_index: -1` (or lower) to sit behind nodes
- Semantic background color
- Nodes are positioned on top

```json
{
  "x": 100, "y": 100,
  "width": 520, "height": 240,
  "markdown_content": "## Marketing Tools",
  "background_color": "#E3F2FD",
  "z_index": -1
}
```

**This is the ONLY note pattern that may overlap with other elements.**

#### Contextual Association

When a grouping note sits behind nodes, its `markdown_content` provides semantic context for ALL objects visually placed on top. AI agents should:
1. Identify notes with `z_index < 0`
2. Determine which nodes/media fall within the note's bounding box
3. Associate the note's content with all contained objects

For example, a note with content `"## Marketing Tools"` means all nodes positioned within its bounds belong to the marketing category.

### 2. Nested Grouping (3-Level Hierarchy)

A parent note contains sub-group notes, which contain nodes:

```
Parent Note (z_index: -2, 2300×1900px)
├── Sub-Note A (z_index: -1, 500×300px)
│   ├── Node 1
│   ├── Node 2
│   └── Node 3
├── Sub-Note B (z_index: -1, 500×300px)
│   ├── Node 4
│   └── Node 5
└── Sub-Note C (z_index: -1, 400×250px)
    ├── Node 6
    └── Node 7
```

Guidelines:
- Parent note: `z_index: -2`
- Sub-group notes: `z_index: -1`
- Nodes: default `z_index: 0`
- 40-60px padding between parent and sub-notes

### 3. Content Card

Standalone notes used as information cards — no nodes needed.

- Fixed size: **200×150px**
- Arranged in grid layout
- Color-coded by category
- Contains title + details in markdown

```json
{
  "x": 100, "y": 100,
  "width": 200, "height": 150,
  "markdown_content": "## Phase 1\n\nDiscovery & Research\n\n**40 hours**",
  "background_color": "#FFFDE7",
  "z_index": 0
}
```

Grid spacing: 220px horizontal, 170px vertical (center-to-center).

Content cards **must not overlap** other elements.

### 4. Schema Cell

Small notes representing database fields/columns, connected by edges.

- Header cells: **200×60px**, saturated background
- Data cells: **200×60-80px**, pastel background
- Arranged in vertical columns per entity
- Connected via edges to show relationships

```json
// Header
{
  "x": 100, "y": 100,
  "width": 200, "height": 60,
  "markdown_content": "**Threads**",
  "background_color": "#1565C0",
  "font_color": "#FFFFFF",
  "text_align": "center",
  "vertical_align": "middle"
}

// Data cell
{
  "x": 100, "y": 170,
  "width": 200, "height": 70,
  "markdown_content": "**id** (uuid)\nPrimary key",
  "background_color": "#E3F2FD"
}
```

Schema cells **must not overlap** other elements. Spacing: 250px between columns, 80px between rows.

## Grouping Note Sizes

| Pattern | Width | Height | Use |
|---------|-------|--------|-----|
| Small group | 200-520px | 150-240px | 1-3 nodes |
| Medium group | 500-800px | 300-480px | 4-8 nodes |
| Large group | 900-2300px | 700-1900px | 10+ nodes, sub-groups |
| Content card | 200px | 150px | Phase/task description |
| Schema header | 200px | 60px | Column title |
| Schema cell | 200px | 60-80px | Database field |

## Background Colors

### Semantic Colors (Grouping Notes)

| Color | Hex | Meaning |
|-------|-----|---------|
| Light Blue | `#E3F2FD` | Information, neutral |
| Light Green | `#E8F5E9` | Success, active |
| Light Yellow | `#FFFDE7` | Warning, attention |
| Light Red | `#FFEBEE` | Error, critical |
| Light Purple | `#F3E5F5` | Special, premium |
| White | `#FFFFFF` | Clean, minimal |
| Light Orange | `#FFF3E0` | Secondary category |

### Schema Color Coding

| Entity | Header (saturated) | Cell (pastel) |
|--------|-------------------|---------------|
| Threads | `#1565C0` | `#E3F2FD` |
| Files | `#283593` | `#E8EAF6` |
| Opportunities | `#F57F17` | `#FFFDE7` |
| Records | `#2E7D32` | `#E8F5E9` |

## Z-Index Layering

| Value | Use |
|-------|-----|
| -2 | Parent grouping note (nested hierarchy) |
| -1 | Sub-group note / standard grouping background |
| 0 | Default (content cards, schema cells, nodes) |
| 1+ | Foreground elements |

## Text Alignment

| Property | Values |
|----------|--------|
| `text_align` | `left`, `center`, `right` |
| `vertical_align` | `top`, `middle`, `bottom` |

## Markdown Support

Notes support Markdown:
- Headers: `# H1`, `## H2`, etc.
- Bold: `**text**`
- Lists: `- item` or `1. item`
- Links: `[text](url)`
- Code blocks (see below)

## Code Blocks

Notes support fenced code blocks with live syntax highlighting. Wrap code in triple backticks with an optional language tag:

````markdown
```json
{
  "key": "value",
  "count": 42
}
```
````

### Supported Features

- **Syntax highlighting**: Applied live during editing with a debounced (250ms) re-render
- **Color scheme**: Grey comments (`#6b7280`), yellow JSON keys (`#fbbf24`), blue strings/values (`#60a5fa`)
- **Line numbers**: Displayed automatically alongside code content
- **Cursor stability**: Character-offset preservation prevents cursor jumping during highlight re-renders

### Code Block in Content Cards

Code blocks work inside any note pattern — grouping backgrounds, content cards, and schema cells. When using code in a content card, increase height to accommodate the code:

```json
{
  "x": 100, "y": 100,
  "width": 300, "height": 250,
  "markdown_content": "## API Config\n\n```json\n{\"endpoint\": \"/api/v1\",\n \"auth\": \"bearer\"}\n```",
  "background_color": "#F3E5F5",
  "z_index": 0
}
```

### Best Practices

1. Use language tags (`json`, `sql`, `javascript`, etc.) for proper highlighting
2. Keep code blocks concise — long blocks may require scrolling inside the note
3. Notes use `overflow-hidden` in view mode and `overflow-auto` in edit mode for internal scrolling
