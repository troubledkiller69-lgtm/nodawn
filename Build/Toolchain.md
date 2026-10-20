---
tags: [nodawn, tooling, build]
updated: 2026-10-20
---

# Toolchain

Back to [[No Dawn - Home]] · Build record: [[Hub - How It Is Built]] · Rules: [[Design Rules]]

How the code leaves Studio and comes back. Set up 2026-10-20, because until then the only copy of the
game's Luau was the open Studio session.

## Where things live

| Thing | Path |
|---|---|
| Game source (this repo) | `C:\Users\Lando\Documents\No Dawn` |
| Design record (this vault) | `C:\Users\Lando\Documents\Obsidian Vaults\No Dawn` |
| Tools | `%LOCALAPPDATA%\NoDawnTools` (on PATH) |
| Rojo Studio plugin | `%LOCALAPPDATA%\Roblox\Plugins\Rojo.rbxm` |
| Studio MCP launcher | `%LOCALAPPDATA%\Roblox\mcp.bat` → `StudioMCP.exe` |

## Tools

| Tool | Version | For |
|---|---|---|
| `rojo` | 7.7.1 | Sync `src/` ↔ the place; build `.rbxlx` without Studio |
| `selene` | 0.32.0 | Lint |
| `stylua` | 2.6.0 | Format — tabs, 100 cols, double quotes |
| `StudioMCP` | 1.0.0 | Roblox's own Studio MCP server (stdio) |

`.\build.ps1 check | sync | build | fmt | sourcemap | test`. `check` is the default.

## Two ways to change the place

1. **`multi_edit` over the Studio MCP** — writes into the live datamodel. No restart, no plugin. Good
   for a quick fix while a session is open.
2. **Rojo sync** — edit files on disk, `build.ps1 sync`, press Connect in the Rojo plugin. Diffable,
   reviewable, survives a Studio crash.

If a change deserves a commit message, make it in a file first.

## Enabling the MCP

Roblox's Studio MCP is built into Studio but off by default. One-time, per machine:

1. In Studio, open **Assistant**.
2. **…** → **Manage MCP Servers**.
3. Turn on **Enable Studio as MCP server**.

Then `cmd.exe /c %LOCALAPPDATA%\Roblox\mcp.bat` is a valid stdio MCP command. `opencode.json` in the
game repo already points at it, so a session opened in that folder gets the tools.

Tools it exposes: `script_read`, `multi_edit`, `script_search`, `script_grep`, `execute_luau`,
`search_game_tree`, `inspect_instance`, `get_studio_state`, `start_stop_play`, `get_console_output`,
`screen_capture`, `list_roblox_studios`, plus asset generation and input simulation.

## Gotchas

- **Every tool call takes a `studio_id`.** Call `list_roblox_studios` first.
- **`StudioMCP.exe` waits for Studio to connect back to it.** If `tools/list` returns empty, the
  "Enable Studio as MCP server" toggle is the thing to check — the proxy process starting is not
  evidence that it worked.
- **The place is live-collaborative.** Other editors appear under Live Collaborators. Rojo writes land
  in their session too, and an unsaved session is the only copy of anything not in git.
- **Rojo plugin loads at Studio startup.** It will not appear until Studio is restarted once after
  install.
