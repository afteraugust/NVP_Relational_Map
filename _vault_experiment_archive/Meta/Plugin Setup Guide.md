---
tags:
  - meta
---

# Plugin Setup Guide

## Required Plugins

### Core (Install First)
1. **Bases** — Native plugin, enable in Settings → Core plugins
2. **Dataview** — Community plugin. After install, enable JavaScript queries and inline queries in settings.
3. **Templater** — Community plugin. Set template folder to `Templates/` in settings.
4. **Excalidraw** — Community plugin. Default settings are fine.
5. **ExcaliBrain** — Community plugin. Requires Dataview. See ontology configuration below.
6. **Relay** — Community plugin. $1/user/month. See collaboration setup below.

### Supporting
7. **Calendar** — Community plugin. Shows interactions on a calendar view.
8. **Homepage** — Community plugin. Set to open `00-Dashboard/Home` on vault launch.
9. **Tag Wrangler** — Community plugin. Right-click tags to rename across vault.
10. **Various Complements** — Community plugin. Autocomplete for wikilinks and frontmatter values.

## ExcaliBrain Ontology Configuration

In ExcaliBrain settings → Ontology, map these frontmatter fields:

| Relationship Type | Fields |
|------------------|--------|
| **Parent** | `orgs`, `source-org`, `funded-by` |
| **Child** | `members`, `contact`, `participants`, `involved-in` |
| **Friend** | `knows`, `collaborates-with`, `partners-with` |
| **Other Friend** | `introduced-by`, `connected-via`, `proposed-by` |

**Sibling** is auto-inferred (entities sharing a parent).

### Type filtering
In ExcaliBrain settings, configure the `type` frontmatter field as a tag-like filter so you can view the graph filtered to just People, or just Organizations, etc.

## Templater Configuration

1. Settings → Template folder: `Templates/`
2. Enable "Trigger Templater on new file creation"
3. Set folder templates:
   - `People/` → `Templates/Person Template`
   - `Organizations/` → `Templates/Organization Template`
   - `Projects/` → `Templates/Project Template`
   - `Funders/` → `Templates/Funder Template`
   - `Ideas/` → `Templates/Idea Template`
   - `Interactions/` → `Templates/Interaction Template`

## Relay Collaboration Setup

1. Install Relay plugin
2. One team member creates a Relay Server
3. Share the server with the other two team members
4. Designate the entire vault (or specific folders) for sync
5. **Important:** Also set up Git as a backup layer (see Workflow Conventions)

## Bases Configuration

Bases views are configured directly in Obsidian, not in markdown files. The files in `Bases/` provide specifications for each view. To create:

1. Command palette → "Bases: Create new base"
2. Set the folder source and configure columns per the spec in each Bases file
3. Bases writes back to frontmatter — so editing a warmth value in the table updates the person's file directly
