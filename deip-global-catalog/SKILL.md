# Skill: Global Item Catalog

## Purpose
Search a centralized knowledge base of common tools, services, and software before creating items. Enrich items with pre-existing metadata (descriptions, vendor URLs, icons, pricing, parent-child relationships).

## REST API Endpoint

Base URL: `https://api.deip.app`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/catalog/search?q=term` | Search catalog for matching items |

### Example REST Call

```bash
# Search catalog for "Slack"
curl -H "X-API-Key: deip_xxx" \
  "https://api.deip.app/catalog/search?q=Slack"
```

## When to Use
- **Before creating items**: Always call `catalog_search` first (or `GET /catalog/search?q=name`) to check if the item exists in the catalog
- **After user mentions a tool**: If user says "we use Slack" or "add Google Workspace", search the catalog
- **For enrichment**: If an item lacks vendor, description, or icon data, check the catalog

## Key Rules
1. **Never overwrite user data** — only fill empty fields
2. **Catalog may not be configured** — if `catalog_search` returns `catalog_configured: false`, proceed normally without catalog data
3. **Fuzzy matches need confirmation** — if `match_type` is "fuzzy", confirm with the user: "Did you mean [catalog name]?"
4. **Exact/contains matches can auto-suggest** — show enrichment options directly
5. **Children are optional** — when a catalog item has children (modules/features), offer to add them but don't auto-add

## Workflow
```
1. User mentions a tool → call catalog_search with the name
2. If match found:
   a. Exact/contains: "I found [Name] in the catalog with vendor, description, and icon. Apply?"
   b. Fuzzy: "Did you mean [Catalog Name]? I have details for it."
3. If user confirms, use catalog data to enrich the item (fill empty fields only)
4. If catalog item has children, offer: "[Name] has N related modules. Add them?"
```

## Match Types
- **exact** (score: 1.0): Case-insensitive exact match
- **contains** (score: 0.9): Substring match in either direction  
- **fuzzy** (score: 0.7-0.99): Levenshtein distance-based similarity (skipped for queries ≤3 chars)

## Fields Enriched from Catalog
- `vendor` — Company/vendor name
- `vendorUrl` — Official website
- `description` — What the tool does
- `notes` — Additional context
- `customIconName` — Lucide icon name
- `customIconUrl` — Custom icon image URL
- `type` — Item type (software, service, etc.)
- `priceValue` — Default price value
- `currency` — Default currency code
- `billingCycle` — Default billing cycle (monthly, annual, etc.)

## Parent-Child Relationships
Catalog items may have children (e.g., Google Workspace → Docs, Sheets, Drive). When creating a parent from the catalog:
1. Create the parent item first
2. Offer to add children with `parentItemId` set to the parent
3. Place children in the same subcategory
