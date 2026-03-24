# NVP Relational Map — Vault Design

**Status:** Draft v2 — revised per feedback
**Date:** 2026-03-19
**Vault users:** August, Savannah

---

## 1. What This System Does

This vault is a relationship intelligence system for a small nonprofit team. It does three things:

1. **Tracks contacts and organizations** — who they are, how you're connected, what you've discussed
2. **Surfaces the network** — shows how people, orgs, funders, and projects connect to each other, including non-obvious pathways
3. **Keeps relationships alive** — flags who's overdue for a touchpoint based on desired communication cadence

The guiding principle: **interactions are the heartbeat**. Every meeting, call, email, or event you log automatically updates the relational web. The system rewards consistent small entries over periodic big data dumps.

---

## 2. Entity Types

Five types of notes, each with a `type` property in frontmatter:

| Type | What it is | One note per... |
|------|-----------|-----------------|
| `person` | Anyone in your network | Individual person |
| `organization` | Business, nonprofit, agency, church, coalition, etc. | Organization |
| `project` | Your internal programs, initiatives, and early-stage ideas | Project (including those at `idea` stage) |
| `funder` | A funding source or relationship | Funder entity (not individual grants — those live in the note body) |
| `interaction` | A dated record of a meeting, call, email, or event | Conversation or encounter |


---

## 3. Frontmatter Schemas

### 3.1 Person

```yaml
---
type: person
role: contact
orgs:
  - "[[Org Name]]"
introduced-by: "[[Person Name]]"
cadence: monthly
owner: "[[August]]"
tags:
  - person
---
```

**Field notes:**

- **role** — Single value. One of: `NVP-team`, `NVP-board-member`, `contact`, `faith`, `funder-contact`, `elected-official`, `business-owner`, `media`, `community-leader`. Default is `contact`. Pick the *primary* role; secondary roles go in the note body.
- **orgs** — Wikilinks to Organization notes. Most people have 1-2.
- **introduced-by** — How this person entered your network. Critical for pathway mapping. Leave blank if they're an original contact.
- **cadence** — How often you *want* to be in touch. One of: `weekly`, `biweekly`, `monthly`, `quarterly`, `as-needed`. Dataview computes whether you're on track by comparing this against your most recent interaction.
- **owner** — Which team member owns the primary relationship. `[[August]]` or `[[Savannah]]`.
- **tags** — Always includes `person`. Optionally add `funder-adjacent` if they're connected to funding.


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

- **category** — One of: `nonprofit`, `business`, `government`, `foundation`, `faith`, `media`, `association`, `education`. These eight categories map to how you'd actually filter and group orgs in a Bases view. Each represents a distinct relationship pattern: nonprofits are programming partners, businesses are sponsors and community stakeholders, government means political influence and public funding, foundations mean private funding, faith communities provide access and volunteers, media means visibility. If a new category genuinely emerges in your work, add it then.
- **members** — Wikilinks to people you know at this org. Creates the ExcaliBrain parent-child link (person's `orgs` → parent, org's `members` → children).
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

- **status** — One of: `idea`, `planning`, `active`, `paused`, `completed`. Use `idea` for early-stage concepts that haven't been committed to yet. When an idea matures, change status to `planning` — no migration needed.
- **lead** — Which team member leads this project.
- **partners** — Orgs or people involved. Wikilinks.
- **funded-by** — Links to Funder notes. Wikilinks.

### 3.4 Funder

```yaml
---
type: funder
source-org: "[[Org Name]]"
contact: "[[Person Name]]"
connected-via: "[[Person Name]]"
status: prospect
focus-areas:
  - community-development
  - civic-engagement
tags:
  - funder
---
```

A Funder note represents your **funding relationship** with a source — not a single grant or opportunity. One funder may have multiple grants, programs, or giving cycles. Specific opportunities, amounts, deadlines, and application details live in the note body (and deadline reminders go in Google Calendar).

**Field notes:**

- **source-org** — The organization that houses this funder. Wikilink. Creates the ExcaliBrain parent link.
- **contact** — Your point person at the funder. Wikilink. Creates ExcaliBrain child link.
- **connected-via** — Who in your network connected you to this funder. Critical for understanding funding pathways. Creates ExcaliBrain "other friend" link.
- **status** — Relationship stage. One of: `prospect` (we know they fund things like ours), `cultivating` (actively building the relationship), `active` (current funding relationship), `past` (previously funded us, relationship dormant).
- **focus-areas** — What this funder cares about. Free-text list. Helps match funders to projects.

### 3.5 Interaction

```yaml
---
type: interaction
date: 2026-03-19
participants:
  - "[[August]]"
  - "[[Person Name]]"
context: call
about:
  - "[[Project or Org Name]]"
logged-by: "[[August]]"
tags:
  - interaction
---
```

**This is the most important note type.** It's the only one you create regularly. Making this fast to log is the whole game.

**Field notes:**

- **date** — When it happened. Also encoded in the filename for sorting.
- **participants** — Everyone involved, including team members. This is the field that builds the relational web — when two people share an interaction, they become connected in the graph.
- **context** — One of: `call`, `email`, `text`, `event`, `meeting`. Keep it simple.
- **about** — What projects, orgs, or funders were discussed. Wikilinks.
- **logged-by** — Which team member recorded this interaction. Powers personal dashboard filters ("show me all interactions I've logged this month").

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
├── Interactions/
├── Templates/
├── Meta/
│   ├── Vault Design.md (this file)
│   ├── Conventions.md
│   └── Plugin Setup.md
└── _vault_experiment_archive/
```

**Why this structure:**

- **00-Dashboard/** sorts first. Two personal dashboards + a shared home.
- **People/Team/** vs **People/Network/** separates your internal files from the growing contact list. Bases and Dataview query across both seamlessly.
- **Interactions/** is where most new files land. Named `YYYY-MM-DD Title` for chronological sorting.
- **Templates/** holds Templater templates.
- **Meta/** holds documentation for how to use the vault.
- No **Ideas/** folder — early-stage concepts live in **Projects/** with `status: idea`.
- No **Maps/** folder — ExcaliBrain generates visualizations on the fly, no manual map files needed.

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
| **Calendar** | Shows interactions on a calendar view. Click a date, see what conversations happened. |
| **Various Complements** | Autocomplete for wikilinks and frontmatter values as you type. Speeds up data entry and reduces typos. |
| **Tag Wrangler** | Right-click any tag to rename it across the entire vault. Insurance against taxonomy drift. |

### 5.3 ExcaliBrain Configuration

In ExcaliBrain settings → Ontology, clear the defaults from each field and enter these:

| ExcaliBrain Field | Your Values | What It Means Visually |
|------------------|-------------|----------------------|
| **Parents** | `orgs, source-org, funded-by` | "belongs to" — appears above the focused node |
| **Children** | `members, contact, participants` | "contains" — appears below the focused node |
| **Left-Side Friends** | `partners-with` | Peer/mutual relationships — appears to the left |
| **Right-Side Friends** | *(clear defaults, leave empty)* | Reserved for future use |
| **Previous (Friends)** | `introduced-by, connected-via` | "how the connection was formed" — the prior link in the chain |
| **Next (Friends)** | *(clear defaults, leave empty)* | Reserved for future use |

**Important:** Clear all the default values (advantages, alternatives, cons, Before, After, etc.) from each field before entering yours. Those defaults are ExcaliBrain's generic examples and will create noise if left in.

What this gives you: navigate to a foundation and immediately see its staff contacts (below), your funder note linked to it (below/funded-by), partner orgs (left), and who connected you (further left as previous). All automatic from frontmatter.

### 5.4 Templater Configuration

- Template folder: `Templates/`
- Enable "Trigger Templater on new file creation"
- Folder templates:
  - `People/` → Person Template
  - `Organizations/` → Organization Template
  - `Projects/` → Project Template
  - `Funders/` → Funder Template
  - `Interactions/` → Interaction Template

### 5.5 Native Features to Configure

**Graph View** — Configure color groups:
- `path:People` → Blue
- `path:Organizations` → Green
- `path:Funders` → Gold
- `path:Projects` → Fuchsia
- `path:Interactions` → Gray

To hide Interactions from the global graph (they add visual noise at scale), type `-path:Interactions` in the Graph View filter/search bar. Toggle the filter on or off as needed.

**Bases** — Create views by right-clicking a folder in the file explorer → "New base", or via the command palette. This creates a `.base.md` file that opens as an interactive spreadsheet, not a regular markdown page. Suggested views:
- All People view (source: `People/`, columns: role, orgs, cadence, owner)
- Funding Pipeline (source: `Funders/`, columns: status, source-org, contact, focus-areas)
- Interaction Log (source: `Interactions/`, columns: date, participants, context, about)
- Project Tracker (source: `Projects/`, columns: status, lead, partners, funded-by)

**Bookmarks** — Pin your dashboard and most-used Bases views.

---

## 6. Communication Cadence Tracking

The cadence system splits responsibilities between two tools: **Obsidian tracks rhythm**, **Google Calendar tracks specific dates**.

### How it works

Each person note has a `cadence` field: `weekly`, `biweekly`, `monthly`, `quarterly`, or `as-needed`.

Dataview automatically computes when you last interacted with each person by scanning the Interactions folder for notes where they appear in `participants`. It then compares that date against their cadence to determine who's on track, who's coming due, and who's overdue.

Specific dated reminders — "follow up with Megan on June 1st," "send proposal by Friday" — go in Google Calendar. These are deadline-oriented tasks that benefit from push notifications, which Obsidian can't provide.

**The only thing you need to do:** log interactions. The rest is computed.

### Dashboard view: Upcoming Touchpoints

The next 20 contacts due for outreach, looking one month ahead:

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed");
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction");

const cadenceDays = {
  "weekly": 7, "biweekly": 14, "monthly": 30, "quarterly": 90
};

const rows = [];
for (const person of people) {
  const personInteractions = interactions.where(i =>
    i.participants && i.participants.some(p =>
      String(p).includes(person.file.name)
    )
  ).sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0
    ? personInteractions[0].date
    : null;

  if (!lastDate) {
    rows.push([person.file.link, person.role, person.cadence, "Never", "Overdue", person.owner]);
    continue;
  }

  const last = new Date(lastDate.toString());
  const days = cadenceDays[person.cadence] || 30;
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysUntilDue = Math.floor((dueDate - today) / 86400000);
  const maxLookahead = 30;

  if (daysUntilDue <= maxLookahead) {
    const status = daysUntilDue < 0
      ? "⚠️ " + Math.abs(daysUntilDue) + "d overdue"
      : daysUntilDue === 0
        ? "📍 Due today"
        : "📅 Due in " + daysUntilDue + "d";
    rows.push([person.file.link, person.role, person.cadence,
      lastDate, status, person.owner]);
  }
}

rows.sort((a, b) => {
  const aNum = parseInt(String(a[4]).replace(/\D/g, '')) * (String(a[4]).includes("overdue") ? -1 : 1);
  const bNum = parseInt(String(b[4]).replace(/\D/g, '')) * (String(b[4]).includes("overdue") ? -1 : 1);
  return aNum - bNum;
});

dv.table(
  ["Person", "Role", "Cadence", "Last Contact", "Status", "Owner"],
  rows.slice(0, 20)
);
```

This shows overdue contacts first (sorted by urgency), then upcoming touchpoints within the next month. Overdue contacts appear the day they're due, not a week later.

### Dashboard view: Relational Suggestions

"You recently talked to X. X is connected to Y. Maybe reach out to Y?"

```dataviewjs
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction")
  .sort(i => i.date, "desc");

const recentDays = 14;
const today = new Date();
const recentInteractions = interactions.where(i => {
  const d = new Date(i.date.toString());
  return (today - d) / 86400000 <= recentDays;
});

// Collect people you've recently interacted with
const recentPeople = new Set();
for (const i of recentInteractions) {
  if (i.participants) {
    for (const p of i.participants) {
      const name = String(p).replace(/\[\[|\]\]/g, '');
      recentPeople.add(name);
    }
  }
}

// For each recent person, find their orgs and other org members
const allPeople = dv.pages('"People/Network"').where(p => p.type === "person");
const suggestions = [];

for (const recentName of recentPeople) {
  const recentPerson = allPeople.find(p => p.file.name === recentName);
  if (!recentPerson || !recentPerson.orgs) continue;

  for (const org of [recentPerson.orgs].flat()) {
    const orgName = String(org).replace(/\[\[|\]\]/g, '');
    // Find other people at the same org who you haven't contacted recently
    const colleagues = allPeople.where(p =>
      p.file.name !== recentName &&
      !recentPeople.has(p.file.name) &&
      p.orgs && [p.orgs].flat().some(o => String(o).includes(orgName))
    );

    for (const colleague of colleagues) {
      // Check if colleague is overdue or coming due
      const collInteractions = interactions.where(i =>
        i.participants && i.participants.some(p =>
          String(p).includes(colleague.file.name)
        )
      );
      const lastContact = collInteractions.length > 0
        ? collInteractions[0].date : null;
      const daysSince = lastContact
        ? Math.floor((today - new Date(lastContact.toString())) / 86400000)
        : 999;

      if (daysSince > 14) {
        suggestions.push([
          colleague.file.link,
          "You spoke with " + recentName + " (" + orgName + ")",
          lastContact ? daysSince + " days ago" : "Never contacted",
          colleague.owner
        ]);
      }
    }
  }
}

// Deduplicate by person name
const seen = new Set();
const unique = suggestions.filter(s => {
  const key = String(s[0]);
  if (seen.has(key)) return false;
  seen.add(key);
  return true;
});

if (unique.length > 0) {
  dv.table(["Suggested Contact", "Connection", "Last Contact", "Owner"],
    unique.slice(0, 10));
} else {
  dv.paragraph("*No relational suggestions right now — your network is well-tended.*");
}
```

This query finds people who share an organization with someone you've recently interacted with, and who you *haven't* contacted recently. It surfaces the "you talked to Nate at Org X, Megan is also at Org X and you haven't spoken in 45 days" connections.

### Dashboard view: Overdue Contacts

Anyone past their cadence due date, starting from day one:

```dataviewjs
const people = dv.pages('"People/Network"')
  .where(p => p.type === "person" && p.cadence && p.cadence !== "as-needed");
const interactions = dv.pages('"Interactions"')
  .where(i => i.type === "interaction");

const cadenceDays = {
  "weekly": 7, "biweekly": 14, "monthly": 30, "quarterly": 90
};

const rows = [];
for (const person of people) {
  const personInteractions = interactions.where(i =>
    i.participants && i.participants.some(p =>
      String(p).includes(person.file.name)
    )
  ).sort(i => i.date, "desc");

  const lastDate = personInteractions.length > 0
    ? personInteractions[0].date : null;
  const days = cadenceDays[person.cadence] || 30;

  if (!lastDate) {
    rows.push([person.file.link, person.cadence, "Never", "—", person.owner]);
    continue;
  }

  const last = new Date(lastDate.toString());
  const dueDate = new Date(last.getTime() + days * 86400000);
  const today = new Date();
  const daysOverdue = Math.floor((today - dueDate) / 86400000);

  if (daysOverdue >= 0) {
    rows.push([person.file.link, person.cadence, lastDate,
      daysOverdue === 0 ? "Due today" : daysOverdue + " days overdue",
      person.owner]);
  }
}

rows.sort((a, b) => {
  const aVal = String(a[3]).includes("Never") ? 999 : parseInt(String(a[3])) || 0;
  const bVal = String(b[3]).includes("Never") ? 999 : parseInt(String(b[3])) || 0;
  return bVal - aVal;
});

if (rows.length > 0) {
  dv.table(["Person", "Cadence", "Last Contact", "Status", "Owner"], rows);
} else {
  dv.paragraph("*All contacts are within their cadence window.*");
}
```

---

## 7. Key Dataview Queries

Beyond cadence tracking, these queries power the dashboards:

### Funding pipeline overview

```dataview
TABLE source-org, contact, connected-via, status, focus-areas
FROM "Funders"
WHERE type = "funder"
SORT status ASC
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

### All projects by status

```dataview
TABLE status, lead, partners, funded-by
FROM "Projects"
SORT choice(status = "active", 1, choice(status = "planning", 2, choice(status = "idea", 3, 4))) ASC
```

### My interactions this month

Replace `August` with your name:

```dataview
TABLE date, participants, context, about
FROM "Interactions"
WHERE contains(logged-by, [[August]])
  AND date >= date(today) - dur(30 days)
SORT date DESC
```

---

## 8. Workflows

### Adding a new contact

1. Create a new note in `People/Network/`. Name it their full name.
2. Templater auto-fills the Person template and prompts for owner.
3. Fill in frontmatter: `role`, `orgs`, `introduced-by`, `cadence`.
4. Fill in the body sections (see template below for prompts).
5. If they belong to an org that already has a note, add them to that org's `members` list.

**Person template body sections:**

```markdown
## Context
Who they are and how we connected:

## Key Contacts
Who they know that matters to us:

## Relationship Notes
Nature of relationship, what they care about, how we can help each other:
```

### Adding a new organization

1. Create a new note in `Organizations/`. Name it the org's official name.
2. Fill in frontmatter: `category`, `members`, `partners-with`, `relevance`.
3. Fill in body sections.

**Organization template body sections:**

```markdown
## About
What this org does and why it matters to our work:

## Key People
Our contacts there and their roles:

## Collaboration History
Past and current ways we've worked together:
```

### Adding a new project

1. Create a new note in `Projects/`. Name it descriptively.
2. Fill in frontmatter: `status` (start with `idea` if early-stage), `lead`, `partners`, `funded-by`.
3. Fill in body sections.

**Project template body sections:**

```markdown
## Description
What this project is and what it aims to accomplish:

## Partners & Stakeholders
Who's involved and what they contribute:

## Status & Next Steps
Current state and upcoming milestones:
```

### Adding a new funder

1. Create a new note in `Funders/`. Name it after the funding source.
2. Fill in frontmatter: `source-org`, `contact`, `connected-via`, `status`, `focus-areas`.
3. Fill in body sections.

**Funder template body sections:**

```markdown
## About
Who they are and what they fund:

## Opportunities
Specific grants, programs, or giving cycles (include amounts and deadlines here):

## Relationship History
How our relationship has developed, key conversations:
```

### Logging an interaction

1. Create a new note in `Interactions/`. Name it `YYYY-MM-DD Brief Title`.
2. Templater auto-fills date, prompts for context type and logged-by.
3. Fill in `participants` (link everyone involved) and `about` (link relevant projects/orgs/funders).
4. Fill in body sections.

**Interaction template body sections:**

```markdown
## Summary
What happened:

## Key Takeaways
What we learned or what shifted:

## Action Items
- [ ]
```

This is the critical daily habit. If you do nothing else, log interactions.

### Before a meeting

1. Search for the person's note. Read their context and relationship notes.
2. Open ExcaliBrain on their note — see their organizational connections, who introduced them, what projects they're linked to.
3. Check the funding pipeline if they're funder-adjacent.

### Weekly review

1. Open your personal dashboard.
2. Scan the Overdue Contacts query — who needs attention today?
3. Review the Upcoming Touchpoints list — who's coming due this week?
4. Glance at Relational Suggestions — any warm introductions to make?
5. Check outstanding action items from recent interactions.
6. Review the funding pipeline for approaching deadlines.
7. Quick sync with your co-user: any new connections, introductions, or status changes?

---

## 9. What This Looks Like in the Graph

**Native Graph View** (filtered by color groups): A constellation where blue People nodes cluster around green Organization nodes. Gold Funder nodes connect through specific people to your orange Project nodes. Dense clusters reveal your strongest community ties; isolated nodes reveal relationships that need development.

**ExcaliBrain** (navigating from a specific entity): Click on a funder and see the org it comes from (parent), your contact person (child), and who connected you (other friend). Click on that contact person and see their other org affiliations (parents) and who they share interactions with. This is how you trace funding triangles and amplification loops — automatically, without hand-drawing anything.

---

## 10. Design Decisions Log

| Decision | Rationale |
|----------|-----------|
| Removed `Idea` entity type | Vault's purpose is contacts and relationships. `Project` with `status: idea` covers early-stage concepts without adding a type, template, and folder. |
| Removed `next-touch` field | Specific dated reminders belong in Google Calendar (push notifications, deadline tracking). Obsidian tracks rhythm via cadence + computed last-contact. |
| Removed `warmth` field | Cadence captures the same intent with less subjectivity. A `weekly` contact is inherently close. |
| Removed `knows` field | Relationships emerge from shared interactions. Manual bidirectional maintenance doesn't scale. |
| Removed `interests` from frontmatter | Inconsistent free-text values break queries. Capture in note body instead. |
| Removed `last-contact` from frontmatter | Dataview computes it from interactions. One less field to maintain. |
| Reframed Funder as relationship, not grant | A funder is an entity with many possible opportunities. Amounts, deadlines, and grant details live in the note body + Google Calendar. |
| Removed `education` and `association` org categories | Don't create useful filter boundaries for this org's work. A university is effectively a nonprofit partner; a coalition is the same. Six categories (nonprofit, business, government, foundation, faith, media) cover the meaningful distinctions. |
| Renamed `owner` to `logged-by` on interactions | Clearer meaning — who recorded this. Powers "my interactions" dashboard filter. |
| Dropped hand-drawn Excalidraw maps | Manual maintenance overhead for a 2-person team. ExcaliBrain + Graph View covers visualization. Can add later. |
| Dropped Relay plugin | Already syncing via git. Two sync layers create conflict risk. |
| 5-7 frontmatter fields per entity | Community consensus: field creep is the #1 CRM abandonment cause. |
| Interactions as the engine | Every logged interaction enriches the graph, updates cadence math, and builds institutional memory. |
