# PRD: Multi-User Obsidian Vault via Git

**Status:** In Progress — User A setup complete
**Date:** 2026-03-19
**Author:** August

---

## 1. Overview

Set up a shared Obsidian vault for two users, synchronized via a private Git repository and the obsidian-git community plugin. The vault will serve as a collaborative knowledge base with full version history and offline editing capability.

## 2. Goals

- Two users can read and edit the same vault from separate machines.
- Changes propagate automatically within minutes.
- Full edit history is preserved.
- Merge conflicts are rare and manageable when they occur.
- No recurring subscription cost.

## 3. Non-Goals

- Real-time collaborative editing (Google Docs-style).
- Mobile access (obsidian-git has known limitations on mobile; desktop only for now).
- Fine-grained permissions or access control (both users have full read/write).
- Branching workflows or pull request reviews.
- Per-user commit attribution (both users share a single GitHub account).

## 4. Constraints & Assumptions

- No more than two concurrent users.
- Small vault (under 1,000 notes, minimal large attachments).
- Very low probability of concurrent edits to the same file.
- Both users have desktop machines (macOS).
- Both users authenticate via a shared GitHub account and SSH key.
- At least one user is comfortable with basic git operations for troubleshooting.

## 5. Architecture

```
User A (local vault) <──push/pull──> Private Remote Repo <──push/pull──> User B (local vault)
       │                              (GitHub / GitLab)                         │
  obsidian-git plugin                                                    obsidian-git plugin
  (auto-commit, pull, push)                                              (auto-commit, pull, push)
```

Each user maintains a full local copy. The obsidian-git plugin handles automatic commits and sync on a configurable interval. The remote repository (GitHub, private) serves as the central sync point and backup.

**Remote:** `git@github.com:afteraugust/NVP_Relational_Map.git`
**Shared GitHub account:** afteraugust

## 6. Setup Steps

### 6.1 Prerequisites

Each user needs:

- Obsidian installed (desktop).
- Git installed and configured (`git --version` to verify; macOS will prompt to install Command Line Tools if missing).
- The shared SSH private key (`id_ed25519`) installed at `~/.ssh/id_ed25519`.

### 6.2 User A: Initialize the Vault — COMPLETE

**6.2.1 — Vault created.** Folder: `nvp_relational_map`

**6.2.2 — Remote repository created.** Private repo on GitHub: `afteraugust/NVP_Relational_Map`

**6.2.3 — Git initialized in the vault folder.**

```bash
cd /path/to/vault
git init
```

**6.2.4 — `.gitignore` created** in vault root:

```
# Per-device workspace state
.obsidian/workspace.json
.obsidian/workspace-mobile.json

# Per-user preferences
.obsidian/hotkeys.json
.obsidian/appearance.json

# Obsidian trash
.trash/

# OS artifacts
.DS_Store
Thumbs.db
desktop.ini
```

**6.2.5 — `.gitattributes` created** in vault root:

```
* text=auto
*.md text eol=lf
```

**6.2.6 — Initial commit and push.**

```bash
git add .
git commit -m "Initial vault setup"
git branch -M main
git remote add origin git@github.com:afteraugust/NVP_Relational_Map.git
git push -u origin main
```

**6.2.7 — SSH key generated and added to GitHub.**

```bash
ssh-keygen -t ed25519 -C "reveree@gmail.com"
# Accepted default path: /Users/augoost/.ssh/id_ed25519
# Verified with: ssh -T git@github.com
```

**6.2.8 — obsidian-git plugin installed and configured.** Settings:

| Setting | Value |
|---|---|
| Auto commit-and-sync interval (minutes) | 1 |
| Auto commit-and-sync after stopping file edits | ON |
| Auto pull interval (minutes) | 5 |
| Pull on startup | ON |
| Push on commit-and-sync | ON |
| Pull on commit-and-sync | ON |
| Merge strategy | Merge |
| Merge strategy on conflicts | None (git default) — manual resolution |
| Commit message on auto commit-and-sync | `{{hostname}}: {{date}}` |

### 6.3 User B: Clone and Join — PENDING

**6.3.1 — Install the shared SSH key.**
August sends the private key file (`id_ed25519` from `/Users/augoost/.ssh/id_ed25519`) to User B securely (AirDrop, encrypted message — not plaintext email).

```bash
mkdir -p ~/.ssh
cp /path/to/the/id_ed25519 ~/.ssh/id_ed25519
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
```

Verify: `ssh -T git@github.com` → should say `Hi afteraugust!`

**6.3.2 — Install git if needed.**
Run `git --version`. If not installed, macOS will prompt to install Command Line Tools.

**6.3.3 — Clone the repository.**

```bash
git clone git@github.com:afteraugust/NVP_Relational_Map.git ~/NVP_Relational_Map
```

(Adjust destination path as desired.)

**6.3.4 — Open as vault.**
In Obsidian: Open folder as vault → select the cloned `NVP_Relational_Map` folder.

**6.3.5 — Enable and verify obsidian-git.**
The plugin files are already in the repo. Go to Settings → Community Plugins → enable "Obsidian Git". Open its settings and verify they match the table in 6.2.8.

**6.3.6 — Test sync.**
Create a test note. Cmd+P → "Obsidian Git: Commit all changes" → confirm it appears on User A's machine within a few minutes.

### 6.4 Verification Checklist

- [ ] Both users can create notes and see them appear on the other machine within a few minutes.
- [ ] `.obsidian/workspace.json` changes do NOT trigger commits.
- [ ] Editing the same file sequentially (not concurrently) merges cleanly.
- [ ] Deliberately create a conflict (both edit the same line of the same file without syncing). Confirm conflict markers appear and can be resolved manually.

## 7. Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Merge conflict on a note | Low (given usage pattern) | Medium — requires manual resolution | Use manual merge strategy; communicate when editing shared files |
| Silent data loss from "ours"/"theirs" strategy | N/A if configured correctly | High | Merge strategy on conflicts set to "None (git default)" — never auto-discard |
| Workspace/metadata conflicts | Eliminated | None | Covered by `.gitignore` |
| Repository bloat from attachments | Low (small vault) | Low | Monitor repo size; move to git-lfs if it exceeds ~500 MB |
| SSH key compromise | Low | Medium | Send key securely; regenerate if compromised |
| Plugin update breaks sync | Rare | Medium | Test updates on one machine first |
| Edits lost on close before sync | Low (1-min interval) | Low | 1-minute auto-sync interval minimizes window; manual sync before close if concerned |

## 8. Future Considerations

- **Mobile access:** If needed later, evaluate Obsidian Sync or Syncthing as a complement. The obsidian-git plugin's mobile support is unreliable as of 2026.
- **More users:** Git scales to small teams (3–5) with the same setup, but conflict probability rises. Beyond that, consider Obsidian Sync or LiveSync.
- **Separate accounts:** If commit attribution becomes important later, create a second GitHub account, add as collaborator, and configure `git config user.name` per machine.
- **Attachment management:** If the vault accumulates large binary files, migrate to git-lfs or an external attachment store.
- **Automated backups:** The git remote already serves as a backup, but consider a scheduled job to clone/archive the repo elsewhere for redundancy.
