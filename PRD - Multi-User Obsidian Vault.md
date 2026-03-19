# PRD: Multi-User Obsidian Vault via Git

**Status:** Draft
**Date:** 2026-03-18
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

## 4. Constraints & Assumptions

- No more than two concurrent users.
- Small vault (under 1,000 notes, minimal large attachments).
- Very low probability of concurrent edits to the same file.
- Both users have desktop machines (macOS, Windows, or Linux).
- At least one user is comfortable with basic git operations for troubleshooting.

## 5. Architecture

```
User A (local vault) <──push/pull──> Private Remote Repo <──push/pull──> User B (local vault)
       │                              (GitHub / GitLab)                         │
  obsidian-git plugin                                                    obsidian-git plugin
  (auto-commit, pull, push)                                              (auto-commit, pull, push)
```

Each user maintains a full local copy. The obsidian-git plugin handles automatic commits and sync on a configurable interval. The remote repository (GitHub or GitLab, private) serves as the central sync point and backup.

## 6. Setup Steps

### 6.1 Prerequisites

Each user needs:

- Obsidian installed (desktop).
- Git installed and configured (`git --version` to verify).
- A GitHub or GitLab account.
- SSH key or personal access token configured for git authentication. (GitHub docs: https://docs.github.com/en/authentication)

### 6.2 User A: Initialize the Vault

**6.2.1 — Create or choose the vault.**
Open Obsidian, create a new vault (or use an existing one). Note the vault's folder path on disk.

**6.2.2 — Create the remote repository.**
On GitHub or GitLab, create a new **private** repository. Do not initialize with a README (the vault content will serve as the initial commit).

**6.2.3 — Initialize git in the vault folder.**

```bash
cd /path/to/vault
git init
```

**6.2.4 — Create `.gitignore`.**
Create a file named `.gitignore` in the vault root with the following contents:

```
# Per-device workspace state
.obsidian/workspace.json
.obsidian/workspace-mobile.json

# Per-user preferences (remove these lines if you want shared hotkeys/theme)
.obsidian/hotkeys.json
.obsidian/appearance.json

# Obsidian trash
.trash/

# OS artifacts
.DS_Store
Thumbs.db
desktop.ini
```

**6.2.5 — Create `.gitattributes`.**
Create a file named `.gitattributes` in the vault root. This prevents line-ending conflicts if users are on different operating systems.

```
* text=auto
*.md text eol=lf
```

**6.2.6 — Initial commit and push.**

```bash
git add .
git commit -m "Initial vault setup"
git branch -M main
git remote add origin git@github.com:YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

**6.2.7 — Install and configure obsidian-git.**
In Obsidian: Settings → Community Plugins → Browse → search "Obsidian Git" (by Vinzent03) → Install → Enable.

Configure these settings:

| Setting | Value | Reason |
|---|---|---|
| Auto pull interval (minutes) | 5 | Picks up the other user's changes regularly |
| Vault backup interval (minutes) | 5 | Auto-commits and pushes your changes |
| Pull updates on startup | ON | Always start with latest state |
| Push on backup | ON | Couples commit with push |
| Commit message | `{{hostname}}: {{date}}` | Identifies who committed and when |
| Auto backup after stop editing any file | ON | Catches edits promptly |

### 6.3 User B: Clone and Join

**6.3.1 — Clone the repository.**

```bash
git clone git@github.com:YOUR_USERNAME/YOUR_REPO.git /path/to/local/vault
```

**6.3.2 — Open as vault.**
In Obsidian: Open folder as vault → select the cloned folder.

**6.3.3 — Install and configure obsidian-git.**
Same plugin installation and settings as User A (section 6.2.7). The plugin configuration lives in `.obsidian/plugins/obsidian-git/data.json`, which is tracked — but User B should verify settings match after first pull.

**6.3.4 — Verify sync.**
User A creates a test note. Wait for auto-push (~5 min or trigger manually via command palette → "Obsidian Git: Commit all changes" then "Obsidian Git: Push"). User B pulls (auto or manual). Confirm the note appears.

### 6.4 Verification Checklist

- [ ] Both users can create notes and see them appear on the other machine within ~10 minutes.
- [ ] `.obsidian/workspace.json` changes do NOT trigger commits.
- [ ] Editing the same file sequentially (not concurrently) merges cleanly.
- [ ] Deliberately create a conflict (both edit the same line of the same file without syncing). Confirm conflict markers appear and can be resolved manually.

## 7. Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Merge conflict on a note | Low (given usage pattern) | Medium — requires manual resolution | Use manual merge strategy; communicate when editing shared files |
| Silent data loss from "ours"/"theirs" strategy | N/A if configured correctly | High | Never set automatic conflict resolution to discard one side |
| Workspace/metadata conflicts | Eliminated | None | Covered by `.gitignore` |
| Repository bloat from attachments | Low (small vault) | Low | Monitor repo size; move to git-lfs if it exceeds ~500 MB |
| Authentication token expiry | Occasional | Low — push/pull fails until renewed | Use SSH keys (no expiry) over personal access tokens |
| Plugin update breaks sync | Rare | Medium | Pin plugin version; test updates on one machine first |

## 8. Future Considerations

- **Mobile access:** If needed later, evaluate Obsidian Sync or Syncthing as a complement. The obsidian-git plugin's mobile support is unreliable as of 2026.
- **More users:** Git scales to small teams (3–5) with the same setup, but conflict probability rises. Beyond that, consider Obsidian Sync or LiveSync.
- **Attachment management:** If the vault accumulates large binary files, migrate to git-lfs or an external attachment store.
- **Automated backups:** The git remote already serves as a backup, but consider a scheduled job to clone/archive the repo elsewhere for redundancy.
