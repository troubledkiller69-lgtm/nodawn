---
tags: [nodawn, overview]
updated: 2026-10-18
---
# Project Overview

Back to [[No Dawn - Home]]

## The game
- Survival extraction shooter on Roblox. **Phase 1** (done) is the hub and everything a player keeps between raids:
  a persistent profile, survivor creation (appearance, occupation, attributes), a grid inventory and a vault
  (locker), currency (Chits) and two vendors. **Phase 2** adds raids, run as separate places.
- The hub is a prison (West Georgia prison from *The Walking Dead*) with a yard, cell blocks, quartermaster and armory
  (vendors), lockers, a medical ward, a vehicle workshop, Services and offices, and a front field with a farm, an
  animal pen and the double main gate. It is always a foggy, rainy night, and 59 NPCs live there.

## Where things live in the place
| Place in Studio | What |
|---|---|
| `ServerScriptService` | One server Script (`Main`) and the services: profiles (PlayerDataService, ProfileSessions, ProfileRepository, Migrations), InventoryService / InventoryTx, SurvivorService, EconomyService, DamageService, RemoteRouter + RequestGuard, interaction and NPC services |
| `ReplicatedStorage.Shared` | Config (Items, Inventory, Occupations, Attributes, Appearance, Vendors, World, Ambience) and pure rules (grid/inventory rules, TradeRules, WorldClock) |
| `StarterPlayer` client | `ClientMain` and controllers: CreationScreen, InventoryScreen, VendorScreen, SessionScreen, ScreenManager, WorldController (night lighting, lamps), AmbienceController (soundscape) |
| `ServerStorage.UnitTest` | Unit suite (137 tests); run `RunUnitTest()` on a playtest server |
| `ServerStorage.DevTools` | Edit-time tools only: **HubBuilder** (Kit + Sections), HubAssets (generated meshes, foliage, NPC gear), PropTools, SectionGate, LeakProbe, AppearanceLibrary, and the dated backups |
| `Workspace.Hub` | The built hub. **Never edit it by hand**: a rebuild replaces it. Change the HubBuilder instead |

## Rebuilding the hub
Run in Studio's command bar (or through the MCP `execute_luau`), Edit mode:
```lua
local m = game.ServerStorage.DevTools.HubBuilder:Clone(); m.Parent = game.ServerStorage
print(require(m).build()); m:Destroy()
```
Requiring a clone always runs the current source. Plain `require` returns a cached copy for the whole Studio session.

## Working with Claude in Studio (must-knows)
- **Camera:** the Studio MCP tools leave the edit camera `Scriptable` about 0.05 s after every call, which freezes your
  viewport. Every `execute_luau` ends with a delayed reset to `Fixed`; captures save and restore the camera (CFrame
  and Focus). Details in [[Lessons|Lessons]].
- **Backups before edits**, shared modules edited on a draft first, and every change checked with SectionGate. See
  [[Hub - How It Is Built]].
- If captures come back blank white, Studio isn't rendering its viewport (window minimized or screen locked).
