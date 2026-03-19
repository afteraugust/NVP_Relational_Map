---
tags:
  - bases-view
---

# All People — CRM View

> **Bases Configuration:**
> - **Source:** folder `People/`
> - **Filter:** `type` equals `person`
> - **Columns:** Name, warmth, owner, orgs, last-contact, next-followup, interests, role
> - **Sort:** next-followup ascending
> - **Filter presets:** By owner (August / Maria / David), by warmth, by interest area

This file is a placeholder for a Bases view. In Obsidian, create a new Base using:
1. Command palette → "Bases: Create new base"
2. Set folder source to `People/`
3. Configure columns matching the spec above

## Dataview Fallback

If Bases is not yet configured, this query provides equivalent functionality:

```dataview
TABLE warmth, owner, orgs, last-contact, next-followup, interests
FROM "People"
WHERE type = "person"
SORT next-followup ASC
```
