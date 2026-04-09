---
tags:
  - meta
---

# How to Use Templater

Templater auto-fills new notes with the right structure when you create them in the right folder. You don't need to invoke it manually — it runs automatically.

---

## How It Works

When you create a new note inside one of these folders, Templater detects the folder and fills the note with the matching template:

| Create a note in... | Gets this template |
|---------------------|--------------------|
| `People/` (or any subfolder) | Person Template |
| `Organizations/` | Organization Template |
| `Projects/` | Project Template |
| `Funders/` | Funder Template |
| `Interactions/` | Interaction Template |

Templater will prompt you with dropdown menus for fields like role, cadence, context type, and owner. Pick from the list and it fills the frontmatter automatically.

---

## Creating a New Note (The Everyday Flow)

1. In the file explorer sidebar, navigate to the folder you want (e.g., `People/Network/`)
2. Right-click → "New note" (or click the new note icon)
3. **Name the note first** — type the person's name, org name, or `YYYY-MM-DD Title` for interactions
4. Templater kicks in — you'll see dropdown prompts appear
5. Pick your options from each prompt
6. The note fills with frontmatter and body sections
7. Fill in the remaining fields and body content

---

## The Prompts You'll See

**Person Template** prompts for:
- Role (contact, NVP-team, business-owner, etc.)
- Cadence (weekly, biweekly, monthly, quarterly, as-needed)
- Owner (August or Savannah)

**Interaction Template** prompts for:
- Which team member is a participant (August or Savannah)
- Context type (call, email, text, event, meeting)
- Logged by (August or Savannah)

**Project Template** prompts for:
- Status (idea, planning, active, paused, completed)
- Lead (August or Savannah)

**Funder Template** prompts for:
- Status (prospect, cultivating, active, past)

**Organization Template** prompts for:
- Category (nonprofit, business, government, foundation, faith, media, association, education)

---

## After the Template Fills

The template gives you the structure — you still need to fill in:

- **Wikilink fields** like `orgs`, `members`, `participants`, `about` — type `[[` and start typing a name to search existing notes
- **Body sections** like Context, Summary, Key Takeaways — write a sentence or two, don't stress about being thorough
- **Empty `"[[]]"` placeholders** — either fill them in or delete the line

---

## If the Template Doesn't Fire

- Make sure the note is in the right folder (not the vault root)
- Check that Templater is enabled: Settings → Community Plugins → Templater should be toggled on
- Check folder template settings: Settings → Templater → Folder Templates should list each folder mapping
- Check that "Trigger Templater on new file creation" is ON

---

## Manually Applying a Template

If you created a note in the wrong place or the template didn't fire:
1. Cmd+P → "Templater: Insert template"
2. Pick the template you want from the list
3. It will fill the current note (prompts and all)

Note: this works best on an empty note. If the note already has content, the template will append rather than replace.
