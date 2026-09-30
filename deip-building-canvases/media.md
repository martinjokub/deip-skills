# Canvas Media

Add images and YouTube videos to canvases for visual context.

## Critical: Non-Overlap Rule

**Media must not overlap with nodes or other media.** Position media in its own dedicated area on the canvas, separate from node clusters.

## Media Types

| Type | Description | URL Format |
|------|-------------|------------|
| `image` | Any image URL | Full URL (https://...) |
| `youtube` | YouTube video | Video ID only (e.g., `dQw4w9WgXcQ`) |

## Creating Media

### Image
```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid",
  "media_type": "image",
  "media_url": "https://example.com/diagram.png",
  "x": 100,
  "y": 100,
  "width": 400,
  "height": 300,
  "title": "Integration Architecture",
  "description": "Diagram showing how Zapier connects HubSpot, Slack, and Mailchimp"
}
```

### YouTube Video
```json
{
  "workspace_id": "uuid",
  "canvas_id": "uuid",
  "media_type": "youtube",
  "media_url": "dQw4w9WgXcQ",
  "x": 500,
  "y": 100,
  "width": 480,
  "height": 270,
  "title": "Product Demo"
}
```

## Title and Description

| Field | Purpose |
|-------|---------|
| `title` | Short caption displayed on canvas |
| `description` | Detailed context for AI understanding |

**Use description for:**
- Architecture diagrams
- Screenshots
- Workflow images
- Explanatory videos

The description helps AI agents understand media content without analyzing the actual image/video.

## Positioning

- Default dimensions: 300×200 pixels
- YouTube recommended: 480×270 or 640×360
- Position relative to canvas origin (0,0 = top-left)
- Keep clear separation from nodes and other media

## Properties

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `x`, `y` | number | required | Position |
| `width`, `height` | number | 300, 200 | Dimensions |
| `title` | string | null | Caption |
| `description` | string | null | AI context |
| `z_index` | number | 0 | Layer order |
| `opacity` | number | 1 | Transparency (0-1) |
| `border_radius` | number | 0 | Corner rounding |

## Best Practices

1. Always add `description` for AI context
2. Use meaningful `title` for visual labels
3. Size appropriately for content type
4. Position in dedicated area away from node clusters
5. Do not overlap with nodes or other media
