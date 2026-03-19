---
tags:
  - bases-view
---

# Ideas Pipeline

> **Bases Configuration:**
> - **Source:** folder `Ideas/`
> - **Filter:** `type` equals `idea`
> - **Columns:** Name, status, proposed-by, potential-partners, category, relevant-to
> - **Sort:** status (exploring first, then seed)

## Dataview Fallback

```dataview
TABLE status, proposed-by, potential-partners, category, relevant-to
FROM "Ideas"
WHERE type = "idea"
SORT status ASC
```
