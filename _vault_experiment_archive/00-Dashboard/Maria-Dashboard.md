---
tags:
  - dashboard
---

# Maria — Community Management

## My Contacts Needing Followup

```dataview
TABLE warmth, last-contact, next-followup, orgs
FROM "People/Network"
WHERE owner = [[Maria Chen]] AND next-followup <= date(today) + dur(7 days)
SORT next-followup ASC
```

## Contacts Not Touched in 30+ Days

```dataview
TABLE warmth, last-contact, orgs
FROM "People/Network"
WHERE owner = [[Maria Chen]] AND last-contact <= date(today) - dur(30 days)
SORT last-contact ASC
```

## My Recent Interactions

```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE owner = [[Maria Chen]]
SORT date DESC
LIMIT 10
```

## Outstanding Followups

```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE owner = [[Maria Chen]] AND followup = true
SORT date ASC
```

## Active Events & Projects

```dataview
TABLE status, partners, timeline
FROM "Projects"
WHERE lead = [[Maria Chen]]
SORT status ASC
```
