---
tags:
  - meta
---

# How to Use Dataview

Dataview powers the dashboard queries — the tables that show overdue contacts, upcoming touchpoints, relational suggestions, and the funding pipeline. You don't need to write Dataview queries yourself unless you want custom views. The dashboards are already set up.

---

## What Dataview Does for You (Automatically)

The dashboards in `00-Dashboard/` contain embedded Dataview queries that:

- **Compute last-contact dates** by scanning Interactions for the most recent one with each person
- **Compare cadence vs. reality** — if someone has `cadence: monthly` and hasn't been contacted in 35 days, they show as overdue
- **Surface relational suggestions** — "you talked to X recently, Y is at the same org and hasn't been contacted"
- **Display the funding pipeline, project status, and recent interactions** as sortable tables

These queries run every time you open the dashboard. They always reflect the current state of your vault.

---

## Where the Queries Live

| Dashboard | What it shows |
|-----------|--------------|
| **Home** | Overdue contacts (all), upcoming touchpoints (all), relational suggestions, funding pipeline, recent interactions, active projects |
| **August Dashboard** | Same views filtered to `owner: [[August]]` |
| **Savannah Dashboard** | Same views filtered to `owner: [[Savannah]]` |

---

## Reading the Dashboard Tables

**Overdue Contacts table:**
- Shows anyone past their cadence window, starting from the day they're due
- "3 days overdue" means their cadence expired 3 days ago
- "No interactions yet" means you have a contact with a cadence set but no logged interactions
- Sorted by most overdue first

**Upcoming Touchpoints table:**
- Shows the next 20 contacts coming due within 30 days
- "Due in 5d" means their cadence window expires in 5 days
- Sorted by soonest due first

**Relational Suggestions table:**
- Looks at the last 14 days of interactions
- Finds people who share an org with someone you recently talked to
- Only suggests people you *haven't* contacted recently
- If empty, your network is well-tended

---

## Writing Your Own Queries (Optional)

If you want a custom view, you can add Dataview queries to any note. Two syntaxes:

**Simple table query** (DQL — Dataview Query Language):

````markdown
```dataview
TABLE role, orgs, cadence, owner
FROM "People/Network"
WHERE cadence = "weekly"
SORT file.name ASC
```
````

This shows all weekly-cadence contacts with their role, orgs, cadence, and owner.

**JavaScript query** (for computed fields like "days since last contact"):

````markdown
```dataviewjs
// Your JavaScript here — see the dashboards for examples
```
````

---

## Useful Queries to Try

**All contacts at a specific org:**
````markdown
```dataview
TABLE role, cadence, owner
FROM "People/Network"
WHERE contains(orgs, [[Org Name]])
```
````

**All funders by status:**
````markdown
```dataview
TABLE source-org, contact, status, focus-areas
FROM "Funders"
SORT status ASC
```
````

**All interactions with a specific person:**
````markdown
```dataview
TABLE date, context, about
FROM "Interactions"
WHERE contains(participants, [[Person Name]])
SORT date DESC
```
````

**Projects at idea stage:**
````markdown
```dataview
TABLE lead, partners
FROM "Projects"
WHERE status = "idea"
```
````

---

## Tips

- Dataview reads from your frontmatter fields. If a field is empty or misspelled, the query won't find it.
- Queries update live — edit a note's frontmatter and the dashboard tables reflect it immediately.
- If a query shows "No results," it usually means the folder is empty or the field names don't match what the query expects.
- You can put Dataview queries in any note, not just dashboards. Want a "related people" query inside an org note? Go for it.
