---
tags:
  - meta
---

# How to Use Graph View

Graph View is Obsidian's built-in visualization of all the links in your vault. Every wikilink between notes becomes a visible connection. Unlike ExcaliBrain (which shows semantic relationships from one note outward), Graph View shows the entire network at once.

---

## Opening Graph View

Cmd+P → "Graph: Open graph view"

Or click the graph icon in the left sidebar.

---

## What You See

A constellation of colored nodes, where each node is a note and each line is a wikilink between notes:

- **Blue** — People
- **Green** — Organizations
- **Gold** — Funders
- **Fuchsia** — Projects
- **Gray** — Interactions (hidden by default to reduce noise)

Dense clusters of connected nodes reveal your strongest community ties. Isolated nodes reveal contacts or orgs you haven't linked to anything yet.

---

## Filtering the Graph

Click the **gear icon** inside the Graph View panel to open settings. The **Filters** section at the top has a search bar.

Useful filters:
- `-path:Interactions` — Hide interaction notes (currently set as default)
- `path:People` — Show only people
- `path:Organizations` — Show only orgs
- `-path:_templates -path:_docs -path:00-Dashboard` — Hide system files
- `tag:#funder` — Show only notes tagged funder

You can combine filters: `path:People OR path:Organizations` shows people and orgs together.

To clear a filter, delete the text from the search bar.

---

## Navigating

- **Click a node** to highlight it and its connections
- **Double-click a node** to open that note
- **Hover over a node** to see its name
- **Scroll to zoom** in and out
- **Drag to pan** the view
- **Drag a node** to move it (it springs back when released)

---

## Local Graph View

For a more focused view, you can open a **local graph** that shows only the connections radiating from a single note:

1. Open any note
2. Cmd+P → "Graph: Open local graph"

This shows just that note and everything directly connected to it — similar to ExcaliBrain but without the semantic grouping. Useful for a quick "who is this person connected to?" check without leaving the note.

---

## Practical Uses

**Weekly review:** Open the full graph and zoom out. Look for:
- Dense clusters (your strongest relationship centers)
- Isolated nodes (contacts that need linking — did you forget to add their org?)
- Bridges (people who connect two otherwise separate clusters — these are your most strategically valuable contacts)

**Spot gaps:** If a funder node (gold) has only one connection, you have a single point of failure in that funding relationship. Look for ways to build additional pathways.

**Discover patterns:** Zoom into a cluster and notice which org sits at the center. That org is likely a key institutional partner worth investing in.

---

## Tips

- Graph View shows *all* wikilinks, not just frontmatter fields. If you mention `[[Marcus Bell]]` in the body of an interaction note, that connection shows up too.
- The colors are set by folder path, not by the `type` frontmatter field. A note in `People/` is blue regardless of its type field.
- If the graph looks like a mess, zoom in on a section or use filters to show only the entity types you care about.
- Graph View works best for the "big picture." For navigating specific relationships, use ExcaliBrain instead.
