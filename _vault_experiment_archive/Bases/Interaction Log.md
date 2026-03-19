---
tags:
  - bases-view
---

# Interaction Log

> **Bases Configuration:**
> - **Source:** folder `Interactions/`
> - **Filter:** `type` equals `interaction`
> - **Columns:** date, file name, participants, context, about, owner, followup
> - **Sort:** date descending
> - **Filter presets:** By owner, by followup=true (outstanding items), by date range

## Dataview Fallback

```dataview
TABLE date, participants, context, about, owner, followup
FROM "Interactions"
WHERE type = "interaction"
SORT date DESC
```
