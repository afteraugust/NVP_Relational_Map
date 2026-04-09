---
tags:
  - meta
---

# Plugin Setup Guide

## Essential Plugins (Install First)

### 1. Dataview
- **Install:** Settings → Community Plugins → Browse → search "Dataview" → Install → Enable
- **Settings:** Enable JavaScript queries (Settings → Dataview → Enable JavaScript Queries)
- **Why:** Powers all dashboard queries and cadence tracking. Also required by ExcaliBrain.

### 2. Templater
- **Install:** Settings → Community Plugins → Browse → search "Templater" → Install → Enable
- **Settings:**
  - Template folder location: `_templates`
  - Trigger Templater on new file creation: **ON**
  - Folder Templates (Settings → Templater → Folder Templates):
    - `People/` → `_templates/Person Template`
    - `Organizations/` → `_templates/Organization Template`
    - `Projects/` → `_templates/Project Template`
    - `Funders/` → `_templates/Funder Template`
    - `Interactions/` → `_templates/Interaction Template`
- **Why:** Auto-fills templates when you create new notes in the right folders. Prompts you for key fields.

### 3. Excalidraw
- **Install:** Settings → Community Plugins → Browse → search "Excalidraw" → Install → Enable
- **Settings:** Defaults are fine
- **Why:** Required as the rendering engine for ExcaliBrain. You won't use it directly unless you later want hand-drawn strategic maps.

### 4. ExcaliBrain
- **Install:** Settings → Community Plugins → Browse → search "ExcaliBrain" → Install → Enable
- **Settings → Ontology:** Clear all default values from each field, then enter:
  - **Parents:** `orgs, source-org, funded-by`
  - **Children:** `members, contact, participants`
  - **Left-Side Friends:** `partners-with`
  - **Right-Side Friends:** *(clear defaults, leave empty)*
  - **Previous (Friends):** `introduced-by, connected-via`
  - **Next (Friends):** *(clear defaults, leave empty)*
- **Important:** The defaults (advantages, alternatives, cons, Before, After, etc.) are generic examples. Clear them all — they'll create noise in your graph if left in.
- **Why:** Auto-generated semantic relationship graph. Click any entity, see all connections by type.
- **How to use:** Cmd+P → "ExcaliBrain" → opens the graph centered on your current note. Click nodes to navigate. This is your primary tool for seeing the relational web.

## Recommended Plugins

### Homepage
- **Install:** Community Plugins → "Homepage"
- **Settings:** Set homepage to `00-Dashboard/Home` (or your personal dashboard)
- **Why:** Opens your dashboard automatically when you launch Obsidian.

### Calendar
- **Install:** Community Plugins → "Calendar"
- **Settings:** Defaults are fine
- **Why:** Sidebar calendar view. Shows dots on days with Interaction notes. Click a date to see what happened that day.

### Various Complements
- **Install:** Community Plugins → "Various Complements"
- **Settings:** Enable "Internal link complement" and "Front matter complement"
- **Why:** Autocomplete for wikilinks and frontmatter values as you type. Reduces typos and speeds up data entry.

### Tag Wrangler
- **Install:** Community Plugins → "Tag Wrangler"
- **Settings:** Defaults are fine
- **Why:** Right-click any tag in the tag pane to rename it across the entire vault. Safety net for when naming conventions drift.

## Native Feature Configuration

### Graph View
Open Graph View (sidebar or Cmd+P → "Graph View"), then click the settings gear:

**Color Groups** (add these):
- Group 1: `path:People` → Blue
- Group 2: `path:Organizations` → Green
- Group 3: `path:Funders` → Gold/Yellow
- Group 4: `path:Projects` → Fuchsia
- Group 5: `path:Interactions` → Gray

**Filtering:**
- To hide Interactions from the global graph (they add visual noise at scale), type `-path:Interactions` in the Graph View search/filter bar. Toggle the filter on or off as needed.
- Orphan nodes: Show (helps you spot contacts you haven't linked to anything yet)

### Bases
Bases is a core plugin (already enabled). To create a view:
1. Right-click a folder in the file explorer → "New base" — or use the command palette
2. This creates a `.base.md` file that opens as an interactive spreadsheet (not a regular markdown page)
3. Set the source folder and add columns from your frontmatter fields
4. Note: if you see a regular markdown page instead of the spreadsheet UI, you may be looking at one of the archive's old placeholder files, not an actual Base

**Suggested views:**
- **All People:** Source `People/`, columns: role, orgs, cadence, owner
- **Funding Pipeline:** Source `Funders/`, columns: status, source-org, contact, focus-areas
- **Interaction Log:** Source `Interactions/`, columns: date, participants, context, about
- **Project Tracker:** Source `Projects/`, columns: status, lead, partners, funded-by

## Property Types (Critical — Do This Once)

Open Settings → Properties and set the type for each field. This tells Obsidian whether a field contains text, links, dates, or tags. **If link fields are set to "Text," the values won't create connections in the graph or ExcaliBrain.**

**Link** (single link to another note):
`introduced-by`, `source-org`, `lead`, `owner`, `logged-by`

**List of links** (multiple links):
`orgs`, `members`, `partners-with`, `partners`, `funded-by`, `participants`, `about`, `contact`, `connected-via`, `focus-areas`

**Text:**
`type`, `role`, `category`, `cadence`, `status`, `context`

**Date:**
`date`

**Tags:**
`tags`

If you've already entered values as plain text in link fields, you'll need to convert them: switch to source mode (Cmd+E) on each affected note and wrap values in `[[brackets]]`. Then change the property type in Settings → Properties. The old plain-text suggestions will stop appearing once no files contain plain-text values in those fields.

## Plugin Installation Order

If setting up from scratch, install in this order:
1. Dataview (other plugins depend on it)
2. Templater (so you can start creating notes properly)
3. Excalidraw (ExcaliBrain depends on it)
4. ExcaliBrain (configure ontology after install)
5. Homepage, Calendar, Various Complements, Tag Wrangler (in any order)
6. **Then:** Configure property types (see above)
