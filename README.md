# DEIP Skills for AI Agents — [GitHub](https://github.com/martinjokub/deip-skills)

Modular documentation that enables AI agents to interact with **DEIP** (Digital Ecosystem Intelligence Platform) — mapping, analyzing, and optimizing digital tool ecosystems.

## What Are Skills?

Skills are structured markdown files that teach AI agents how to use DEIP's APIs. Each skill covers a specific domain (workspaces, items, canvases, etc.) and includes:

- **REST API endpoints** — Direct HTTP calls (budget-efficient, no tool overhead)
- **MCP tool references** — For environments supporting Model Context Protocol
- **Examples & patterns** — Request/response samples, workflow guides

## Installation

### For Google Antigravity IDE

#### Workspace Scope (per-project)

**Linux / macOS:**
```bash
# From your project root
mkdir -p .agent/skills
cp -r skills/deip-* .agent/skills/
```

**Windows (PowerShell):**
```powershell
# From your project root
New-Item -ItemType Directory -Force -Path .agent\skills
Copy-Item -Recurse skills\deip-* .agent\skills\
```

#### Global Scope (all projects)

**Linux / macOS:**
```bash
mkdir -p ~/.gemini/antigravity/skills
cp -r skills/deip-* ~/.gemini/antigravity/skills/
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.gemini\antigravity\skills"
Copy-Item -Recurse skills\deip-* "$env:USERPROFILE\.gemini\antigravity\skills\"
```

### For Other AI Agents (Claude, GPT, etc.)

Include the skill files in your agent's context or system prompt. Each `SKILL.md` is self-contained and can be loaded individually.

## API Key Setup

All DEIP API calls require an API key:

1. Log in to [DEIP](https://deip.app)
2. Open **Settings** → **API Keys** tab
3. Click **Create API Key** — select permissions (read, write, delete)
4. Copy the key (starts with `deip_`) — it won't be shown again

### Authentication Header

```
X-API-Key: deip_your_api_key_here
Content-Type: application/json
```

## REST API vs MCP — Which to Use?

| Approach | Agent Token Cost | API Cost | Best For |
|----------|-----------------|----------|----------|
| **REST API** (recommended) | Low — agent reads docs once, constructs `fetch`/`curl` calls | Free | Budget-efficient, high-volume workflows |
| **MCP Server** | Higher — tool discovery + per-call reasoning tokens | Free | Auto-discovery, tool-native environments |

### REST API (Recommended for Cost Efficiency)

The agent reads the skill documentation, then makes direct HTTP calls. No per-tool reasoning overhead.

```bash
# Example: List workspaces
curl -H "X-API-Key: deip_xxx" \
  https://api.deip.app/workspaces

# Example: Create an item
curl -X POST -H "X-API-Key: deip_xxx" \
  -H "Content-Type: application/json" \
  -d '{"name":"Slack","type":"service","subcategoryId":"sub-uuid"}' \
  https://api.deip.app/workspaces/{workspace_id}/items
```

### MCP Server

For environments that support MCP tool calling:

```json
{
  "mcpServers": {
    "deip": {
      "type": "http",
      "url": "https://mcp.deip.app",
      "headers": {
        "X-API-Key": "deip_your_api_key_here"
      }
    }
  }
}
```

## Skills Index

| Skill | Description | Key Operations |
|-------|-------------|----------------|
| [deip-overview](./deip-overview/SKILL.md) | Core concepts, auth, error recovery | Platform architecture |
| [deip-managing-workspaces](./deip-managing-workspaces/SKILL.md) | Workspace CRUD, categories, currencies | Create/manage ecosystems |
| [deip-managing-items](./deip-managing-items/SKILL.md) | Items (tools, services, people) | Bulk create, parent-child |
| [deip-building-canvases](./deip-building-canvases/SKILL.md) | Visual diagrams, nodes, edges | Relationship mapping |
| [deip-analytics-context](./deip-analytics-context/SKILL.md) | Summaries, knowledge base | Cost analysis, learnings |
| [deip-reminders](./deip-reminders/SKILL.md) | Automated notifications | Payment/renewal alerts |
| [deip-global-catalog](./deip-global-catalog/SKILL.md) | Item enrichment catalog | Auto-fill metadata |

## Skill File Structure

```
deip-overview/
├── SKILL.md              # Main skill file (required)
└── error-handling.md     # Supporting reference docs
```

Each `SKILL.md` has YAML frontmatter:

```yaml
---
name: deip-overview
description: Core concepts and authentication for DEIP.
---
```

## Updating Skills

When skills are updated, pull the latest version:

```bash
# If cloned as a git repo
cd .agent/skills  # or ~/.gemini/antigravity/skills
git pull

# Or re-download and replace
```

## License

These skills are provided for use with DEIP platform accounts. An active DEIP account and API key are required.

---
*Last synced: 2026-03-06*
