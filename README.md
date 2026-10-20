# No Dawn - vault

Design and build record for **No Dawn**, a Roblox survival extraction shooter. Start at [[No Dawn - Home]].

## First open

1. Clone the repo.
2. Obsidian → **Open folder as vault** → select the clone.
3. When Obsidian asks, trust the author and enable community plugins. The Git plugin is already committed in `.obsidian/plugins/obsidian-git`.
4. Sign in to GitHub when git asks. The plugin pulls on boot, commits every 10 minutes and pushes every 2.

The Obsidian setup is committed, so the vault behaves the same on every machine: core plugins,
templates, daily notes, appearance, graph colours and bookmarks. Only `.obsidian/workspace.json` is
ignored, so each machine keeps its own layout.

This machine's clone: `C:\Users\Lando\Documents\Obsidian Vaults\No Dawn`.

## What's in it

| Folder | For |
|---|---|
| `Spec/` | The contract. Specs, standing rules, work orders, handoffs |
| `Build/` | How the hub is built, and the log of every build pass |
| `Records/` | What happened. Snapshots, ledger, acceptance, handover |
| `Brainstorm/` | Everything after Phase 1. Nothing here is a commitment yet |
| `Screenshots/` | Captures, by stage. Binary — do not re-save |
| `Templates/` | Note templates, ready to insert |
| `Daily/` | Daily scratchpad. Triaged out before the day closes |

## House rules

- `.obsidian/workspace.json` is ignored, so each machine keeps its own layout.
- Never commit credentials. The plugin uses the system git credential manager.
- One editor per note at a time. The plugin merges, but Markdown merges are line-based and ugly.
- Images live in `Screenshots/`. They are binary and should not be re-saved by an editor.
- The conventions — note shape, naming, where a thing goes, frozen-record banners — are in
  `CLAUDE.md`. Read it before editing anything.
