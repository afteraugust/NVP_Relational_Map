---
tags:
  - dashboard
---

# August — Strategy & Partnerships

## My Contacts Needing Followup

```dataview
TABLE warmth, last-contact, next-followup, orgs
FROM "People/Network"
WHERE owner = [[August]] AND next-followup <= date(today) + dur(7 days)
SORT next-followup ASC
```

## My Recent Interactions

```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE owner = [[August]]
SORT date DESC
LIMIT 10
```

## Funding Pipeline

```dataview
TABLE status, contact, amount-range, deadline
FROM "Funders"
SORT deadline ASC
```

## Ideas I'm Exploring

```dataview
TABLE status, potential-partners, category
FROM "Ideas"
WHERE contains(file.outlinks, [[August]])
SORT status ASC
```

## My Network (All Contacts)

```dataview
TABLE warmth, orgs, interests
FROM "People/Network"
WHERE owner = [[August]]
SORT warmth ASC
```
