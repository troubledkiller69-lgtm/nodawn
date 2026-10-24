---
tags: [nodawn, todo]
updated: 2026-10-23
---

# Open Items

Back to [[No Dawn - Home]] · History: [[Tracker]] · Decisions: [[Realism Ledger]]

The live list. Finished work leaves this note; it does not accumulate here. Anything older is in [[Tracker]] or [[Hub - Build History]].

## Blocking

- [ ] **Save the place.** The latest hub work, including the 2026-10-21 readability rebuild and the 2026-10-22 movement wiring and first-person view, exists only in the open Studio session. Publish when ready.
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
- [ ] The No Dawn UI pass: the user is having ChatGPT build the main HUD (stamina, health, etc.). What it can read is in [[HUD Hooks]].
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
- **No grapple** (user, 2026-10-23: "lets remove the grapple completely i though it would work well with this type of game and i was mistook"). Code, clips, the GR-04 model and its notes were removed; don't bring it back.
- **Day/night cycling.** The hub is a permanent foggy night. Living Hub step 5 contradicts this and is obsolete.

## Raid map — Detroit

See [[Map - How It Is Built]].

- [ ] Judge the parking lots from above: the pavement and dirt cells blend into soft pale blotches at 4-stud resolution (roof capture).
- [ ] Commit `tools/` in the game repo: MapBuilder, the map pipeline and the 2026-10-21/22 work (`geo.py`, `tolua.py`, `Ground.lua`, `Buildings.lua`, `Trenches.lua`, `Overgrowth.lua`, `Runtime/`, `MapBuilder.lua`, `BuiltGrid.lua`) exist only on disk and in the raid place.
- [ ] Judge the knotweed and grass tufts at night: they read paler, blue-grey, than the trees, sumac and vines (`overgrowth_detroit_lineup.jpg`).
- [ ] Judge the Cut's density: sumac thickets close the views either side of the greenway (`overgrowth_cut_division.jpg`). Thin the Cut layer if the shortcut needs sightlines.
- [ ] Overgrowth leftovers: 13 of 368 tree crowns reach a building wall; one lot plant stands on asphalt. Tighten the crown clearance in the Overgrowth section if they show.
- [ ] Props pass: wrecked cars, dead utility poles and lines, dumpsters, market stalls in the sheds, barricades where streets meet the boundary.
- [ ] The Cut's other exits: the greenway ramps (data exported) and stairs beside each bridge.
- [ ] 15–18 enterable buildings with real interiors (stair core, upper floor); the rest stay sealed shells.
- [ ] Gameplay markers: 24 spawns in 8 clusters, 5 extracts (3 active per server), ~500 loot nodes.
- [ ] Credits UI: "Map data © OpenStreetMap contributors" (ODbL). The world itself stays textless.
- [ ] Save and publish the raid place.

## Phase 2 prerequisites

- [ ] Publish first, then clear the multi-client tests above.
- Raids as separate places; a profile-lock hand-off on teleport; teleport data for the deployment; server lifecycle (no timer, late join, loot respawn); raid damage rules; loot through `InventoryTx`.

## Phase 2 — code in progress

- [ ] Deploy `Shared.Rules.HitSelect` and `Shared.Rules.Rewind`, then run `nodawn/tests/run_rules.lua`. Both are pure; no rig needed.
- [ ] Rewrite `HitboxService` to a margin ray plus `HitSelect`, set `HumanoidRootPart.CanQuery = false` at spawn, and silence accessory handles.
- [ ] Run the synthetic-rig probe (blocked: Studio closed). Verify `Spherecast.Distance` semantics while it is open.
- [ ] Stance layer: stand, crouch (2026-10-22), prone and lean (2026-10-23, see [[Realism Ledger]]) shipped; the stance hitboxes wait on `HitboxService`. Lean follow-ups: a stamina cost and cooldown (no numbers in the notes), a touch button, and E doubling as the ProximityPrompt key.
- [x] `SurvivalRules` with tests; wound state, treatment actions, nutrition/hydration meters, stress, Injuries tab (2026-10-24, see [[Realism Ledger]]; report in `Desktop\No Dawn\survival\README.md`). Follow-ups: pain and painkillers, fractures, infection, squad treatment, the hub medical ward, a physical controller pass, a published multiplayer check, and dropping the hub drain once raids exist.
- [ ] Schema pass: rename the profile key `wallet.chits` (and `Economy.StartingChits` / `MaxChits` with it) through a migration. Player-facing text already says Dollars (2026-10-21).
- [ ] Movement animation set: v1 delivered 2026-10-21 (14 clips) and v2 2026-10-23 (8: leans and the prone set); all 22 in `Desktop\No Dawn\animation\out\ND_Movement_v1.rbxm` ([[Work Order - Movement Animation Set]]). ToProne's head lift mid-way is a polish item.
- [ ] Save the 22 KeyframeSequences in `ServerStorage.Animations.ND_Movement_v1` to Roblox (right-click → Save to Roblox) and put the ids in `Shared.Config.AnimationIds` (human step). Studio playtests already play the clips; a live server keeps Roblox's Animate script, and sprint stop, slide, vault and mantle stay off, until every id is set.
- [ ] Movement follow-ups (wiring shipped 2026-10-22, see [[Realism Ledger]]): crouch doesn't shrink the hitbox yet; see fall damage on the first raid playtest (the hub is all safe zone).

## Phase 2 — design decisions

- [ ] Approve the cosmetic-only store and the Dollars/Robux firewall ([[Idea - Monetisation]]).
- [ ] Approve the launch roster of eight firearms and the melee damage fix ([[Idea - Weapon Roster]]).
- [ ] Approve the account-renown model, or supply the level system He has in mind ([[Idea - Progression]]).
- [ ] Approve the open-server model: no timer, late join, loot respawns, extraction-only banking ([[Idea - Raid Frame]]).
- [ ] Decide 24 or 32 players for the first Detroit map; the model recommends 24 launch, 32 ceiling ([[Idea - Detroit Map]]).
- [ ] Approve procedural injuries and hunger/thirst ([[Idea - Injury and Survival]]).

