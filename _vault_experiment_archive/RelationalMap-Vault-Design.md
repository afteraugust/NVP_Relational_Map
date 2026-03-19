# Relational Map — Obsidian Vault Design Document

## 1. Purpose & Philosophy

This vault is a **shared relational intelligence system** for a small nonprofit team (3 people) running a local-first community-building cohort. It serves three functions simultaneously:

1. **CRM** — track every person and organization in your network, their connections, warmth, and interaction history
2. **Strategic map** — visualize relationship pathways between people, funding, partnerships, and programs
3. **Institutional memory** — ensure that no relationship lives only in one person's head

The core insight driving the architecture: in a community-building org, **every entity type is connected to every other**. A person leads to an org, which leads to funding, which enables a project, which attracts more people. The vault must make these pathways *visible and navigable*.

---

## 2. Entity Types & Relational Ontology

### 2.1 Entity Types

| Type | What it represents | Why it matters |
|------|-------------------|----------------|
| **Person** | Any individual in your network | The atomic unit — all relationships flow through people |
| **Organization** | Businesses, nonprofits, government bodies, faith communities, etc. | People cluster around orgs; orgs are where funding, partnership, and influence live |
| **Project** | Your internal programs and initiatives | Projects are what you *do* — they consume resources and produce relationships |
| **Funder** | Specific funding opportunities (grants, sponsors, donors) | Distinct from orgs because one org may have multiple funding streams |
| **Idea** | Potential collaborations, programs, partnerships not yet in motion | The pipeline — ideas become projects when activated |
| **Interaction** | A dated record of a meeting, call, email, or encounter | The connective tissue — interactions are how relationships develop |

### 2.2 Relationship Types

These are the named connections between entities. Each becomes a frontmatter field using Dataview inline syntax.

**Person ↔ Person:**
- `knows` — general connection
- `introduced-by` — how you met them (critical for pathway mapping)
- `collaborates-with` — active working relationship

**Person ↔ Organization:**
- `orgs` — belongs to, works at, member of
- `role-at` — their function (board member, staff, volunteer, patron)

**Person ↔ Project:**
- `involved-in` — participates in one of your projects
- `champion` — external advocate for a project

**Organization ↔ Organization:**
- `partners-with` — formal or informal partnership
- `funds` — one org funds another (for org-level funding relationships)

**Funder → Person / Organization:**
- `contact` — who is your point of contact at this funder
- `source-org` — which organization houses this funding opportunity
- `connected-via` — who in your network connects you to this funder

**Idea → Everything:**
- `proposed-by` — who surfaced this idea
- `relevant-to` — which projects, orgs, or people it involves
- `potential-partners` — who could make this happen

**Interaction → People:**
- `participants` — who was in the conversation
- `about` — which projects, orgs, or ideas were discussed

### 2.3 The Relational Flywheel

Here's the core dynamic this vault makes visible:

```
People ──know──→ People ──work at──→ Organizations
  ↑                                        │
  │                                   fund / sponsor
  │                                        ↓
Projects ←──enable──── Funders ←──housed at── Organizations
  │                                        ↑
  │                                        │
  └──attract──→ More People ──know──→ More People
```

Every new person added to the network potentially unlocks: a new org connection, a funding pathway, a partnership idea, or a project champion. The vault surfaces these latent connections.

---

## 3. Frontmatter Schema

### 3.1 Person

```yaml
---
type: person
role:
  - contact          # default for new network additions
  # also: team | funder-contact | elected-official | faith-leader |
  #       business-owner | educator | media | community-leader
orgs:
  - "[[Org Name]]"
introduced-by: "[[Person Name]]"
knows:
  - "[[Person Name]]"
warmth: warm         # hot | warm | cool | cold | new
owner: "[[August]]"  # which team member owns this relationship
last-contact: 2026-02-15
next-followup: 2026-03-01
interests:
  - community-development
  - small-business
  # also: housing | youth | civic-engagement | faith | health |
  #       education | arts | environment | workforce
tags:
  - person
  - contact
---
```

### 3.2 Organization

```yaml
---
type: organization
category: nonprofit    # business | nonprofit | government | foundation |
                       # faith | education | media | association
members:
  - "[[Person Name]]"
partners-with:
  - "[[Org Name]]"
location: Riverside    # neighborhood or area
relevance:
  - marketing          # marketing | funding | programming | political | volunteers
size: medium           # small | medium | large (relative to your context)
tags:
  - organization
---
```

### 3.3 Project

```yaml
---
type: project
status: active         # idea | planning | active | paused | completed
lead: "[[Team Member]]"
partners:
  - "[[Org Name]]"
  - "[[Person Name]]"
funded-by:
  - "[[Funder Name]]"
goal:
  - marketing          # marketing | funding | programming | community-growth
timeline: 2026-Q1
tags:
  - project
---
```

### 3.4 Funder

```yaml
---
type: funder
category: foundation   # foundation | corporate | government | individual
source-org: "[[Org Name]]"
contact: "[[Person Name]]"
connected-via: "[[Person Name]]"
focus-areas:
  - community-development
  - civic-engagement
status: cultivating    # prospect | cultivating | applied | funded | declined
amount-range: medium   # micro (<$1K) | small ($1-10K) | medium ($10-50K) |
                       # large ($50-100K) | major ($100K+)
deadline: 2026-06-01
tags:
  - funder
---
```

### 3.5 Idea

```yaml
---
type: idea
status: exploring      # seed | exploring | proposed | in-progress | shelved
proposed-by: "[[Person Name]]"
relevant-to:
  - "[[Project Name]]"
  - "[[Org Name]]"
potential-partners:
  - "[[Org Name]]"
  - "[[Person Name]]"
category: partnership  # partnership | program | event | campaign | research
tags:
  - idea
---
```

### 3.6 Interaction

```yaml
---
type: interaction
date: 2026-02-15
participants:
  - "[[Person Name]]"
  - "[[Team Member]]"
context: coffee-meeting  # coffee-meeting | event | call | email | text |
                         # group-meeting | site-visit
about:
  - "[[Project Name]]"
  - "[[Idea Name]]"
owner: "[[Team Member]]"
followup: true
tags:
  - interaction
---
```

---

## 4. Folder Structure

```
RelationalMap/
├── 00-Dashboard/
│   ├── Home.md                    # Landing page with embeds and links
│   ├── August-Dashboard.md        # Personal dashboard (Bases views)
│   ├── Maria-Dashboard.md
│   └── David-Dashboard.md
├── People/
│   ├── Team/
│   │   ├── August.md
│   │   ├── Maria Chen.md
│   │   └── David Torres.md
│   └── Network/
│       ├── Rev. James Whitfield.md
│       ├── Priya Sharma.md
│       ├── Councilwoman Diane Okafor.md
│       └── ... (all contacts)
├── Organizations/
│   ├── Grace Community Church.md
│   ├── Riverside Community Foundation.md
│   └── ...
├── Projects/
│   ├── Spring Block Party Series.md
│   ├── Neighborhood Leadership Academy.md
│   └── ...
├── Funders/
│   ├── RCF Community Building Grant.md
│   ├── United Way Neighborhood Initiative.md
│   └── ...
├── Ideas/
│   ├── University Service-Learning Partnership.md
│   ├── Health and Community Fair.md
│   └── ...
├── Interactions/
│   ├── 2026-01-10 Coffee with Priya Sharma.md
│   ├── 2026-01-22 Foundation Meeting - Marcus Bell.md
│   └── ...
├── Maps/
│   ├── Funding Pathways.excalidraw.md
│   ├── Event Partner Ecosystem.excalidraw.md
│   ├── Network Clusters.excalidraw.md
│   └── Strategic Overview.excalidraw.md
├── Bases/
│   ├── All People.base.md
│   ├── All Organizations.base.md
│   ├── Funding Pipeline.base.md
│   ├── Interaction Log.base.md
│   └── Ideas Pipeline.base.md
├── Templates/
│   ├── Person Template.md
│   ├── Organization Template.md
│   ├── Project Template.md
│   ├── Funder Template.md
│   ├── Idea Template.md
│   └── Interaction Template.md
└── Meta/
    ├── Plugin Setup Guide.md
    ├── Workflow Conventions.md
    └── Tagging Taxonomy.md
```

### Folder Rationale

- **00-Dashboard/** prefixed with `00` to sort first. Each team member gets a personalized view.
- **People/** split into Team and Network to keep your 3 internal files separate from the growing contact list.
- **Interactions/** dated by convention (`YYYY-MM-DD Title`) so they sort chronologically. These are the most frequently created files.
- **Maps/** contains Excalidraw drawings — your hand-curated strategic visualizations.
- **Bases/** contains Bases view definitions. These act as your "database views" into the vault.
- **Templates/** Templater templates for consistent data entry.
- **Meta/** documentation for the team on how to use the vault.

---

## 5. Plugin Stack

### 5.1 Core Plugins (Essential)

| Plugin | Role in this system |
|--------|-------------------|
| **Bases** (native) | Primary dashboard layer. Editable table/gallery views of People, Orgs, Funders, Ideas. Write-back to frontmatter means Maria can update `warmth` or `last-contact` from a spreadsheet view without opening each file. |
| **Dataview** | Complex computed queries that Bases can't handle: "all people connected to someone at a foundation," "interaction count by person in last 30 days," aggregated pathway analysis. Powers ExcaliBrain. |
| **Templater** | Standardized entry templates. When Maria creates a new Person, the template auto-populates `type: person`, `owner:`, today's date, and prompts for key fields. |
| **Excalidraw** | Hand-drawn strategic maps. Funding pathway diagrams, event partnership ecosystems, cluster visualizations. Each node is a wikilink back to a vault file. |
| **ExcaliBrain** | Auto-generated relationship graph. Click any person and see all their connections radiating outward — orgs, other people they know, projects they're involved in, funders they connect to. Uses your frontmatter fields as relationship definitions. |
| **Relay** | Real-time collaboration. All three team members edit the same vault with CRDT conflict resolution. |

### 5.2 ExcaliBrain Ontology Mapping

ExcaliBrain collapses all relationships into 5 visual types. Here's how we map our schema:

| ExcaliBrain Type | Our Fields | Visual Meaning |
|-----------------|------------|----------------|
| **Parent** | `orgs`, `source-org`, `funded-by` | "belongs to" / "comes from" — the entity this one is housed within |
| **Child** | `members`, `contact`, `participants`, `involved-in` | "contains" / "includes" — entities housed within this one |
| **Friend** | `knows`, `collaborates-with`, `partners-with` | Mutual/peer relationships — the lateral network |
| **Other Friend** | `introduced-by`, `connected-via`, `proposed-by` | Directional relationships — how pathways were formed |
| **Sibling** | (auto-inferred) | Entities sharing a parent — e.g., two people at the same org |

This mapping means: navigating to "Riverside Community Foundation" in ExcaliBrain shows Marcus Bell as a child (he's a member), your grant as a child (it's funded-by this org), and partner orgs as friends. You immediately see the full picture.

### 5.3 Supporting Plugins

| Plugin | Role |
|--------|------|
| **Calendar** | Timeline view of Interactions. Click a date, see what conversations happened. |
| **Homepage** | Auto-opens your personal dashboard on vault launch. |
| **Tag Wrangler** | Manage and rename tags across the vault without manual find-replace. |
| **Various Complements** | Autocomplete for wikilinks and frontmatter values — speeds up data entry significantly. |

---

## 6. Test Data — The Riverside Neighbors Network

### 6.1 Team

| Name | Role | Focus |
|------|------|-------|
| **August** | Strategy & Partnerships | External relationships, funding pipeline, strategic partnerships |
| **Maria Chen** | Community Manager | Day-to-day contact management, event coordination, community intake |
| **David Torres** | Executive Director | High-level relationships, board/funder meetings, organizational direction |

### 6.2 People (Network)

| # | Name | Role | Org(s) | Introduced By | Knows | Warmth | Owner | Interests |
|---|------|------|--------|---------------|-------|--------|-------|-----------|
| 1 | Rev. James Whitfield | Faith leader | Grace Community Church | (original) | Fatima Al-Rashid, Carlos Mendez | hot | David | community-development, youth, faith |
| 2 | Priya Sharma | Business owner | Sharma's Kitchen, Main Street Alliance | (event) | Carlos Mendez, Rachel Torres | warm | August | small-business, community-development |
| 3 | Councilwoman Diane Okafor | Elected official | City Council Ward 4 | David Torres | Marcus Bell, Angela Reeves | hot | David | civic-engagement, housing, community-development |
| 4 | Marcus Bell | Funder contact | Riverside Community Foundation | Diane Okafor | Lisa Drummond, Keisha Washington | warm | David | community-development, civic-engagement |
| 5 | Prof. Sarah Lin | Educator | State University Urban Studies | (inbound - read Gazette article) | Tom Nguyen | warm | August | civic-engagement, education, youth |
| 6 | Tom Nguyen | Media | Riverside Gazette | Maria Chen | Sarah Lin, Rachel Torres | warm | Maria | community-development, arts |
| 7 | Angela Reeves | Government | Riverside Housing Authority | Diane Okafor | Diane Okafor, Keisha Washington | cool | David | housing, community-development |
| 8 | Carlos Mendez | Business owner, board member | Mendez Hardware, Main Street Alliance | Priya Sharma | Priya Sharma, Rev. Whitfield | warm | August | small-business, community-development |
| 9 | Fatima Al-Rashid | Youth program director | Riverside YMCA | Rev. Whitfield | Rev. Whitfield, Maria Chen | hot | Maria | youth, community-development, health |
| 10 | Officer Kevin Park | Community liaison | Riverside PD | (community meeting) | Angela Reeves | cool | Maria | civic-engagement, community-development |
| 11 | Lisa Drummond | Grant writer (consultant) | Independent | Marcus Bell | Marcus Bell, Keisha Washington | warm | David | community-development, civic-engagement |
| 12 | James Okafor | Real estate developer | Okafor Development Group | Diane Okafor | Diane Okafor | cool | August | housing, small-business |
| 13 | Rachel Torres | Community influencer | (none — runs neighborhood FB group) | Maria Chen | Priya Sharma, Tom Nguyen | hot | Maria | community-development, arts, civic-engagement |
| 14 | Dr. Michael Chen | Physician, board member | Riverside Health Clinic | Fatima Al-Rashid | Fatima Al-Rashid | cool | Maria | health, community-development |
| 15 | Keisha Washington | Regional director | United Way Riverside | (David's prior org) | David Torres, Marcus Bell, Lisa Drummond | warm | David | community-development, civic-engagement, youth |

### 6.3 Organizations

| Name | Category | Key Members | Partners | Relevance |
|------|----------|-------------|----------|-----------|
| Grace Community Church | faith | Rev. Whitfield | Riverside YMCA | programming, volunteers, marketing |
| Sharma's Kitchen | business | Priya Sharma | Main Street Alliance | marketing, event-catering |
| City Council Ward 4 | government | Diane Okafor | Riverside Housing Authority | political, funding |
| Riverside Community Foundation | foundation | Marcus Bell | United Way Riverside | funding |
| State University Urban Studies | education | Prof. Sarah Lin | (potential) | volunteers, research |
| Riverside Gazette | media | Tom Nguyen | — | marketing |
| Riverside Housing Authority | government | Angela Reeves | City Council Ward 4 | programming, political |
| Main Street Alliance (Riverside) | association | Carlos Mendez, Priya Sharma | — | marketing, sponsorship |
| Riverside YMCA | nonprofit | Fatima Al-Rashid | Grace Community Church | programming, space |
| United Way Riverside | foundation | Keisha Washington | Riverside Community Foundation | funding |
| Riverside Health Clinic | business | Dr. Michael Chen | — | programming, health |
| Okafor Development Group | business | James Okafor | — | funding-potential |

### 6.4 Projects

| Name | Status | Lead | Partners | Funded By | Goal |
|------|--------|------|----------|-----------|------|
| Spring Block Party Series | active | Maria Chen | Sharma's Kitchen, Grace Community Church, Main Street Alliance | (seeking sponsors) | marketing, community-growth |
| Neighborhood Leadership Academy | planning | August | Riverside YMCA, State University (potential) | RCF Community Building Grant (applied) | programming, community-growth |
| Small Business Partnership Program | idea | August | Main Street Alliance | (unfunded) | marketing, partnerships |
| "Know Your Neighbor" Survey | active | Maria Chen | (team only) | (internal) | programming, community-growth |

### 6.5 Funders

| Name | Source Org | Contact | Connected Via | Status | Amount Range | Focus |
|------|-----------|---------|---------------|--------|-------------|-------|
| RCF Community Building Grant | Riverside Community Foundation | Marcus Bell | Diane Okafor | applied | medium ($25-50K) | community-development |
| United Way Neighborhood Initiative | United Way Riverside | Keisha Washington | (David direct) | cultivating | large ($50-100K) | community-development, civic-engagement |
| National Civic League All-America City | National Civic League | (none yet) | (research) | prospect | small ($10K + prestige) | civic-engagement |
| Main Street Alliance Event Sponsors | Main Street Alliance | Carlos Mendez | Priya Sharma | cultivating | micro ($1-5K each) | small-business, community-development |

### 6.6 Ideas

| Name | Status | Proposed By | Relevant To | Potential Partners | Category |
|------|--------|-------------|-------------|-------------------|----------|
| University Service-Learning Partnership | exploring | Sarah Lin | Neighborhood Leadership Academy | State University Urban Studies | research, programming |
| Health & Community Fair | seed | Fatima Al-Rashid | Spring Block Party Series | Riverside YMCA, Riverside Health Clinic | event |
| Gazette Storytelling Series | exploring | August | (marketing broadly) | Riverside Gazette, Tom Nguyen | campaign |
| Interfaith Neighborhood Dinners | seed | Rev. Whitfield | Know Your Neighbor Survey | Grace Community Church | program |

### 6.7 Sample Interactions

| Date | Title | Participants | Context | About | Owner | Followup |
|------|-------|-------------|---------|-------|-------|----------|
| 2026-01-10 | Coffee with Priya Sharma | August, Priya Sharma | coffee-meeting | Small Business Partnership Program | August | Priya will introduce Carlos |
| 2026-01-15 | Ward 4 Monthly | David Torres, Diane Okafor | group-meeting | Neighborhood Leadership Academy, housing | David | Diane connecting David to Marcus |
| 2026-01-22 | Foundation Meeting | David Torres, Marcus Bell | coffee-meeting | RCF Community Building Grant | David | Submit application by Feb 15 |
| 2026-01-28 | YMCA Program Check-in | Maria Chen, Fatima Al-Rashid | call | Spring Block Party Series, Health & Community Fair idea | Maria | Fatima to propose fair concept |
| 2026-02-03 | Gazette Interview | Maria Chen, Tom Nguyen | coffee-meeting | Know Your Neighbor Survey, Gazette Storytelling Series | Maria | Tom pitching series to editor |
| 2026-02-07 | Merchant Mixer | August, Priya Sharma, Carlos Mendez | event | Main Street Alliance Event Sponsors, Small Business Partnership | August | Carlos open to block party sponsorship |
| 2026-02-10 | United Way Intro | David Torres, Keisha Washington | call | United Way Neighborhood Initiative | David | Keisha sending RFP details |
| 2026-02-14 | Sarah Lin Outreach | August, Sarah Lin | email | University Service-Learning Partnership | August | Sarah checking with department chair |

---

## 7. Relationship Pathways — What the Map Reveals

The test data above contains several **non-obvious strategic pathways** that only become visible when the network is mapped:

### Pathway 1: The Funding Triangle
```
Diane Okafor ──introduced──→ Marcus Bell (RCF)
                                    │
                              recommended
                                    ↓
David Torres ──prior org──→ Keisha Washington (United Way)
                                    │
                              both know
                                    ↓
                            Lisa Drummond (grant writer)
```
**Insight:** Lisa Drummond is connected to *both* your major funding contacts. She's not just a grant writer — she's a credibility bridge between your two biggest funding prospects.

### Pathway 2: The Marketing Amplification Loop
```
Maria ──runs events──→ Block Party Series
                            │
                       catered by
                            ↓
                    Priya (Sharma's Kitchen)
                            │
                     member of
                            ↓
                    Main Street Alliance
                            │
                    40 merchants who could
                            ↓
                    sponsor + attend + promote
                            │
                    covered by
                            ↓
                    Tom Nguyen (Gazette)
                            │
                    article read by
                            ↓
                    Sarah Lin → volunteers
```
**Insight:** Each block party isn't just an event — it's a flywheel that feeds media coverage, merchant relationships, volunteer pipelines, and sponsorship cultivation simultaneously.

### Pathway 3: The Youth Programming Cluster
```
Rev. Whitfield ←──both serve on advisory board──→ Fatima Al-Rashid
       │                                                │
  Grace Church (space, congregation)            YMCA (programs, space)
       │                                                │
       └──────── natural partnership for ───────────────┘
                 Interfaith Dinners idea
                 Health & Community Fair
                 Leadership Academy venue
```
**Insight:** The Whitfield-Fatima connection unlocks *three* different collaboration possibilities. Their shared advisory board seat means they already have a working relationship.

### Pathway 4: The Political-Funding Bridge
```
Diane Okafor (Council) ──married to──→ James Okafor (developer)
       │                                       │
  controls discretionary funds          interested in community investment
       │                                       │
  connected to Angela Reeves            potential sponsor / in-kind
  (Housing Authority)                   for physical space needs
```
**Insight:** James Okafor is currently `cool` warmth and seems peripheral. But he's a potential physical-space sponsor (developer = buildings) *and* his wife controls city discretionary funding. Worth warming up.

---

## 8. Bases View Definitions

### 8.1 All People (CRM View)
- **Source:** `People/` folder, filter `type: person`
- **Columns:** Name, warmth, owner, orgs (linked), last-contact, next-followup, interests
- **Sort:** next-followup ascending (who needs attention soonest)
- **Filter presets:** By owner (August / Maria / David), by warmth, by interest area

### 8.2 Funding Pipeline
- **Source:** `Funders/` folder, filter `type: funder`
- **Columns:** Name, source-org, contact, status, amount-range, deadline, connected-via
- **Sort:** deadline ascending
- **Color coding:** status (prospect=gray, cultivating=yellow, applied=blue, funded=green, declined=red)

### 8.3 Interaction Log
- **Source:** `Interactions/` folder, filter `type: interaction`
- **Columns:** date, title (file name), participants, context, about, followup
- **Sort:** date descending
- **Filter:** By owner, by date range, by followup=true (outstanding action items)

### 8.4 Ideas Pipeline
- **Source:** `Ideas/` folder, filter `type: idea`
- **Columns:** Name, status, proposed-by, potential-partners, category
- **Sort:** status (exploring first, then seed, then proposed)

### 8.5 Organization Directory
- **Source:** `Organizations/` folder, filter `type: organization`
- **Columns:** Name, category, members, relevance, partners-with
- **Group by:** category

---

## 9. Excalidraw Map Conventions

### 9.1 Map Types

Create these as standing maps in the `Maps/` folder. They are *hand-curated* — you update them deliberately as the strategic picture evolves.

**Funding Pathways** — Shows all funders, the people who connect you to them, and the org relationships that provide credibility. Updated when funding status changes.

**Event Partner Ecosystem** — For each major event/project, who provides what (venue, catering, promotion, volunteers, funding). Updated during project planning.

**Network Clusters** — People grouped into boxes by social/professional circle: civic leadership, faith community, business community, education, media. Shows bridge people who span multiple clusters.

**Strategic Overview** — The big picture. Your org at center, major partnership pathways radiating outward, color-coded by type (funding=green, marketing=blue, programming=orange, political=purple).

### 9.2 Visual Conventions

- **People nodes:** Circles with `[[wikilinks]]` — clicking navigates to their file
- **Org nodes:** Rounded rectangles
- **Project nodes:** Rectangles with thick borders
- **Funder nodes:** Diamond shapes (or rectangles with green fill)
- **Grouping boxes:** Large dashed rectangles labeled with cluster name
- **Line colors:** Green=funding pathway, Blue=marketing/visibility, Orange=programming, Purple=political/influence, Gray=informal connection
- **Warmth indicators:** Hot=red border, Warm=orange, Cool=gray, Cold=dashed

---

## 10. User Workflows

### 10.1 August — Strategy & Partnerships

**Daily:** Open personal dashboard → scan "my contacts needing followup" (Bases filtered to owner=August, sorted by next-followup) → check Ideas pipeline for anything moving.

**After a meeting:** Create Interaction note from template → link participants and topics discussed → update warmth on relevant Person files → if new connection discovered, add `knows` links to both people.

**Strategic planning:** Open Funding Pathways excalidraw map → trace connection chains → identify weakest links → assign cultivation tasks.

**Exploring a new partnership:** Search vault for org or interest area → open ExcaliBrain on relevant person → visually walk the graph outward → identify who bridges you to the target.

### 10.2 Maria Chen — Community Manager

**Daily:** Open personal dashboard → scan "recent interactions needing followup" (Bases filtered to followup=true) → check "contacts not touched in 30 days" query.

**After an event:** Batch-create Person notes for new contacts using Templater → link introduced-by fields → create Interaction notes for significant conversations → update `last-contact` on existing contacts who attended.

**Community intake:** New person template → fill frontmatter → write brief context note in body → link to any orgs or people they mentioned → update the "Know Your Neighbor" project note if relevant.

**Content/marketing:** Open Network Clusters map → identify good storytelling candidates (bridge people, interesting connections) → pitch to Tom Nguyen for Gazette series.

### 10.3 David Torres — Executive Director

**Before a meeting:** Search for the person/org → read their file + linked Interaction notes → open ExcaliBrain to see their network position → identify talking points and asks.

**Board/funder meetings:** Review Funding Pipeline Bases view → check deadlines and statuses → read recent Interactions with each funder contact → update funder status after meetings.

**Strategic oversight:** Open Strategic Overview excalidraw map → review overall network health → identify clusters that are underdeveloped → assign Maria or August to cultivate.

**New opportunity assessment:** When someone proposes a partnership: create Idea note → link all relevant entities → open ExcaliBrain to see how the idea connects to existing network → assess feasibility based on relationship warmth along the pathway.

---

## 11. Dataview Queries (For Where Bases Falls Short)

### Contacts needing attention
```dataview
TABLE warmth, owner, last-contact, next-followup
FROM "People/Network"
WHERE next-followup <= date(today) + dur(3 days)
SORT next-followup ASC
```

### Connection pathway query — "Who connects us to [org]?"
```dataview
TABLE orgs, knows, introduced-by
FROM "People/Network"
WHERE contains(orgs, [[Target Org]])
   OR contains(knows, [[Target Person]])
```

### Interaction frequency by person (last 90 days)
```dataview
TABLE length(rows) AS "Interactions"
FROM "Interactions"
WHERE date >= date(today) - dur(90 days)
FLATTEN participants AS person
GROUP BY person
SORT length(rows) DESC
```

### Unfollowed interactions
```dataview
TABLE date, participants, about
FROM "Interactions"
WHERE followup = true
SORT date ASC
```

### People by shared interest (find unexpected connections)
```dataview
TABLE orgs, warmth, owner
FROM "People/Network"
WHERE contains(interests, "civic-engagement")
  AND contains(interests, "small-business")
```

### Network gaps — orgs with no warm contacts
```dataview
TABLE category, members, relevance
FROM "Organizations"
WHERE !any(members, (m) => contains(m.warmth, "hot") OR contains(m.warmth, "warm"))
```

---

## 12. Templater Templates

### Person Template
```markdown
---
type: person
role:
  - contact
orgs:
  - "[[]]"
introduced-by: "[[]]"
knows:
  - "[[]]"
warmth: new
owner: "[[<% tp.system.suggester(["August", "Maria Chen", "David Torres"], ["August", "Maria Chen", "David Torres"]) %>]]"
last-contact: <% tp.date.now("YYYY-MM-DD") %>
next-followup: <% tp.date.now("YYYY-MM-DD", 14) %>
interests:
  -
tags:
  - person
  - contact
---

## Context
*How we met / why they matter:*


## Notes


## Key Relationships
```

### Interaction Template
```markdown
---
type: interaction
date: <% tp.date.now("YYYY-MM-DD") %>
participants:
  - "[[<% tp.system.suggester(["August", "Maria Chen", "David Torres"], ["August", "Maria Chen", "David Torres"]) %>]]"
  - "[[]]"
context: <% tp.system.suggester(["coffee-meeting", "event", "call", "email", "text", "group-meeting", "site-visit"], ["coffee-meeting", "event", "call", "email", "text", "group-meeting", "site-visit"]) %>
about:
  - "[[]]"
owner: "[[<% tp.system.suggester(["August", "Maria Chen", "David Torres"], ["August", "Maria Chen", "David Torres"]) %>]]"
followup: false
tags:
  - interaction
---

## Summary


## Key Takeaways


## Action Items
- [ ]
```

---

## 13. Git Backup Strategy (Safety Net for Relay)

Even with Relay handling live collaboration, maintain a Git backup:

```
# .gitignore
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.trash/
```

One team member (suggest August) runs a daily auto-commit or uses the Obsidian Git plugin set to commit every 30 minutes. This provides:
- Full version history of every file
- Recovery from any Relay sync issues
- Ability to see *who changed what and when* beyond Relay's scope
- Disaster recovery if the vault is ever corrupted

---

## 14. Implementation Order

1. **Create folder structure** and Meta documentation
2. **Install plugins:** Dataview, Templater, Excalidraw, ExcaliBrain, Bases, Calendar, Homepage, Relay
3. **Configure ExcaliBrain ontology** mapping (Section 5.2)
4. **Create Templater templates** (Section 12)
5. **Enter team member files** (People/Team/)
6. **Enter test data** — all People, Orgs, Projects, Funders, Ideas from Section 6
7. **Create Bases views** (Section 8)
8. **Build Excalidraw maps** — start with Strategic Overview and Network Clusters
9. **Set up personal dashboards** with embedded Bases views and Dataview queries
10. **Configure Relay** for team collaboration
11. **Set up Git backup**
12. **Write Workflow Conventions** doc for team onboarding
