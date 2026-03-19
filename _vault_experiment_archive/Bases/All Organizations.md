---
tags:
  - bases-view
---

# All Organizations

> **Bases Configuration:**
> - **Source:** folder `Organizations/`
> - **Filter:** `type` equals `organization`
> - **Columns:** Name, category, members, relevance, partners-with, location
> - **Group by:** category
> - **Sort:** name ascending

## Dataview Fallback

```dataview
TABLE category, members, relevance, partners-with, location
FROM "Organizations"
WHERE type = "organization"
SORT category ASC
```
