---
tags:
  - meta
---

# How to Use Bases

Bases is Obsidian's built-in database feature. It gives you spreadsheet-style table views of your notes, with editable cells that write back to frontmatter. Think of it as a CRM spreadsheet view of your vault data.

---

## Creating a Base

1. Right-click any folder in the file explorer → "New base"
2. Or: Cmd+P → "Create new base"
3. This creates a `.base.md` file that opens as an interactive table — not a regular markdown page

If you see markdown text instead of a spreadsheet, you're looking at a regular `.md` file, not a Base.

---

## Setting Up a View

When you open a new Base:

1. **Set the source** — click the source/folder picker and choose which folder to pull notes from (e.g., `People/` or `Funders/`)
2. **Add columns** — click the `+` button to add columns. Each column maps to a frontmatter property (type, role, orgs, cadence, owner, etc.)
3. **Sort** — click a column header to sort by it
4. **Filter** — add filters to show only certain notes (e.g., `status` equals `active`)

---

## Suggested Views

**All People**
- Source: `People/`
- Columns: role, orgs, cadence, owner
- Useful for: scanning your whole contact list, checking cadences, seeing who owns what

**Funding Pipeline**
- Source: `Funders/`
- Columns: status, source-org, contact, focus-areas
- Useful for: tracking where each funder relationship stands

**Interaction Log**
- Source: `Interactions/`
- Columns: date, participants, context, about
- Sort by: date descending
- Useful for: reviewing recent conversations, finding interactions with specific people

**Project Tracker**
- Source: `Projects/`
- Columns: status, lead, partners, funded-by
- Useful for: seeing all projects at a glance, tracking which are at idea vs. active stage

---

## Editing from Bases

This is where Bases shines for CRM use: you can edit frontmatter values directly in the table without opening each note.

- Click a cell to edit it
- Changes write back to the note's frontmatter immediately
- For link fields, type `[[` to search for existing notes

This makes batch updates fast — changing the cadence for 10 contacts takes seconds instead of opening 10 files.

---

## Tips

- Bases reads the same frontmatter that Dataview reads. They're not separate databases — both are views into the same underlying data.
- You can create multiple Bases for the same folder with different filters. For example: one view of all People, another showing only `cadence: weekly` contacts.
- Bases views are saved as files. You can put them in `00-Dashboard/` or bookmark them for quick access.
- Bases currently supports table views. For kanban-style pipeline views (drag cards between columns), this may come in a future Obsidian update.
