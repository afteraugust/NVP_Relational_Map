---
tags:
  - dashboard
---

# David — Executive Direction

## Upcoming Meetings & Followups

```dataview
TABLE warmth, last-contact, next-followup, orgs
FROM "People/Network"
WHERE owner = [[David Torres]] AND next-followup <= date(today) + dur(14 days)
SORT next-followup ASC
```

## Funding Pipeline Overview

```dataview
TABLE status, source-org, contact, amount-range, deadline
FROM "Funders"
SORT deadline ASC
```

## My Recent Interactions

```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE owner = [[David Torres]]
SORT date DESC
LIMIT 10
```

## Key Relationships (Hot + Warm)

```dataview
TABLE warmth, orgs, interests
FROM "People/Network"
WHERE owner = [[David Torres]] AND (warmth = "hot" OR warmth = "warm")
SORT warmth ASC
```

## All Projects

```dataview
TABLE status, lead, funded-by, goal
FROM "Projects"
SORT status ASC
```
