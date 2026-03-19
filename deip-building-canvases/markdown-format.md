# Canvas Markdown Import Format

Canvases are defined in the `## RELATIONS CANVASES` section of workspace markdown exports.

## Structure Overview

```markdown
## RELATIONS CANVASES

### Canvas: [Name]
**Display Order:** [number]

#### Nodes
| Row | Name | X | Y | Size | Icon Override |

#### Notes
| Index | X | Y | Width | Height | BgColor | FontColor | Align | VAlign | Content |

#### Media
| Index | Type | X | Y | Width | Height | URL | Title |

#### Edges
| From | To | Type | Style | Color | Animated | Label | Description | FromAnchor | ToAnchor |

#### Groups
| Name | Color |

---
```

## Nodes Table

Places workspace items on the canvas.

| Column | Description |
|--------|-------------|
| Row | 1-based item row number from items list |
| Name | Item name (for reference/fallback) |
| X | X position in pixels |
| Y | Y position in pixels |
| Size | Size level 1-7 (default 3) |
| Icon Override | `library:IconName`, `upload:url`, or `-` |

### Example

```markdown
| Row | Name | X | Y | Size | Icon Override |
|-----|------|---|---|------|---------------|
| 1 | Slack | 100 | 100 | 4 | - |
| 2 | Notion | 300 | 100 | 4 | library:FileText |
| 5 | Custom App | 500 | 100 | 3 | upload:https://example.com/icon.png |
```

## Notes Table

Text boxes for grouping and context.

| Column | Description |
|--------|-------------|
| Index | 0-based index (for edge references) |
| X, Y | Position in pixels |
| Width, Height | Dimensions in pixels |
| BgColor | Hex background color |
| FontColor | Hex text color |
| Align | `left`, `center`, `right` |
| VAlign | `top`, `middle`, `bottom` |
| Content | Markdown content (escape `\|` as `\\|`, newlines as `\n`) |

### Content Escaping

- Pipe characters: `\|`
- Newlines: `\n`

### Example

```markdown
| Index | X | Y | Width | Height | BgColor | FontColor | Align | VAlign | Content |
|-------|---|---|-------|--------|---------|-----------|-------|--------|---------|
| 0 | 50 | 50 | 350 | 200 | #E3F2FD | #333333 | left | top | ## Communication\nCore messaging tools |
```

## Media Table

Images and YouTube videos.

| Column | Description |
|--------|-------------|
| Index | 0-based index (for edge references) |
| Type | `image` or `youtube` |
| X, Y | Position in pixels |
| Width, Height | Dimensions in pixels |
| URL | Image URL or YouTube video ID |
| Title | Optional title for AI context |

### Example

```markdown
| Index | Type | X | Y | Width | Height | URL | Title |
|-------|------|---|---|-------|--------|-----|-------|
| 0 | image | 400 | 300 | 200 | 150 | https://example.com/diagram.png | Architecture Diagram |
| 1 | youtube | 400 | 500 | 320 | 180 | dQw4w9WgXcQ | Product Demo |
```

## Edges Table

Connections between elements.

| Column | Description |
|--------|-------------|
| From | `Row N`, `Note N`, or `Media N` |
| To | `Row N`, `Note N`, or `Media N` |
| Type | Relationship type (see below) |
| Style | `solid`, `dashed`, `dotted` |
| Color | Hex color |
| Animated | `Yes` or `No` |
| Label | Edge label text |
| Description | Extended description |
| FromAnchor | `top`, `bottom`, `left`, `right`, `auto`, or `-` |
| ToAnchor | `top`, `bottom`, `left`, `right`, `auto`, or `-` |

### Relationship Types

| Type | Description |
|------|-------------|
| `parent_child` | Parent to module/feature/mobileapp |
| `integration` | Tools that sync/share data |
| `dependency` | Tool A requires Tool B |
| `alternative` | Interchangeable tools |
| `data_flow` | Data moves from A to B |
| `custom` | Custom relationship |

### Example

```markdown
| From | To | Type | Style | Color | Animated | Label | Description | FromAnchor | ToAnchor |
|------|----|------|-------|-------|----------|-------|-------------|------------|----------|
| Row 1 | Row 2 | integration | solid | #4F46E5 | No | Sync | Bidirectional sync | right | left |
| Note 0 | Row 3 | - | dashed | #666666 | No | - | - | - | - |
| Row 4 | Media 0 | data_flow | solid | #10B981 | Yes | Export | Daily export | - | - |
```

## Groups Table

Edge filter groups.

| Column | Description |
|--------|-------------|
| Name | Group name |
| Color | Hex color |

### Example

```markdown
| Name | Color |
|------|-------|
| Core Integrations | #4F46E5 |
| Optional | #9CA3AF |
```

## Full Canvas Example

```markdown
## RELATIONS CANVASES

### Canvas: Integration Map
**Display Order:** 0

#### Nodes
| Row | Name | X | Y | Size | Icon Override |
|-----|------|---|---|------|---------------|
| 1 | Slack | 100 | 100 | 4 | - |
| 2 | Notion | 300 | 100 | 4 | - |
| 3 | Zapier | 200 | 250 | 3 | - |

#### Notes
| Index | X | Y | Width | Height | BgColor | FontColor | Align | VAlign | Content |
|-------|---|---|-------|--------|---------|-----------|-------|--------|---------|
| 0 | 50 | 50 | 350 | 150 | #E3F2FD | #333333 | left | top | ## Communication |

#### Edges
| From | To | Type | Style | Color | Animated | Label | Description | FromAnchor | ToAnchor |
|------|----|------|-------|-------|----------|-------|-------------|------------|----------|
| Row 1 | Row 3 | integration | solid | #4F46E5 | No | Triggers | - | bottom | top |
| Row 3 | Row 2 | integration | solid | #4F46E5 | Yes | Actions | Automated sync | bottom | top |

#### Groups
| Name | Color |
|------|-------|
| Automation | #4F46E5 |

---
```
