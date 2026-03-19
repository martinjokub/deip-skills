---
name: deip-changelog
description: Interpret the DEIP platform changelog. Use the read_changelog tool to fetch entries on demand.
---

# DEIP Changelog Interpretation

## Source of Truth

**`/CHANGELOG.md`** (project root) is the single source of truth. Vite copies it to `public/changelog.txt` at build time. The `read_changelog` tool fetches the published static file — no storage sync needed.

## When to Use

Use the `read_changelog` tool when the user asks about:
- Recent changes, updates, or releases
- What version the platform is on
- What was done in a specific version
- "Check the changelog" / "Lovable done the changes, check it"

## Version Format

`vMAJOR.MINOR.PATCH.BUILD` — e.g. `v0.9.0.206`

- **MAJOR.MINOR.PATCH** — set manually by the user (never auto-increment these)
- **BUILD** — auto-incremented with each release

## Changelog Entry Structure

Each entry starts with `## vX.Y.Z.N (YYYY-MM-DD HH:MM:SS CET)` followed by:
- `### Added` — new features
- `### Fixed` — bug fixes
- `### Changed` — behavioral changes
- `### Enhanced` — improvements to existing features

## How to Use the Tool

```
read_changelog({ count: 5 })        // Latest 5 entries (default)
read_changelog({ version: "v0.9.0.200" })  // Specific version
read_changelog({ count: 1 })        // Just the latest
```

## Summarization Guidelines

- Lead with the version number and date
- Group changes by category (Added/Fixed/Changed)
- Highlight user-facing impact over internal details
- If the user says "check the changelog", fetch the latest 3-5 entries and summarize
