---
tags:
  - meta
---

# Workflow Conventions

## File Naming
- **People:** Full name as spoken (e.g., `Rev. James Whitfield`, `Prof. Sarah Lin`, `Councilwoman Diane Okafor`)
- **Organizations:** Official name (e.g., `Riverside Community Foundation`)
- **Interactions:** `YYYY-MM-DD Title` (e.g., `2026-02-07 Merchant Mixer`)
- **Projects:** Descriptive name (e.g., `Spring Block Party Series`)
- **Funders:** Specific opportunity name (e.g., `RCF Community Building Grant`)
- **Ideas:** Descriptive name (e.g., `University Service-Learning Partnership`)

## Frontmatter Conventions
- Always use wikilinks in frontmatter: `"[[Person Name]]"` (quoted to avoid YAML issues)
- Use the exact file name in wikilinks — no aliases in frontmatter
- `warmth` values: `hot` (active champion), `warm` (engaged), `cool` (early/distant), `cold` (dormant), `new` (just added)
- `owner` is the team member who "owns" the primary relationship — not exclusive, just primary point of contact
- Update `last-contact` after every interaction
- Set `next-followup` based on warmth: hot=2 weeks, warm=3 weeks, cool=1 month, cold=quarterly

## Creating New Contacts
1. Create new file in `People/Network/`
2. Templater auto-fills the person template
3. Fill frontmatter: orgs, introduced-by, knows, warmth, owner
4. Write a brief Context section (how you met, why they matter)
5. Add `knows` links to the new person on any existing contacts who know them
6. Create an Interaction note if the first meeting was substantive

## Logging Interactions
1. Create new file in `Interactions/`
2. Name it `YYYY-MM-DD Brief Title`
3. Fill frontmatter: participants, context, about, owner, followup
4. Write Summary and Key Takeaways
5. List Action Items with checkboxes
6. Update `last-contact` on all participant files
7. Update `next-followup` on participant files as appropriate
8. If new connections were discovered, update `knows` fields on relevant Person files

## Relationship Ownership
- **August:** Business community, partnership development, strategic contacts
- **Maria:** Community contacts, event-related relationships, media, youth
- **David:** Funders, elected officials, institutional leaders, high-level partnerships
- When a contact naturally spans multiple owners, assign to whoever has the warmest relationship

## Git Backup
One team member (suggest August) should have the Obsidian Git plugin configured:
- Auto-commit every 30 minutes
- Auto-push daily
- This provides version history and disaster recovery alongside Relay

## Excalidraw Map Maintenance
- **Strategic Overview:** Update monthly or after major network changes
- **Funding Pathways:** Update when funder status changes
- **Network Clusters:** Update when new cluster connections are discovered
- **Event Partner Ecosystem:** Update during project planning phases

## Weekly Team Sync
Use the vault to drive your weekly team meeting:
1. Review outstanding followups (Home dashboard)
2. Each person reports on their hot/warm contacts
3. Review funding pipeline
4. Discuss ideas pipeline — anything ready to move forward?
5. Update Excalidraw maps if strategic picture has shifted
