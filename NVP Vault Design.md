# NVP Relational Map — Vault Design

**Status:** Draft for review
**Date:** 2026-03-19
**Vault users:** August, Savannah

---

## 1. What This System Does

This vault is a relationship intelligence system for a small nonprofit team. It does three things:

1. **Tracks contacts and organizations** — who they are, how you're connected, what you've discussed
2. **Surfaces the network** — shows how people, orgs, funders, and projects connect to each other, including non-obvious pathways
3. **Keeps relationships alive** — flags who's overdue for a touchpoint based on desired communication cadence

The guiding principle: **interactions are the heartbeat**. Every coffee meeting, call, email, or event you log automatically updates the relational web. The system rewards consistent small entries over periodic big data dumps.

---

## 2. Entity Types

Six types of notes, each with a `type` property in frontmatter:

| Type | What it is | One note per... |
|------|-----------|-----------------|
| `person` | Anyone in your network | Individual person |
| `organization` | Business, nonprofit, agency, church, school, coalition | Organization |
| `project` | Your internal programs and initiatives | Project |
| `funder` | A specific funding opportunity or grant | Funding stream (not org — one org may have multiple) |
| `idea` | Potential collaboration, program, or partnership not yet active | Idea |
| `interaction` | A dated record of a meeting, call, email, or event | Conversation or encounter |

**Why these six and not fewer?** You could collapse Funder into Organization, or Idea into Project. But funders need their own pipeline tracking (status, deadline, amount), and ideas need a lightweight staging area before they graduate to projects. Keeping them separate makes Bases views and graph filtering cleaner.

**Why not more?** The archive had these same six and they cover the full lifecycle: people cluster around orgs, orgs connect to funders, funders enable projects, projects attract more people. Adding types like "event" or "grant-report" creates filing overhead without improving navigability.

---

## 3. Frontmatter Schemas

These are the fields that go in YAML frontmatter at the top of each note. The design goal: **every field should be something you'll fill in for 80%+ of new entries**. Optional context goes in the note body, not frontmatter.

### 3.1 Person

```yaml
---
type: person
role: contact
orgs:
  - "[[Org Name]]"
introduced-by: "[[Person Name]]"
cadence: monthly
next-touch: 2026-04-01
owner: "[[August]]"
tags:
  - person
---
```

**Field notes:**

- **role** — Single value, not a list. One of: `team`, `contact`, `funder-contact`, `elected-official`, `business-owner`, `faith-leader`, `educator`, `media`, `community-leader`. Default is `contact`. Pick the *primary* role; secondary roles go in the note body.
- **orgs** — Wikilinks to Organization notes. Most people have 1-2.
- **introduced-by** — How this person entered your network. Critical for pathway mapping. Leave blank if they're an original contact.
- **cadence** — How often you *want* to be in touch. One of: `weekly`, `biweekly`, `monthly`, `quarterly`, `as-needed`. This is the "desired frequency" half of your tracking.
- **next-touch** — The specific date by which you should reach out. Can be set manually ("touch base June 1st") or calculated from cadence. This is the actionable trigger.
- **owner** — Which team member owns the primary relationship. `[[August]]` or `[[Savannah]]`.
- **tags** — Always includes `person`. Add `funder-adjacent` if they're connected to funding. Keep tags minimal; use frontmatter fields for everything queryable.

**What's NOT here (and why):**

- **~~warmth~~** — The archive had `hot/warm/cool/cold/new`. In practice, warmth is subjective, changes constantly, and people forget to update it. Your `cadence` field captures the same intent (high-cadence contacts are your warm ones) with less maintenance.
- **~~knows~~** — Maintaining bidirectional `knows` lists is the biggest maintenance trap in the archive. Instead, relationships emerge automatically: if August and Priya both appear as `participants` in an interaction, the graph connects them. ExcaliBrain handles this.
- **~~interests~~** — These are better as tags or note body content. Putting them in frontmatter creates inconsistency (is it `small-business` or `small business` or `entrepreneurship`?).
- **~~last-contact~~** — Dataview can compute this from your most recent interaction. No need to manually track it.

### 3.2 Organization

```yaml
---
type: organization
category: nonprofit
members:
  - "[[Person Name]]"
partners-with:
  - "[[Org Name]]"
relevance:
  - funding
tags:
  - organization
---
```

**Field notes:**

- **category** — One of: `nonprofit`, `business`, `government`, `foundation`, `faith`, `education`, `media`, `association`.
- **members** — Wikilinks to people you know at this org. This creates the ExcaliBrain parent-child relationship (person's `orgs` → parent, org's `members` → children).
- **partners-with** — Other orgs this one has a relationship with. Creates Friend links in ExcaliBrain.
- **relevance** — What this org means to you. List from: `funding`, `marketing`, `programming`, `political`, `volunteers`, `space`. Multiple values OK.

### 3.3 Project

```yaml
---
type: project
status: active
lead: "[[August]]"
partners:
  - "[[Org Name]]"
funded-by:
  - "[[Funder Name]]"
tags:
  - project
---
```

**Field notes:**

- **status** — One of: `idea`, `planning`, `active`, `paused`, `completed`.
- **lead** — Which team member leads this project.
- **partners** — Orgs or people involved. Wikilinks.
- **funded-by** — Links to Funder notes (not org notes — a funder is a specific funding stream).

### 3.4 Funder

```yaml
---
type: funder
source-org: "[[Org Name]]"
contact: "[[Person Name]]"
connected-via: "[[Person Name]]"
status: prospect
amount: "10-50K"
deadline: 2026-06-01
tags:
  - funder
---
```

**Field notes:**

- **source-org** — The organization that houses this funding opportunity.
- **contact** — Your point person at the funder.
- **connected-via** — Who in your network connected you to this opportunity. Critical for understanding funding pathways.
- **status** — Pipeline stage. One of: `prospect`, `cultivating`, `applied`, `funded`, `declined`.
- **amount** — Free text. Use whatever makes sense: "$5K", "10-50K", "$100K+", "in-kind".
- **deadline** — Application or decision deadline. Leave blank if ongoing/rolling.

### 3.5 Idea

```yaml
---
type: idea
status: seed
proposed-by: "[[Person Name]]"
relevant-to:
  - "[[Project or Org Name]]"
tags:
  - idea
---
```

**Field notes:**

- **status** — One of: `seed`, `exploring`, `proposed`, `shelved`. When an idea becomes real, promote it to a Project note and link back.
- **proposed-by** — Who surfaced this idea.
- **relevant-to** — What existing projects, orgs, or people it connects to.

### 3.6 Interaction

```yaml
---
type: interaction
date: 2026-03-19
participants:
  - "[[August]]"
  - "[[Person Name]]"
context: coffee-meeting
about:
  - "[[Project or Org Name]]"
owner: "[[August]]"
tags:
  - interaction
---
```

**Field notes:**

- **date** — When it happened. Also encoded in the filename.
- **participants** — Everyone involved, including team members. This is the field that builds the relational web. When two people share an interaction, they become connected in the graph.
- **context** — One of: `coffee`, `call`, `email`, `text`, `event`, `meeting`, `site-visit`.
- **about** — What projects, orgs, ideas, or funders were discussed. Wikilinks.
- **owner** — Which team member is responsible for any follow-up.

**This is the most important note type.** It's the only one you create regularly (everything else is created once and updated occasionally). Making this fast to log is the whole game.

---

## 4. Folder Structure

```
NVP_Relational_Map/
├── 00-Dashboard/
│   ├── Home.md
│   ├── August Dashboard.md
│   └── Savannah Dashboard.md
├── People/
│   ├── Team/
│   │   ├── August.md
│   │   └── Savannah.md
│   └── Network/
│       └── (all contacts)
├── Organizations/
├── Projects/
├── Funders/
├── Ideas/
├── Interactions/
├── Templates/
├── Meta/
│   ├── Vault Design.md (this file — move here after review)
│   ├── Conventions.md
│   └── Plugin Setup.md
└── _vault_experiment_archive/
```

**Why this structure:**

- **00-Dashboard/** sorts first. Two personal dashboards + a shared home.
- **People/Team/** vs **People/Network/** separates your 2 internal files from the growing contact list. Bases and Dataview query across both seamlessly.
- **Interactions/** is where most new files land. Named `YYYY-MM-DD Title` for chronological sorting.
- **Templates/** holds Templater templates.
- **Meta/** holds documentation for how to use the vault.
- No **Maps/** folder for now (no manual Excalidraw maps). ExcaliBrain generates its visualizations on the fly.
- No **Bases/** folder. Bases views in Obsidian are created via the UI, not as markdown files. The archive's Bases folder just held placeholder specs.

---

## 5. Plugin Stack

### 5.1 Essential (Install First)

| Plugin | Why | Dependency? |
|--------|-----|-------------|
| **Dataview** | Computed queries on dashboards (overdue contacts, interaction counts, funding pipeline). Also required by ExcaliBrain. | None |
| **Templater** | Standardized note creation with prompted fields and auto-dates. Dramatically reduces friction for new entries. | None |
| **Excalidraw** | Rendering engine for ExcaliBrain. You won't use it directly unless you later want hand-drawn maps. | None |
| **ExcaliBrain** | Auto-generated semantic relationship graph. Click any entity, see all connections by type. The primary tool for network visualization. | Dataview + Excalidraw |

### 5.2 Recommended

| Plugin | Why |
|--------|-----|
| **Homepage** | Auto-opens your personal dashboard when you launch Obsidian. Small quality-of-life improvement. |
| **Calendar** | Shows interactions on a calendar view. Click a date, see what conversations happened. Useful for "when did I last talk to X?" |
| **Various Complements** | Autocomplete for wikilinks and frontmatter values as you type. Speeds up data entry and reduces typos in link names. |
| **Tag Wrangler** | Right-click any tag to rename it across the entire vault. Insurance against taxonomy drift. |

### 5.3 ExcaliBrain Configuration

In ExcaliBrain settings → Ontology, map your frontmatter fields:

| ExcaliBrain Type | Your Fields | What It Means Visually |
|-----------------|-------------|----------------------|
| **Parent** | `orgs`, `source-org`, `funded-by` | "belongs to" — the entity above this one |
| **Child** | `members`, `contact`, `participants` | "contains" — entities within this one |
| **Friend** | `partners-with` | Peer/mutual relationships |
| **Other Friend** | `introduced-by`, `connected-via`, `proposed-by` | Directional — how the connection was formed |

What this gives you: navigate to "Riverside Community Foundation" and immediately see Marcus Bell (child/member), your grant (child/funded-by), United Way (friend/partner), and Diane Okafor (other friend/connected-via). All automatic from frontmatter.

### 5.4 Templater Configuration

- Template folder: `Templates/`
- Enable "Trigger Templater on new file creation"
- Folder templates:
  - `People/` → Person Template
  - `Organizations/` → Organization Template
  - `Projects/` → Project Template
  - `Funders/` → Funder Template
  - `Ideas/` → Idea Template
  - `Interactions/` → Interaction Template

### 5.5 Native Features to Configure

**Graph View** — Already enabled. Configure color groups:
- `path:People` → Blue
- `path:Organizations` → Green
- `path:Funders` → Gold
- `path:Projects` → Orange
- `path:Ideas` → Purple
- `path:Interactions` → Gray (or hide — they add noise at scale)

**Bases** — Already enabled. Create views after templates are set up:
- All People view (source: `People/`, columns: role, orgs, cadence, next-touch, owner)
- Funding Pipeline (source: `Funders/`, columns: status, source-org, contact, amount, deadline)
- Interaction Log (source: `Interactions/`, columns: date, participants, context, about)

**Bookmarks** — Pin your dashboard and most-used Bases views.

---

## 6. Communication Cadence Tracking

This is the system for "reach out to X every two weeks" and "touch base with Y on June 1st."

### How it works

Each person note has two fields:

- **`cadence`** — The rhythm: `weekly`, `biweekly`, `monthly`, `quarterly`, `as-needed`
- **`next-touch`** — The specific next date to reach out

When you log an interaction with someone, you update their `next-touch` date. The Templater interaction template will remind you.

### Dashboard query: Who needs attention?

```dataview
TABLE role, orgs, cadence, next-touch, owner
FROM "People/Network"
WHERE next-touch <= date(today) + dur(3 days)
SORT next-touch ASC
```

This shows everyone whose `next-touch` is within 3 days (past or future). It's the "who should I call today?" list.

### Dashboard query: Overdue contacts (dropped balls)

```dataview
TABLE role, orgs, cadence, next-touch, owner
FROM "People/Network"
WHERE next-touch < date(today) - dur(7 days)
SORT next-touch ASC
```

Anyone more than a week past their `next-touch`. This is the "oops" list.

### Dashboard query: Last interaction with each contact

```dataviewjs
// For each person in Network, find their most recent interaction
const people = dv.pages('"People/Network"').where(p => p.type === "person");
const interactions = dv.pages('"Interactions"').where(i => i.type === "interaction");

const rows = [];
for (const person of people) {
  const personInteractions = interactions
    .where(i => i.participants && i.participants.some(p =>
      dv.fileLink(person.file.name).path === dv.parse(p)?.path ||
      String(p).includes(person.file.name)
    ))
    .sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0 ? personInteractions[0].date : "No interactions";
  const daysSince = personInteractions.length > 0
    ? Math.floor((Date.now() - new Date(personInteractions[0].date)) / 86400000)
    : "—";

  rows.push([person.file.link, person.owner, person.cadence, lastDate, daysSince + " days"]);
}

dv.table(["Person", "Owner", "Cadence", "Last Contact", "Days Since"],
  rows.sort((a, b) => (b[4] === "— days" ? -1 : parseInt(b[4]) - parseInt(a[4]))));
```

This computes `last-contact` automatically from your interaction log — no manual date tracking needed.

---

## 7. Key Dataview Queries

Beyond cadence tracking, these queries power the dashboards:

### Funding pipeline overview

```dataview
TABLE source-org, contact, connected-via, status, amount, deadline
FROM "Funders"
WHERE type = "funder"
SORT deadline ASC
```

### Recent interactions (last 30 days)

```dataview
TABLE date, participants, context, about
FROM "Interactions"
WHERE date >= date(today) - dur(30 days)
SORT date DESC
```

### Who connects us to a specific org?

Replace `[[Target Org]]` with any org name:

```dataview
TABLE orgs, introduced-by, role
FROM "People/Network"
WHERE contains(orgs, [[Target Org]])
```

### Active and planning projects

```dataview
TABLE status, lead, partners, funded-by
FROM "Projects"
WHERE status = "active" OR status = "planning"
```

### Ideas pipeline

```dataview
TABLE status, proposed-by, relevant-to
FROM "Ideas"
SORT status ASC
```

---

## 8. Workflows

### Adding a new contact

1. Create a new note in `People/Network/`. Name it their full name.
2. Templater auto-fills the Person template with today's date and prompts for owner.
3. Fill in: `role`, `orgs` (link to existing org or create new), `introduced-by`, `cadence`, `next-touch`.
4. In the note body, write a sentence or two of context: how you met, why they matter, anything to remember.
5. If they belong to an org that already has a note, add them to that org's `members` list.

### Logging an interaction

1. Create a new note in `Interactions/`. Name it `YYYY-MM-DD Brief Title`.
2. Templater auto-fills date, prompts for owner and context type.
3. Fill in `participants` (link everyone involved) and `about` (link relevant projects/orgs/ideas).
4. Write a brief summary in the note body. List action items with checkboxes.
5. Update `next-touch` on each participant's person note.

This is the critical habit. If you do nothing else, log interactions.

### Before a meeting

1. Search for the person's note. Read their context and any linked interactions.
2. Open ExcaliBrain on their note — see their organizational connections, who introduced them, what projects they're linked to.
3. Check the funding pipeline if they're funder-adjacent.

### Weekly review

1. Open your personal dashboard.
2. Scan the "needs attention" query — who's overdue?
3. Check outstanding action items from recent interactions.
4. Review the funding pipeline for approaching deadlines.
5. Quick sync with your co-user: any new connections, introductions, or status changes?

---

## 9. What This Looks Like in the Graph

**Native Graph View** (filtered by color groups): You see a constellation where blue People nodes cluster around green Organization nodes. Gold Funder nodes connect through specific people to your orange Project nodes. Dense clusters reveal your strongest community ties; isolated nodes reveal relationships that need development.

**ExcaliBrain** (navigating from a specific entity): Click on a funder and see the org it comes from (parent), your contact person (child), and who connected you (other friend). Click on that contact person and see their other org affiliations (parents), who they know that you also know (friends), and every interaction you've had with them (children via participants field). This is how you trace the "funding triangle" and "marketing amplification loop" patterns the archive identified — but automatically, without hand-drawing anything.

---

## 10. Design Decisions Log

Decisions made during design, with rationale:

| Decision | Rationale |
|----------|-----------|
| Dropped `warmth` field | Cadence captures the same intent with less subjectivity. A `weekly` contact is inherently "hot." |
| Dropped `knows` field | Relationships emerge from shared interactions. Manual bidirectional maintenance doesn't scale. |
| Dropped `interests` from frontmatter | Inconsistent free-text values break queries. Use tags or note body instead. |
| Dropped `last-contact` from frontmatter | Dataview computes it from interactions. One less field to manually update. |
| Dropped hand-drawn Excalidraw maps | Manual maintenance overhead for a 2-person team. ExcaliBrain + Graph View covers visualization. Can add later. |
| Dropped Relay plugin | Already syncing via git. Two sync layers create conflict risk. |
| Kept Funder as separate entity type | One org may have multiple funding streams with different deadlines and statuses. |
| Kept Idea as separate entity type | Lightweight staging before something becomes a Project with real tracking needs. |
| 6-8 frontmatter fields per entity | Community consensus: field creep is the #1 CRM abandonment cause. |
| Interactions as the engine | Every logged interaction enriches the graph, updates cadence math, and builds institutional memory. Low-friction interaction logging is the design priority. |
