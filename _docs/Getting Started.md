---
tags:
  - meta
---

# Getting Started — New User Onboarding

Welcome to the NVP Relational Map. This guide walks you through everything you need to get up and running, from first launch to your first logged interaction.

---

## Step 1: Open the Vault

If you're the second user joining this vault, follow the setup instructions in the [[User Guide - Multi-User Vault]] to clone the repository and open it in Obsidian.

If the vault is already on your machine, open Obsidian → "Open folder as vault" → select the `NVP_Relational_Map` folder.

---

## Step 2: Verify Plugins Are Installed

Open Settings (Cmd+,) → Community Plugins. You should see these plugins listed and enabled:

- Dataview
- Templater
- Excalidraw
- ExcaliBrain
- Homepage
- Calendar
- Various Complements
- Tag Wrangler

If any are missing, see [[Plugin Setup]] for installation instructions. Install them in order — Dataview first, then Templater, then Excalidraw, then ExcaliBrain. The rest can go in any order.

---

## Step 3: Set Up Property Types

This is a one-time configuration that ensures frontmatter fields work correctly across the vault. Open Settings → Properties. For each field listed below, set its type so Obsidian knows how to handle it:

**Link fields** (single link to another note):
- `introduced-by` → Link
- `source-org` → Link
- `lead` → Link
- `owner` → Link
- `logged-by` → Link

**List of links** (multiple links to other notes):
- `orgs` → List of links
- `members` → List of links
- `partners-with` → List of links
- `partners` → List of links
- `funded-by` → List of links
- `participants` → List of links
- `about` → List of links
- `contact` → List of links
- `connected-via` → List of links
- `focus-areas` → List

**Text fields:**
- `type` → Text
- `role` → Text
- `category` → Text
- `cadence` → Text
- `status` → Text
- `context` → Text

**Date fields:**
- `date` → Date

**Tags:**
- `tags` → Tags

**Why this matters:** If a field is set to "Text" instead of "Link," the values won't create connections in the graph or in ExcaliBrain. Getting this right up front prevents the issue of entering plain text where links are expected.

---

## Step 4: Learn the Folder Structure

```
00-Dashboard/     → Your home base. Start here every day.
People/Team/      → Your internal team (August, Savannah)
People/Network/   → Everyone in your network
Organizations/    → Businesses, nonprofits, agencies, churches, etc.
Projects/         → Your programs and initiatives (including early ideas)
Funders/          → Funding sources and relationships
Interactions/     → Dated records of meetings, calls, emails, events
Templates/        → Don't edit these directly — they auto-fill new notes
Meta/             → Documentation (you're reading one now)
```

---

## Step 5: Open Your Dashboard

Navigate to `00-Dashboard/` and open your personal dashboard (`August Dashboard` or `Savannah Dashboard`). This is your daily starting point. It shows:

- **Overdue contacts** — people past their cadence window
- **Upcoming touchpoints** — who's coming due in the next 30 days
- **Your recent interactions** — what you've logged lately

The **Home** dashboard shows the same views for the whole team, plus the funding pipeline, relational suggestions, and active projects.

---

## Step 6: Create Your First Contact

1. Navigate to `People/Network/` in the file explorer
2. Right-click → New note
3. Name it with the person's full name (e.g., `Marcus Bell`)
4. Templater will auto-fill the Person template and prompt you for role, cadence, and owner
5. Fill in the frontmatter fields — at minimum: `role`, `orgs`, and `cadence`
6. Write a sentence or two in the **Context** section about how you know them

When entering `orgs`, type `[[` and start typing the org name. If the org doesn't have a note yet, that's fine — the link will show as unresolved (you can create the org note later).

---

## Step 7: Log Your First Interaction

1. Navigate to `Interactions/`
2. Create a new note named `YYYY-MM-DD Brief Title` (e.g., `2026-03-24 Call with Marcus Bell`)
3. Templater will prompt you for context type (call, email, meeting, etc.) and who logged it
4. Fill in `participants` — link everyone who was part of the conversation, including yourself
5. Fill in `about` — link any projects, orgs, or funders that came up
6. Write a brief summary and any action items

This is the most important habit. Every interaction you log feeds the cadence tracker, builds the graph, and creates institutional memory.

---

## Step 8: Explore the Graph

Try two things:

**Graph View** (Cmd+P → "Graph: Open graph view"): See the whole network. Blue nodes are people, green are orgs, gold are funders, fuchsia are projects. Click any node to navigate to it. The graph filter is set to hide Interactions — toggle this with the filter bar inside the Graph View settings (gear icon).

**ExcaliBrain** (Cmd+P → "ExcaliBrain"): Opens a semantic map centered on your current note. Parents (orgs) appear above, members appear below, partners appear to the left, and introduction pathways appear further left. Click any node to re-center the map on it. This is your tool for exploring "who connects us to what."

---

## Step 9: Sync Your Work

This vault syncs between users via Git. The obsidian-git plugin handles this automatically, but build one habit:

**When you're done editing, hit Cmd+P → "Git: Commit-and-sync" before switching away from Obsidian.** This pushes your changes and pulls any changes from the other user.

See [[User Guide - Multi-User Vault]] for full sync details.

---

## Daily Workflow (Once You're Set Up)

1. Open Obsidian → your dashboard loads automatically (Homepage plugin)
2. Scan overdue contacts and upcoming touchpoints
3. Do your relationship work — meetings, calls, emails
4. After each meaningful conversation, log an Interaction note (2 minutes)
5. Cmd+P → "Git: Commit-and-sync" before you close Obsidian

---

## Quick Reference

| I want to... | Do this |
|--------------|---------|
| Add a new contact | New note in `People/Network/`, fill the template |
| Log a conversation | New note in `Interactions/`, name it `YYYY-MM-DD Title` |
| Add an organization | New note in `Organizations/`, fill the template |
| Track a funding source | New note in `Funders/`, fill the template |
| Start a new project or idea | New note in `Projects/`, set status to `idea` or `planning` |
| See who needs attention | Open your dashboard |
| Explore someone's connections | Open their note, then Cmd+P → "ExcaliBrain" |
| See the whole network | Cmd+P → "Graph: Open graph view" |
| Find all interactions with someone | Open their note → check backlinks panel |
| Sync your changes | Cmd+P → "Git: Commit-and-sync" |
