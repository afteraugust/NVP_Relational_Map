---
tags:
  - dashboard
---

# Riverside Neighbors — Relational Map

## Quick Actions
- [[Templates/Person Template|+ New Person]]
- [[Templates/Organization Template|+ New Organization]]
- [[Templates/Interaction Template|+ New Interaction]]
- [[Templates/Idea Template|+ New Idea]]

## Team Dashboards
- [[August-Dashboard|August — Strategy & Partnerships]]
- [[Maria-Dashboard|Maria — Community Management]]
- [[David-Dashboard|David — Executive Direction]]

## Views
- [[Bases/All People|All People (CRM)]]
- [[Bases/All Organizations|Organizations]]
- [[Bases/Funding Pipeline|Funding Pipeline]]
- [[Bases/Interaction Log|Interaction Log]]
- [[Bases/Ideas Pipeline|Ideas Pipeline]]

## Strategic Maps
- [[Maps/Strategic Overview|Strategic Overview]]
- [[Maps/Funding Pathways|Funding Pathways]]
- [[Maps/Network Clusters|Network Clusters]]
- [[Maps/Event Partner Ecosystem|Event Partner Ecosystem]]

---

## Contacts Needing Followup

```dataview
TABLE warmth, owner, last-contact, next-followup
FROM "People/Network"
WHERE next-followup <= date(today) + dur(3 days)
SORT next-followup ASC
```

## Outstanding Action Items

```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE followup = true
SORT date ASC
```

## Active Projects

```dataview
TABLE status, lead, partners, goal
FROM "Projects"
WHERE status = "active" OR status = "planning"
SORT status ASC
```
