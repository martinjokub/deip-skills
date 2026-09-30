# Reading Canvas Semantics

This guide teaches AI agents how to interpret the semantic meaning of canvas data retrieved via MCP tools.

## Node Context

Every node on a canvas represents a workspace item. The `_item` object contains fields that explain the item's purpose:

| Field | What It Tells You |
|-------|-------------------|
| `_item.notes` | User's own context — why they use it, how many seats, renewal dates, custom notes |
| `_item.description` | What the tool/service does functionally |
| `_item.type` | Category: `software`, `service`, `module`, `feature`, `mobileapp`, `hardware`, etc. |
| `_item.vendor` | Who provides it |

**Always read `_item.notes` and `_item.description`** — these are the primary sources of meaning for understanding why an item exists in the ecosystem.

### Example

```json
{
  "name": "HubSpot",
  "_item": {
    "type": "software",
    "vendor": "HubSpot Inc.",
    "description": "Customer relationship management platform",
    "notes": "Primary CRM, 50 seats, renews March. Marketing team owns this."
  }
}
```

From this you know: HubSpot is the organization's main CRM with 50 users, owned by marketing, renewing in March.

## Edge Meaning

Edges carry semantic meaning through three combined fields:

| Field | Purpose |
|-------|---------|
| `relationship_type` | The category of relationship |
| `label` | Short visible summary on the connection line |
| `description` | Full explanation of the relationship mechanics |

**Interpretation formula**: `[from item] --[label]--> [to item]` with `relationship_type` providing the category and `description` providing the details.

### Examples

| Type | Label | Description | Meaning |
|------|-------|-------------|---------|
| `integration` | "syncs contacts" | "Zapier syncs new contacts from HubSpot to Slack #sales every 6 hours" | These tools share contact data bidirectionally via Zapier |
| `dependency` | "requires auth" | "App cannot function without SSO provider for user authentication" | The source tool structurally depends on the target |
| `data_flow` | "sends leads" | "Marketing-qualified leads are pushed from website forms to CRM" | Data moves in one direction from source to destination |
| `alternative` | "backup option" | "Can replace Slack if pricing becomes prohibitive" | These tools are interchangeable |

### Direction Matters

For directional types, `from` and `to` carry specific meaning:
- **dependency**: `from` = the dependent tool, `to` = the tool it depends on
- **data_flow**: `from` = data source, `to` = data destination
- **parent_child**: `from` = parent, `to` = child

## Grouping Note Context

Notes with `z_index < 0` are **grouping backgrounds**. Their `markdown_content` provides context for ALL objects visually positioned on top of them.

### How to Associate

1. Identify notes with `z_index < 0`
2. Check which nodes/media fall within the note's bounding box (`x`, `y`, `width`, `height`)
3. The note's `markdown_content` describes or categorizes all contained objects

### Example

A note at `(100, 100)` with `width: 520`, `height: 240`, `z_index: -1` and content `"## Marketing Tools"` — any nodes positioned within `x: 100-620, y: 100-340` belong to the "Marketing Tools" group.

### Nested Grouping

When a parent note (`z_index: -2`) contains sub-group notes (`z_index: -1`), the hierarchy provides layered context:
- Parent note context applies to everything inside
- Sub-group note context is more specific to its contained nodes

## Parent-Child Hierarchy

Edges with `is_system: true` are auto-generated when items with parent-child relationships are both placed on the canvas. These reveal structural hierarchy:

| Parent Type | Child Types | Meaning |
|-------------|-------------|---------|
| `software` | `module`, `feature` | Software contains these components |
| `service` | `feature`, `mobileapp` | Service provides these capabilities |

System edges:
- Have `relationship_type: "parent_child"`
- Cannot be deleted while both items are on the canvas
- Direction: parent → child
- Indicate that the child item is a component/part of the parent

## Custom Connection Types

Users can create custom connection types beyond the 6 built-in ones (`parent_child`, `integration`, `dependency`, `alternative`, `data_flow`, `custom`).

Custom types carry domain-specific semantics:
- The `relationship_type` string itself may be meaningful (e.g., `"billing_flow"`, `"auth_chain"`, `"yes"`, `"no"`)
- The `label` on custom-typed edges is the primary indicator of meaning
- The `description` provides full context

When encountering an unfamiliar `relationship_type`, read the `label` and `description` to understand the user's intended meaning.

## Building Complete Understanding

Follow this step-by-step workflow to fully interpret a canvas:

### Step 1: Read All Nodes
For each node, read `_item.notes`, `_item.description`, `_item.type`, and `_item.vendor` to understand what each item is and why it's in the ecosystem.

### Step 2: Identify Grouping Notes
Find notes with `z_index < 0`. Map which nodes/media fall within each note's bounds. Associate the note's `markdown_content` with its contained objects.

### Step 3: Read Edges
For each edge, combine `relationship_type` + `label` + `description` to understand WHY two objects are connected. Pay attention to direction for directional types.

### Step 4: Trace Hierarchy
Identify system edges (`is_system: true`) to understand parent-child structural relationships. This reveals the ecosystem's organizational hierarchy.

### Step 5: Synthesize
Combine all context:
- Individual item purpose (from node item data)
- Group membership (from grouping notes)
- Relationships and data flows (from edges)
- Structural hierarchy (from parent-child edges)

This gives you a complete semantic understanding of the ecosystem represented on the canvas.

## When Understanding Is Incomplete: Ask the User

Not all relationships will have clear labels or descriptions. When the semantic meaning of a connection is ambiguous or missing, **ask the user** rather than guessing.

### When to Ask

- An edge has no `label` and no `description` — you don't know WHY these items are connected
- A custom `relationship_type` has no clear meaning from its name alone
- Multiple edges suggest a pattern but you're not confident in the interpretation
- A grouping note is empty or vague but contains many important nodes
- An item's `_item.notes` and `_item.description` are both empty for a key node

### How to Ask

Frame questions to confirm or expand understanding:

- "I see HubSpot is connected to Slack with type 'integration' but no label — what does this integration do? Does it sync contacts, send notifications, or something else?"
- "There are 5 tools grouped under a note titled 'Core Stack' — what makes these your core tools? Are they all mission-critical?"
- "I notice a custom connection type 'yes' between Tool A and Tool B — what does this represent in your workflow?"

### Record What You Learn

When the user answers, **store the insight in the knowledge base** using `update_knowledge_base` with category `[RELATIONSHIP]`. This ensures the meaning persists across conversations and doesn't need to be asked again.

Example flow:
1. Agent sees unlabeled edge between Salesforce and Mailchimp
2. Agent asks: "What's the relationship between Salesforce and Mailchimp?"
3. User responds: "Salesforce sends qualified leads to Mailchimp for nurture campaigns"
4. Agent stores: `[RELATIONSHIP] Salesforce → Mailchimp: Lead nurture pipeline` in knowledge base
5. Agent updates the edge with `label: "sends leads"` and `description: "Qualified leads pushed to Mailchimp nurture campaigns"`
