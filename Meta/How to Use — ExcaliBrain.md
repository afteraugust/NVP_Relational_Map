---
tags:
  - meta
---

# How to Use ExcaliBrain

ExcaliBrain is an auto-generated relationship map. It reads the frontmatter fields in your notes and draws connections between them. You don't build the map — it builds itself from your data.

---

## Opening ExcaliBrain

1. Navigate to any note in the vault (a person, org, funder, etc.)
2. Cmd+P → type "ExcaliBrain" → select "ExcaliBrain"
3. A visual map opens, centered on your current note

Or: click the ExcaliBrain icon in the left sidebar (brain icon) if it's visible.

---

## Reading the Map

When you open ExcaliBrain on a note, you see the focused entity in the center with connections radiating outward:

- **Above (Parents):** Entities this one "belongs to." For a person, these are their organizations. For a funder, this is the source org.
- **Below (Children):** Entities "within" this one. For an org, these are its members. For an interaction, these are its participants.
- **Left (Friends):** Peer relationships. For an org, these are its partners.
- **Further Left (Previous):** How the connection was formed. For a person, this is who introduced them. For a funder, this is who connected you.

---

## Navigating

- **Click any node** to re-center the map on that entity. The whole view redraws around it.
- **This is the power move:** Start on a funder → click up to its source org → click down to another member of that org → click left to see who that person partners with. You're tracing relationship pathways through the network.

---

## What Each Connection Means

| You see... | It came from this field | Meaning |
|-----------|------------------------|---------|
| Person → Org (above) | Person's `orgs` field | "This person is at this org" |
| Org → Person (below) | Org's `members` field | "This org includes this person" |
| Org → Org (left) | Org's `partners-with` field | "These orgs work together" |
| Funder → Org (above) | Funder's `source-org` field | "This funder is housed at this org" |
| Funder → Person (below) | Funder's `contact` field | "This is our contact at the funder" |
| Person → Person (further left) | Person's `introduced-by` field | "This person introduced us" |
| Funder → Person (further left) | Funder's `connected-via` field | "This person connected us to the funder" |

---

## Practical Uses

**Before a meeting:** Open the person's note → launch ExcaliBrain → see at a glance what orgs they belong to, who introduced them, what funders or projects they're connected to. Instant context.

**Exploring funding pathways:** Open a funder → see the source org (above), your contact (below), and who connected you (left). Click on the contact to see their other org connections — maybe they bridge you to another funder.

**Finding hidden connections:** Open an org → see all its members below → click on a member you haven't talked to → see their other orgs and connections. "Oh, this person is also at the community foundation — I didn't know that."

**Understanding clusters:** Open any person → note which orgs cluster above them → click those orgs → see who else is a member. This reveals the social clusters in your network.

---

## Tips

- ExcaliBrain only sees **wikilinks** in frontmatter. If a field contains plain text instead of `[[links]]`, the connection won't appear. Make sure property types are set to "Link" or "List of links" in Settings → Properties.
- The map updates in real-time as you edit frontmatter. Add a new `orgs` link to a person, and the connection appears immediately.
- You can pin ExcaliBrain in a side panel and keep it open while you navigate notes. The map re-centers as you switch notes.
- If the map looks empty or sparse, it usually means frontmatter fields are missing or entered as plain text instead of wikilinks.
