---
tags: [nodawn, spec]
updated: 2026-10-18
---

# Phase 1 Scope Map

Back to [[No Dawn - Home]] · The spec itself: [[Phase 1 Handoff]]

Every requirement in the handoff, where it actually landed, and what state it is in. Use this before designing Phase 2 — it answers "is this already built?" without re-deriving it from the place.

## Requirements → implementation

| Handoff | Requirement | Where it landed | State |
|---|---|---|---|
| §1 | Audit before touching anything | Milestone A, recorded in [[Phase 1 Architecture]] | Done |
| §2 | Modular Luau, centralised config, server-owned state | `ServerScriptService` services; `ReplicatedStorage.Shared` Config + Rules; `StarterPlayer` controllers | Done |
| §3 | First person, PC and console, original identity | First-person camera; gamepad focus wired | Mouse verified; gamepad partial (#17) |
| §4 | Prison hub, greybox first, server-enforced safe zone | `HubBuilder` and 13 sections; `DamageService` zone check | Built well past greybox. 10,693 parts |
| §5 | Survivor creation, mix-and-match, occupations | `CreationScreen`, `Config/Appearance`, `Config/Occupations` | Done |
| §6 | Six attributes, validated on the server | `Config/Attributes`, `SurvivorService` | Done |
| §7 | Versioned profiles, session lock, migrations, no blank overwrite | `PlayerDataService`, `ProfileSessions`, `ProfileRepository`, `Migrations` | Done; live-server test (#20) outstanding |
| §8 | Item catalog, four storage categories, grid + weight | `Config/Items`, `InventoryService` and `InventoryTx`, `GridRules` | Done, with UI |
| §9 | Currency, static vendor stock, atomic trades | `Config/Vendors`, `EconomyService`, `TradeRules` | Done |
| §10 | Personal vault, secure container | Locker terminal; secure-container eligibility rules | Done |
| §11 | Grounded UI, controller-friendly, no fake controls | `Theme`, `ScreenManager`, creation, inventory, vendor and session screens | Done and mouse-driven; the identity pass is not started |
| §12 | Server authority, rate limits, deduplication | `RequestGuard`, `RemoteRouter`, owner-only replication | Done |
| §13 | Bounded perf, no per-frame loops | 60 Hz server, ~63 fps in playtest | Published-client fps unmeasured |
| §14 | Milestones A–G | Reordered A → B → C → E1 → D → E2 → F → G | Done |
| §15 | 20 acceptance tests | [[Phase 1 Final Report]], [[Tracker]] | 15 pass, 5 partial, 0 fail |
| §16 | Out-of-scope list respected | No raids, infected, full combat, factions, trading, crafting, monetisation | Respected |
| §17 | Delivery report per milestone | [[Tracker]], [[Phase 1 - Game Systems]] | Done |

## Deliberate deviations

**Signs — §4.9 asked for them, the hub has none.** On 2026-10-17 all 71 sign and paint calls were removed at the user's request, including wayfinding and function labels. Vendors, lockers and the gate are now read from NPCs, layout and prompts. The standing rule in [[Design Rules]] overrides §4.9. Do not add text back without asking, easter eggs included.

**Blocky avatars, realistic world.** §3 asks for blocky Roblox avatar proportions. The realism rules demand believable props and environments. Both hold: characters are R15, environments are not. "Never blocky" applies to the world, not the rigs.

**Permanent night.** The handoff is silent on time of day. The hub is a fixed foggy, rainy night (`Config.World.Fixed`, day-for-night render). Living Hub step 5's day/night cycle contradicts this and is dead.

**Milestone order.** E1 before D, because survivor creation grants the starting kit and Chits through the transaction engine.

## Seams Phase 2 inherits

- `DamageService.apply` is the only damage path and already refuses when either side is in a zone.
- `InventoryTx` gives atomic transfers, ready for loot and death drops.
- `ProfileSchema` reserves `stats` and `progression`.
- Hub and raids are separate places; teleporting needs a profile-lock hand-off, teleport data, and the raid's own damage rules.
- The Sadist is stored `concealed = true` with owner-only replication and the same starting kit as Unemployed, so gear cannot reveal it. Its stress and infected-killing effects are undefined — see [[Brainstorm - Index]].
