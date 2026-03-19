---
name: deip-overview
description: Provides core concepts and authentication for DEIP (Digital Ecosystem Intelligence Platform). Use when starting work with DEIP or understanding platform architecture.
---

# DEIP Platform Overview

DEIP (Digital Ecosystem Intelligence Platform) enables organizations to map, analyze, and optimize their digital tool ecosystems. It provides a structured way to catalog tools, services, people, and their relationships.

## Core Hierarchy

```
Workspace (Digital Ecosystem)
├── Categories (top-level grouping)
│   └── Subcategories (detailed grouping)
│       └── Items (tools, services, people, assets)
└── Canvases (visual relationship diagrams)
    ├── Nodes (items placed on canvas)
    ├── Notes (text boxes for grouping/context)
    ├── Media (images, YouTube videos)
    └── Edges (connections between elements)
```

## Authentication

All MCP operations require API key authentication. The API key determines:
- Which workspaces are accessible
- Permission level (viewer vs editor)

## UUID Format

All entity IDs use UUID format: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

**Critical**: Always use UUIDs for `workspace_id`, never workspace names. If you have a name, use `resolve_workspace` to get the UUID.

## Key Concepts

### Workspaces
Container for an entire digital ecosystem. Each workspace has:
- Base currency and FX rates
- Categories and subcategories for organization
- Items representing tools, services, and people
- Canvases for visual relationship mapping

### Items
The core entities in DEIP. Nine types available:
- `software`, `service`, `hardware`, `asset`, `human`, `ai`
- `module`, `feature`, `mobileapp` (child types requiring parent)

### Canvases
Visual diagrams for mapping relationships. Support:
- Placing items as nodes
- Creating connections (edges) between elements
- Adding notes and media for context

## API Access

DEIP can be accessed via **REST API** (recommended for cost efficiency) or **MCP tools**.

### REST API Base URL

```
https://api.deip.app
```

### Authentication Header

```
X-API-Key: deip_your_api_key_here
Content-Type: application/json
```

### MCP Server URL

```
https://mcp.deip.app
```

### API Domains

| Domain | Description |
|--------|-------------|
| **Workspace** | CRUD operations on workspaces |
| **Structure** | Manage categories and subcategories |
| **Items** | Create and manage ecosystem components |
| **Canvas** | Build visual relationship diagrams |
| **Analytics** | Get summaries and manage knowledge base |
| **Reminders** | Configure automated notifications |

## Error Recovery

See [error-handling.md](./error-handling.md) for mandatory error recovery patterns.

## Related Skills

- [Managing Workspaces](../deip-managing-workspaces/SKILL.md) - Workspace structure CRUD
- [Managing Items](../deip-managing-items/SKILL.md) - Item CRUD and types
- [Building Canvases](../deip-building-canvases/SKILL.md) - Visual diagram creation
- [Analytics & Context](../deip-analytics-context/SKILL.md) - Summaries and knowledge base
- [Reminders](../deip-reminders/SKILL.md) - Notification management
