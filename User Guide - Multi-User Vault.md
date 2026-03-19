# Multi-User Vault Guide

This vault is shared between two users via Git. The **obsidian-git** plugin handles syncing automatically. Under normal use, you don't need to think about git at all — just write notes.

---

## How Sync Works

Your changes are automatically committed and pushed within 1 minute of your last edit. The other user's changes are pulled every 5 minutes and on startup. You'll see a brief status message in the bottom-right when a sync occurs.

## Rules

1. **Don't edit the same file at the same time.** The vault can handle it, but it creates merge conflicts that require manual cleanup. A quick message ("I'm editing X") is enough to avoid this.
2. **Let sync finish before closing Obsidian.** Changes push within ~1 minute of your last edit, but if you close immediately you may beat the timer. You can trigger it manually: Cmd+P → "Obsidian Git: Commit all changes".
3. **Don't delete `.gitignore` or `.gitattributes`.** These files live in the vault root and control what gets synced. They're invisible in Obsidian's file explorer but exist on disk.

## If Something Goes Wrong

### "I see weird `<<<<<<<` markers in a note"

This is a merge conflict. Both users edited the same part of the same file. The file will look something like:

```
<<<<<<< HEAD
Your version of the text
=======
The other person's version of the text
>>>>>>> origin/main
```

Fix it by keeping the text you want and deleting the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`). Save the file. The next auto-sync will commit your resolution.

### "My changes disappeared"

Open the command palette → "Obsidian Git: Open file history" on the affected note. Git preserves every version. You can view or recover any previous state.

If you can't find it through the plugin, open a terminal in the vault folder and run:

```bash
git log --oneline
```

This shows all commits. To see what changed in a specific commit:

```bash
git show <commit-hash>
```

### "Sync isn't working"

Open the command palette → "Obsidian Git: Pull" and then "Obsidian Git: Push". Check for error messages in Obsidian's developer console (Cmd+Shift+I → Console tab). The most common cause is an expired authentication token or network issue.

## Manual Sync Commands

All available from the command palette (Cmd+P):

| Command | What it does |
|---|---|
| Obsidian Git: Commit all changes | Saves your current changes to git |
| Obsidian Git: Push | Sends your commits to the remote |
| Obsidian Git: Pull | Fetches the other user's changes |
| Obsidian Git: Open file history | Shows version history for the current note |

## What Not to Worry About

- **Plugin settings and vault config** are shared automatically. If one user installs a new plugin, it'll appear for the other on next sync.
- **Theme and hotkey preferences** are per-user and won't conflict.
- **Deleted notes** go to Obsidian's trash first, and git retains the history regardless. Nothing is ever permanently lost.

---

## New User Setup

If you're the second user joining this vault, here's how to get set up.

**You'll need:** a Mac with Obsidian installed.

**1. Install the SSH key.**
August will send you a file called `id_ed25519`. Save it, then open Terminal and run:

```bash
mkdir -p ~/.ssh
cp /path/to/the/id_ed25519 ~/.ssh/id_ed25519
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
```

Test it:

```bash
ssh -T git@github.com
```

You should see: `Hi afteraugust! You've successfully authenticated...`

**2. Install git if needed.**
Run `git --version` in Terminal. If it's not installed, macOS will prompt you to install the Command Line Tools — follow that prompt.

**3. Clone the vault.**

```bash
git clone git@github.com:afteraugust/NVP_Relational_Map.git ~/NVP_Relational_Map
```

Change `~/NVP_Relational_Map` to wherever you want the vault to live on your machine.

**4. Open it in Obsidian.**
Open Obsidian → "Open folder as vault" → select the `NVP_Relational_Map` folder you just cloned.

**5. Enable the sync plugin.**
Go to Settings → Community Plugins → the obsidian-git plugin should already be listed. Enable it. Open its settings and verify they match these values:

| Setting | Value |
|---|---|
| Auto commit-and-sync interval | 1 minute |
| Auto commit-and-sync after stopping file edits | ON |
| Auto pull interval | 5 minutes |
| Pull on startup | ON |
| Push on commit-and-sync | ON |
| Pull on commit-and-sync | ON |
| Merge strategy | Merge |
| Merge strategy on conflicts | None (git default) |

**6. Test it.**
Create a test note, then Cmd+P → "Obsidian Git: Commit all changes". Let August know and he'll confirm it showed up on his end.
