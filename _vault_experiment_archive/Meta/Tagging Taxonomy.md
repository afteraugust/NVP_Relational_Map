---
tags:
  - meta
---

# Tagging Taxonomy

## Entity Type Tags (Required — one per file)
- `person` — any individual
- `organization` — any org, business, agency, etc.
- `project` — internal programs and initiatives
- `funder` — specific funding opportunities
- `idea` — potential collaborations not yet in motion
- `interaction` — dated record of a meeting/call/email

## Role Tags (People)
- `team` — our staff
- `contact` — general network contact (default)
- `funder-adjacent` — connected to funding ecosystem

## Utility Tags
- `dashboard` — dashboard and view files
- `bases-view` — Bases configuration files
- `meta` — documentation and conventions

## Interest Areas (Frontmatter, not tags)
These are used in the `interests` frontmatter field, not as tags:
- `community-development`
- `small-business`
- `civic-engagement`
- `housing`
- `youth`
- `faith`
- `health`
- `education`
- `arts`
- `environment`
- `workforce`

## Relevance Categories (Organizations, frontmatter)
Used in the `relevance` field on Organization files:
- `marketing` — helps us reach more people
- `funding` — connected to money
- `programming` — potential programming partner
- `political` — civic/political influence
- `volunteers` — source of volunteer labor

## Design Principle
We keep tags minimal and use frontmatter fields for most categorization. This is because:
1. Frontmatter is queryable by Dataview AND editable by Bases
2. Tags are useful for broad categorization but become unwieldy at scale
3. ExcaliBrain reads frontmatter fields, not tags
