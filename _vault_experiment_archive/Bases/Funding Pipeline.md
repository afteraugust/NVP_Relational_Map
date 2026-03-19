---
tags:
  - bases-view
---

# Funding Pipeline

> **Bases Configuration:**
> - **Source:** folder `Funders/`
> - **Filter:** `type` equals `funder`
> - **Columns:** Name, source-org, contact, connected-via, status, amount-range, deadline
> - **Sort:** deadline ascending
> - **Color coding:** status (prospect=gray, cultivating=yellow, applied=blue, funded=green, declined=red)

## Dataview Fallback

```dataview
TABLE source-org, contact, connected-via, status, amount-range, deadline
FROM "Funders"
WHERE type = "funder"
SORT deadline ASC
```
