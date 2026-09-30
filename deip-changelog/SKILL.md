---
name: deip-changelog
description: Interpret the DEIP platform changelog. Use the read_changelog tool to fetch entries on demand.
---

# DEIP Changelog Interpretation

## Source of Truth

**`public.changelog_releases`** (Postgres table) is the single source of truth as of v0.9.4.0. Every `bump-version` call inserts a row with structured `sections` (JSONB), `case_refs[]`, `areas[]`, and a zero-padded `version_sort` column that orders 4-part semver correctly. The `read_changelog` tool queries this table directly.

There is no `CHANGELOG.md` to edit and no `public/changelog.txt` to sync. A tiny fallback lives in `src/generated/latestRelease.ts` so the sidebar never displays `v0.0.0.0` on first paint before the DB responds.

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
read_changelog({ count: 5 })                       // Latest 5 entries (default)
read_changelog({ version: "v0.9.0.200" })          // Specific version
read_changelog({ since_version: "v0.9.3.100" })    // Everything newer than X
read_changelog({ contains_case: "CASE-2339" })     // Releases mentioning a case
read_changelog({ area: "dealhub" })                // Releases tagged with an area
read_changelog({ format: "json" })                 // Structured rows instead of MD
```

## Summarization Guidelines

- Lead with the version number and date
- Group changes by category (Added/Fixed/Changed)
- Highlight user-facing impact over internal details
- If the user says "check the changelog", fetch the latest 3-5 entries and summarize
