# Canvas Layout Patterns

Visual organization patterns for effective canvas diagrams.

## Critical: Non-Overlap Rule

**All canvas elements must maintain clear separation. No visual overlapping is allowed, with one exception.**

### Rules
1. **Nodes** must never overlap other nodes or media
2. **Media** must never overlap other media or nodes
3. **Notes as content cards** must never overlap other elements
4. **Exception**: Notes used as **grouping backgrounds** (`z_index < 0`) may sit behind nodes, other notes, and media

### Minimum Spacing (Center-to-Center)

| Node Size Level | Pixel Size | Minimum Spacing |
|-----------------|------------|-----------------|
| 1 (compact) | 44px | 120px |
| 3 (default) | 72px | 140px |
| 4 | ~94px | 160px |
| 5 (important) | 116px | 180px |
| 7 (hero) | 176px | 240px |

### Inside Grouping Notes
- Maintain 20-40px padding from note edges to contained nodes
- Maintain standard spacing between nodes inside the note

---

## Grid Layout

Best for: Tool inventories, category views, structured lists

```
┌─────────────────────────────────────┐
│  [Node]    [Node]    [Node]         │
│   100,100   250,100   400,100       │
│                                     │
│  [Node]    [Node]    [Node]         │
│   100,250   250,250   400,250       │
│                                     │
│  [Node]    [Node]    [Node]         │
│   100,400   250,400   400,400       │
└─────────────────────────────────────┘
```

Guidelines:
- Spacing: 140-200px between nodes (center-to-center)
- Origin (0,0) is top-left
- Align related items in rows/columns

## Grouped Layout

Best for: Department views, tool categories, logical groupings

```
┌─────────────────────────────────────────────────┐
│  ┌── Note (z:-1) ──┐  ┌── Note (z:-1) ──┐      │
│  │   Marketing     │  │   Engineering   │      │
│  │  [N1] [N2] [N3] │  │  [N4] [N5] [N6] │      │
│  └─────────────────┘  └─────────────────┘      │
│                                                 │
│  ┌── Note (z:-1) ──┐                           │
│  │   Operations    │                           │
│  │  [N7] [N8]      │                           │
│  └─────────────────┘                           │
└─────────────────────────────────────────────────┘
```

### Grouping Note Sizes

| Group Size | Note Width | Note Height | Use |
|------------|-----------|-------------|-----|
| Small (1-3 nodes) | 200-520px | 150-240px | Single category |
| Medium (4-8 nodes) | 500-800px | 300-480px | Department |
| Large (10+ nodes) | 900-2300px | 700-1900px | Sub-groups inside |

Guidelines:
- Place note as background (z_index: -1)
- Position nodes on top of note with 20-40px padding from edges
- Use semantic background colors
- Leave 500-600px between groups

### Nested Grouping (3-Level Hierarchy)

Parent note contains sub-group notes, which contain nodes:

```
┌─── Parent Note (z:-2, 2300x1900px) ──────────────────┐
│  "Platform (70 tools)"                                │
│                                                       │
│  ┌── Sub-Note (z:-1) ──┐  ┌── Sub-Note (z:-1) ──┐   │
│  │  Communication      │  │  Marketing           │   │
│  │  [N1] [N2] [N3]     │  │  [N4] [N5] [N6]     │   │
│  └─────────────────────┘  └──────────────────────┘   │
│                                                       │
│  ┌── Sub-Note (z:-1) ──┐                             │
│  │  Ecommerce          │                             │
│  │  [N7] [N8]          │                             │
│  └─────────────────────┘                             │
└───────────────────────────────────────────────────────┘
```

Guidelines:
- Parent note: `z_index: -2`
- Sub-group notes: `z_index: -1`
- Nodes: `z_index: 0` (default)

## Flow Layout

Best for: Data flows, integrations, pipelines

```
┌─────────────────────────────────────────────────┐
│                                                 │
│  [Source 1] ──→                                │
│               ↘                                 │
│  [Source 2] ──→ [Processor] ──→ [Destination]  │
│               ↗                                 │
│  [Source 3] ──→                                │
│                                                 │
└─────────────────────────────────────────────────┘
```

Guidelines:
- Data sources on left
- Processors in middle
- Destinations on right
- Use `data_flow` edges with animation

## Hub and Spoke

Best for: Central tool with integrations, dependency mapping

```
┌─────────────────────────────────────────────────┐
│                  [Tool 1]                       │
│                     ↑                           │
│       [Tool 2] ←── [HUB] ──→ [Tool 3]          │
│                     ↓                           │
│                  [Tool 4]                       │
└─────────────────────────────────────────────────┘
```

Guidelines:
- Central item at larger size (size_level: 5-7)
- Satellites evenly distributed around
- Use `integration` or `dependency` edges

## Hierarchical Layout

Best for: Parent-child relationships, org charts

```
┌─────────────────────────────────────────────────┐
│              [Parent Tool]                      │
│                   │                             │
│        ┌──────────┼──────────┐                 │
│        │          │          │                 │
│    [Module]   [Feature]  [Mobile]              │
└─────────────────────────────────────────────────┘
```

Guidelines:
- Parent at top or center
- Children below or around
- Use `parent_child` edges (auto-created)

## Content Card Grid

Best for: Phase planning, task boards, project timelines (notes only, no nodes)

```
┌─────────────────────────────────────────────────┐
│  ┌─ Note ──┐  ┌─ Note ──┐  ┌─ Note ──┐        │
│  │Phase 1  │  │Phase 2  │  │Phase 3  │        │
│  │40 hours │  │60 hours │  │30 hours │        │
│  │#FFFDE7  │  │#FFFDE7  │  │#FFF3E0  │        │
│  └─────────┘  └─────────┘  └─────────┘        │
│                                                 │
│  ┌─ Note ──┐  ┌─ Note ──┐                     │
│  │Phase 4  │  │Phase 5  │                     │
│  │25 hours │  │45 hours │                     │
│  │#FFF3E0  │  │#FFF3E0  │                     │
│  └─────────┘  └─────────┘                     │
└─────────────────────────────────────────────────┘
```

Guidelines:
- Note size: 200×150px each
- Spacing: 220px horizontal, 170px vertical (center-to-center)
- Color-code by phase group or category
- Use markdown for title + details inside each note
- No nodes needed — notes ARE the content

## Schema Diagram

Best for: Database schemas, data models, field mapping (notes as cells, connected by edges)

```
┌─────────────────────────────────────────────────────────┐
│  ┌─ Header ─┐  ┌─ Header ─┐  ┌─ Header ─┐             │
│  │ Threads  │  │  Files   │  │ Records  │             │
│  │ #1565C0  │  │ #283593  │  │ #2E7D32  │             │
│  └──────────┘  └──────────┘  └──────────┘             │
│  ┌─ Cell ───┐  ┌─ Cell ───┐  ┌─ Cell ───┐             │
│  │ id       │──│ thread_id│  │ id       │             │
│  │ #E3F2FD  │  │ #E8EAF6  │  │ #E8F5E9  │             │
│  └──────────┘  └──────────┘  └──────────┘             │
│  ┌─ Cell ───┐  ┌─ Cell ───┐  ┌─ Cell ───┐             │
│  │ subject  │  │ name     │──│ file_id  │             │
│  │ #E3F2FD  │  │ #E8EAF6  │  │ #E8F5E9  │             │
│  └──────────┘  └──────────┘  └──────────┘             │
└─────────────────────────────────────────────────────────┘
```

Guidelines:
- Header notes: 200×60px, saturated background color
- Cell notes: 200×60-80px, pastel variant of header color
- Arrange in vertical columns, one per table/entity
- Connect cells with edges to show relationships (foreign keys)
- Use `data_flow` or `dependency` edge types

### Color Coding for Schema Columns

| Entity | Header Color | Cell Color |
|--------|-------------|------------|
| Threads | `#1565C0` (blue) | `#E3F2FD` (light blue) |
| Files | `#283593` (indigo) | `#E8EAF6` (light indigo) |
| Opportunities | `#F57F17` (amber) | `#FFFDE7` (light yellow) |
| Records | `#2E7D32` (green) | `#E8F5E9` (light green) |

## Positioning Tips

| Pattern | Start Position | Spacing |
|---------|----------------|---------|
| Grid | (100, 100) | 150px horizontal, 150px vertical |
| Grouped | (50, 50) for first group | 500-600px between groups |
| Flow | (100, height/2) | 250-300px between stages |
| Hub | (center, center) | 200-250px radius |
| Hierarchical | (center, 100) | 200px between levels |
| Content Card | (100, 100) | 220px horizontal, 170px vertical |
| Schema | (100, 100) | 250px between columns, 80px between rows |

## Size Recommendations

| Element | Size Level |
|---------|------------|
| Primary/hero items | 5-7 |
| Standard items | 3 |
| Secondary/supporting | 1-3 |
| Central hub items | 5-7 |
