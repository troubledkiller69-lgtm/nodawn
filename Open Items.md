---
tags: [nodawn, todo]
updated: 2026-10-21
---

# Open Items

Back to [[No Dawn - Home]] · History: [[Tracker]] · Decisions: [[Realism Ledger]]

The live list. Finished work leaves this note; it does not accumulate here. Anything older is in [[Tracker]] or [[Hub - Build History]].

## Blocking

- [ ] **Save the place.** The latest hub work, including the 2026-10-21 readability rebuild, exists only in the open Studio session. Publish when ready.
- [ ] Set `Lighting.Technology = Future` by hand (Explorer → Lighting → Properties). Scripts can't set it.
- [ ] Review or remove the Studio plugins `cloud_4771536187` and `cloud_7352299243`. They inject GUI scripts and errors into every playtest.

## Stage 4 close-out

- [ ] Capture the wrecked sedan. The other eight bus and pickup shots are in `Screenshots/stage4`.
- [ ] A wide field shot that includes the bus line.
- [ ] Bus road side and flat tyre; pickup from the plaza.
- [ ] Then judge the touch-ups visible in the shots: the stretched side mirror reads as a black slab, the grey primer plates look near-black at night, the windshield guard reads as a white grid.

## Stage 5 — final verify

- [ ] SectionGate on every section, LeakProbe, part budget against the current 10,693 parts / 109 lights.
- [ ] Playtest walk: spawn → vendors → lockers → workshop → gate. Console clean.
- [ ] Unit tests if any runtime code changed.
- [ ] Before and after shot set.

## Hub backlog

- [ ] Interiors: the vendor customer sides now have a dado, window lamps and (quartermaster) a notice board, but are still thin. Then Services, cells and medical.
- [ ] Declutter the exterior. The light hierarchy is done (2026-10-21: vendors, Services and both gates are the lit destinations); the clutter is not.
- [ ] Three cell-block `ScreenGlow` monitor faces and one ivy mesh had been deleted by hand in the place; the 2026-10-21 rebuild restored them from code. If they were removed on purpose, take them out of `Sections.CellBlock` / `Sections.Nature`.
- [ ] The No Dawn UI pass (not started).
- [ ] Small: `Kit.debris` reads as blocky; `HubAssets.Bus` and `Pickup` are unused; `DevTools.KitDraft` and `SectionsDraft` match live code and can be deleted.
- [ ] Living hub steps 2–5 (NPC routines, VFX, crows, events), parked. Favour sound and motion over more props.
- [ ] Judge by ear and eye: soundscape volumes, the boulders, and whether more boulders are wanted.

## Phase 1 loose ends

These need a published place, a second client, or hardware.

- [ ] Two clients: lock contention (#6) and cross-player access (#15).
- [ ] DataStore on a live server (#20).
- [ ] A real gamepad pass over the creator, inventory, vault and vendor screens (#17).
- [ ] Client fps on a published client, shadow-casting floodlights first.

## Decided — do not reopen

- **Signs.** All 71 removed on 2026-10-17. Handoff §4.9 asked for signage; the ruling overrides it.
- **Day/night cycling.** The hub is a permanent foggy night. Living Hub step 5 contradicts this and is obsolete.

## Phase 2 prerequisites

- [ ] Publish first, then clear the multi-client tests above.
- Raids as separate places; a profile-lock hand-off on teleport; teleport data for the deployment; server lifecycle (no timer, late join, loot respawn); raid damage rules; loot through `InventoryTx`.

## Phase 2 — code in progress

- [ ] Deploy `Shared.Rules.HitSelect` and `Shared.Rules.Rewind`, then run `nodawn/tests/run_rules.lua`. Both are pure; no rig needed.
- [ ] Rewrite `HitboxService` to a margin ray plus `HitSelect`, set `HumanoidRootPart.CanQuery = false` at spawn, and silence accessory handles.
- [ ] Run the synthetic-rig probe (blocked: Studio closed). Verify `Spherecast.Distance` semantics while it is open.
- [ ] Stance layer: `StanceRules`, `StanceService`, `MovementController`, `Movement` channel.
- [ ] `Shared.Rules.WoundRules` and `SurvivalRules` with tests; wound state, treatment actions, hunger/thirst meters.
- [ ] Rename the player-facing currency from Chits to Dollars in the HUD, vendor UI, item prices and tests. Keep `wallet.chits` internally until a schema pass.
- [ ] Movement animation set: work order written, in progress with Claude ([[Work Order - Movement Animation Set]]).

## Phase 2 — design decisions

- [ ] Approve the cosmetic-only store and the Dollars/Robux firewall ([[Idea - Monetisation]]).
- [ ] Approve the launch roster of eight firearms and the melee damage fix ([[Idea - Weapon Roster]]).
- [ ] Approve the account-renown model, or supply the level system He has in mind ([[Idea - Progression]]).
- [ ] Approve the open-server model: no timer, late join, loot respawns, extraction-only banking ([[Idea - Raid Frame]]).
- [ ] Decide 24 or 32 players for the first Detroit map; the model recommends 24 launch, 32 ceiling ([[Idea - Detroit Map]]).
- [ ] Approve procedural injuries and hunger/thirst ([[Idea - Injury and Survival]]).

