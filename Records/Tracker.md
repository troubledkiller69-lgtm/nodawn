# Phase 1 — Milestone Plan

> **Superseded.** This is the frozen Phase 1 working checklist, kept as the record of the build. Several boxes were never ticked even where the work was finished. Live work is in [[Open Items]]; current numbers are on [[No Dawn - Home]].

Design reference: [docs/phase1-architecture.md](../docs/phase1-architecture.md)
Order: A → B → C → E1 → D → E2 → F → G (E1 moved ahead of D; see architecture §12).
After each milestone, the report lists files and objects changed, functionality added, tests run and their results, known gaps, and what comes next.

## A — Audit and architecture ✅ (awaiting approval)
- [x] Audit the DataModel, services, scripts, remotes, settings and DataStore access
- [x] Define the module layout, profile schema, item, occupation and attribute models, and the remote interface
- [x] Document risks, blockers and the milestone order

## B — Prison hub greybox ✅
- [x] Replace the template Baseplate/SpawnLocation with ground, the perimeter wall and guard towers
- [x] Central yard and spawn; block out the cell blocks, armory/supply, medical, workshop, admin/services, vault room and deployment sally port
- [x] Circulation: corridors, doors, checkpoints, fencing, signage
- [x] Polish the yard + spawn and the supply vendor area; overcast lighting pass
- [x] Tag the `SafeZone` volume and the vendor/vault anchors (prompts are added at runtime by InteractionService)
- [x] Verify: navigation walk-through, collision, spawn placement, part count / perf, screenshots

## C — Persistent profile
- [ ] DataStoreAdapter (real store + double with fault injection), ProfileRepository, Migrations
- [ ] PlayerDataService: lock, load, sanitize, autosave, dirty tracking, release, BindToClose, owner-only replication
- [ ] Spawn gating (`CharacterAutoLoads=false`) and the failed-load / profile-in-use screens
- [ ] Tests against the double: new player, returning player, failed load never saves, held lock, lost lock during save, newer-schema refusal, shutdown
- [ ] Tests against the real DataStore (needs Studio API access): save → rejoin → restore, two lock holders

## E1 — Item catalog and transaction engine (no UI)
- [ ] Config/Items catalog, Config/Inventory (grids, slots, carry formula)
- [ ] GridRules / InventoryRules (pure) + InventoryService transactions (move, split, merge, equip)
- [ ] RequestGuard + RemoteRouter (rate limit, schema, rid dedup)
- [ ] Unit tests: bounds, overlap, rotation, stacks, weight, slot compatibility, secure eligibility, atomic rollback, replay

## D — Survivor creation
- [ ] Config/Occupations, Config/Attributes, Config/Appearance + curated AppearanceAssets
- [ ] SurvivorService: validate submissions, grant the kit + starting chits in one transaction, critical save, apply appearance, spawn
- [ ] Creation UI: appearance (labeled cosmetic), occupation cards, attribute allocation, review, confirm; viewport preview with rotation
- [ ] First-person camera + modal mouse release; gamepad focus and Back navigation
- [ ] Tests: invalid occupation, appearance, allocation and totals rejected; duplicate creation rejected; rejoin restores the survivor

## E2 — Inventory and vault UI
- [ ] Inventory screen (equipped, pockets, backpack, secure) + vault terminal screen; select-based detail panel
- [ ] Keyboard/mouse drag + gamepad select-then-place
- [ ] Tests: transfers preserve totals (item count and quantity sums), vault proximity enforced, no dup/loss under rapid repeats

## F — Currency and vendors ✅ (2026-10-17, user: "Do Milestone F first, then the living-hub pass")
- [x] Config/Vendors static stock + EconomyService buy/sell over the transaction engine
  - Config.Vendors: quartermaster + armory (matches the two tagged counters); fixed offers, unlimited supply;
    `buys` categories; require-time checks (live item, whole price >= sellValue so buy→sell can't mint Chits)
  - Shared/Rules/TradeRules: offer lookup, unit price, "does this vendor buy it" (client preview = server rule)
  - EconomyService.buy / .sell as InventoryTx stage ops; Vendor.Buy {vendorId, offerId, qty, delivery} and
    Vendor.Sell {vendorId, uid, qty}; vendor proximity re-checked per request; new code WalletFull
  - Delivery: carried = wear into free slot → pockets → backpack; vault = locker (server delivery, no terminal)
  - Sales from any container except worn gear (InventoryTx already documents vendor sales skip the terminal)
- [x] Vendor UI with before/after balance; updates only after server confirmation
  - VendorScreen: BUY/SELL tabs, list + detail, qty −/+/Max (gamepad ◄ ►), delivery toggle, Chits + carry preview
  - Transport failure retries once with the same rid (server replays instead of trading twice)
- [x] Tests: exact deduction and grant, insufficient funds, no space, replayed rid, sell ineligible/unowned, rapid concurrent requests
  - Unit (Economy_Test) + live playtest through the real remote: TooFar, replayed rid, 10 concurrent buys, UI capture
- Results:
  - Unit: 125/125 passing (22 new in Economy_Test), run on a playtest server
  - Live, real Studio DataStore, real Vendor remote: far buy → TooFar; injected `price` → BadPayload; 2 beans
    400 → 340 and the same rid replayed → cached result, charged once; 10 concurrent bandage buys → 8 Ok + 2
    RateLimited, 340 → 140 and +8 bandages exactly; medical sold at Armory → NotEligible; foreign uid →
    NotOwned; other counter from 38 studs → TooFar; revolver with 80 → InsufficientFunds
  - UI (mouse): prompt opens the screen, buy (preview 400 → 340, confirmed balance applied), sell with Max
    (80 → 120, row removed), Armory greys out medical with the reason, Back ×2 closes, walking away closes;
    balance persisted across playtests
  - Fixed during verification: success message used the client mirror as "before" (showed "for 320");
    now quotes the trade total, which the server prices from the same config
- Gaps: gamepad focus not exercisable from the MCP tools (RB tab switch verified as key input only);
  multi-client/published tests belong to G. Studio profile is now 120 Chits + 8 extra bandages (test trades)
- Objects: + Shared.Config.Vendors, + Shared.Rules.TradeRules, + Services.EconomyService,
  + Controllers.VendorScreen, + UnitTest.Cases.Economy_Test; ~ Net (WalletFull), ~ Main (boot order),
  ~ ClientMain, ~ Theme (Category colours), ~ InventoryScreen (uses Theme.Category)

## G — Integration and acceptance ✅ (2026-10-17; test list: docs/phase1-acceptance.md)
- [x] Run all 20 acceptance tests (results below)
- [x] Clean up debug code; check for duplicate systems (one RemoteRouter, DataStore only in the 2 persistence
      modules, no stray prints outside dev/test tools). Removed unused `ServerStorage._Quarantine` (166 objects;
      Studio didn't record an undo step). Kept DevTools backups: SectionGate/LeakProbe diff against them.
- [x] Final perf check: server 60 Hz (worst 19 ms). Client 15 fps / 68 ms worst in a Studio playtest
      (server+client in one process, Studio window likely unfocused): NOT representative. Risks to check on a
      published client: 16 shadow-casting floodlight SpotLights, ~5,900 shadow-casting parts, 7.4k parts streamed.
- [x] Readiness notes for Phase 2 (below)
- Living-hub step 1 soundscape verified: 5 beds load and play, 24 prop loops, ravens firing, indoor switch
  crossfades beds and reverb both ways. Volumes not judged by ear.

### Acceptance results (Studio = local solo playtest; "real DS" = real Studio DataStore)
| # | Result | How |
|---|---|---|
| 1 | Pass (Studio, in-memory store) | Fresh profile → creator via mouse: appearance, occupation (Mechanic), 6/7 points, review warning, Create → survivor, kit (pipe wrench equipped, duct tape), 400 Chits, character spawned |
| 2 | Pass (Studio, live remote + unit) | 9 invalid submits with no survivor: 7 → BadPayload (unknown occupation/attribute, value 99, negative, asset-id hair, missing slot, extra `chits` field), 2 → InvalidSelection (overspent pool, past creation cap); nothing created. Rate limit also observed |
| 3 | Pass (Studio, real DS) | Returning survivor loads with no creator; second Submit → AlreadyCreated, no second kit/Chits |
| 4 | Pass (Studio, real DS) | Snapshot vs restart: survivor (occupation, attributes, allocation, appearance), Chits, every item uid/position identical; appearance worn |
| 5 | Partial (unit only) | ProfileSessions/Repository tests against the double (failed load exposes no data, writes nothing; corrupt/future records). No live fault at load time |
| 6 | Pass (Studio, real DS) | Second lock holder → `locked`; foreign save of 999,999 Chits → `lost`, nothing written; live session saves `ok`. Two real servers not run |
| 7 | Pass (Studio, real DS + unit) | Milestone F: exact deduction and grant |
| 8 | Pass (Studio, real DS + unit) | Milestone F: replayed rid charged once; 10 concurrent → 8 Ok + 2 RateLimited, totals exact |
| 9 | Pass (Studio, real DS + unit) | Milestone F: sale removes units, credits once |
| 10 | Pass (Studio, real DS + unit) | Every refused trade/move left balance and items unchanged (verified by totals and snapshots) |
| 11 | Pass (Studio, remote + unit) | Overlap/out-of-bounds → InvalidMove; x=40 → BadPayload |
| 12 | Partial | Stack limits, split/merge rules, slot compatibility live; overweight unit-tested only |
| 13 | Pass (Studio, UI + remote) | Locker only at a terminal (TooFar both ways from the yard); UI quick-move and cell placement keep the same uid |
| 14 | Pass (Studio, remote + unit) | Non-eligible water → NotEligible; out-of-bounds/overlap refused; capacity in unit tests |
| 15 | Partial (single client) | Foreign uids → NotOwned; requests only ever touch the caller's session; State pushes owner-only. Two clients not run |
| 16 | Pass (Studio) | ForceField in zone, TakeDamage(40) blocked; Survivors collision group; DamageService gateway verified live in D |
| 17 | Partial | Mouse: creator, inventory/locker, vendor all driven end to end. Gamepad: Y/B/RB bindings fire and controls take focus, but a real controller (input type) couldn't be simulated |
| 18 | Pass (Studio, in-memory + unit) | Forced save failure: session stays dirty, status `error`, HUD says "Saving will retry automatically"; clearing the fault → next save `ok`. Shutdown covered by unit tests |
| 19 | Pass (Studio audit) | No duplicate systems; third-party plugin scripts injected into playtests are not part of the place |
| 20 | Partial | Real Studio DataStore verified (save → restart → restore, lock). Publishing is the user's action; live-server DataStore not run |

### Phase 2 readiness
- Seams in place: DamageService (single damage gateway, zone-aware), InventoryTx (atomic transfers for loot),
  EconomyService/TradeRules, ProfileSchema reserved `stats`/`progression`, hub/raid as separate places.
- Before or alongside Phase 2: publish, then run the multi-client/live-server tests (6, 15, 20) and a real
  gamepad pass (17); measure fps on a published client (shadow-casting floodlights first); hub polish backlog;
  living-hub steps 2–5 (re-scoped).
- Teleport to raids will need a lock hand-off (hub releases on leave, raid acquires with LockAcquireAttempts
  waiting), teleport data for the deployment, and the raid's own DamageService rules.
- Known minor issues: the locker's 10-column grid scrolls sideways on small viewports; the client mirror only
  updates from its own requests (fine in play, misleading in scripted tests).

## Status at pause (usage limit reached, 2026-10-16)
- B done. C done; review fixes applied for all 6 confirmed findings. E1 done. Tests: 98/98 passing.
- D: server side done and verified live (creation, kit, chits, critical save, spawn, appearance, safe
  zone, damage gateway, out-of-bounds return, missing-root respawn). Creator UI written
  (`CreationScreen`, `ScreenManager`) but NOT yet verified working.
- **Open bug (next step):** the client stays on "LOADING SURVIVOR" while the server session is
  "ready". Likely cause: the late initial `GetState` response overwrites the "ready" push.
  Planned fix: add a per-session status sequence number to pushes and GetState responses, and have
  ClientState ignore stale updates. Then re-verify the creator UI with mouse and gamepad.
- Not started: E2 (inventory/locker UI), F (vendors/EconomyService/Config.Vendors, HUD), G (acceptance).
- User actions pending: enable Studio API access; set Lighting.Technology = Future; save/publish
  the place; review the two third-party plugins (cloud ids 4771536187, 7352299243) that inject GUI
  scripts into playtests.

## Detail pass: catalog avatars and NPCs (user request, 2026-10-16)
- [x] Curate Roblox Marketplace survivor items (shirts, pants, hair, beards, hats, bandanas, NPC vests/packs); lookbook-verified on the blocky R15 body
- [x] Config.Appearance v2: catalog options (skin, hair, facial hair, headwear, face cover, top, 3D outerwear, bottom, gear); option ids only over the wire
- [x] DevTools.AppearanceLibrary: fetch assets into Shared/Assets/Appearance at edit time (scripts/joints stripped; layered items via HumanoidDescription pipeline); NPC-only gear in HubAssets.NpcGear
- [x] AppearanceApplier: clone + weld library items (works on anchored preview and live rigs)
- [x] Profile schema v2 + v1 appearance migration; verified on the real Studio DataStore profile
- [x] Kit.npc: 10 Humanoid NPCs in catalog gear (vendors on a serving step, guards, clerk, medic, mechanic, camp survivors); NpcService idle animation
- [x] Rebuild hub (5,132 parts, 76 lights); unit tests 103/103; creator ViewportFrame renders layered jackets
- [ ] Building shells still read as boxes: dedicated architecture detail pass (recessed windows, frames, rooflines, generated props/materials)
- [ ] Navigation re-check after the Detail section (new props could block paths)

## Building detail pass (in progress)
- [x] Fixed Kit.box: ~300 lines of helpers had been nested inside it (defined only on first box call, redefined every call)
- [x] generate_material works again (sync); PrisonConcrete done. Async jobs: StainedFloor 830ea905, CinderBlock 0a503cbb, RustedCorrugated f7aaca78, YardDirt 66e3643a
- [ ] Generated prop meshes (async jobs, store in HubAssets, use via Kit.prop with box fallback):
  Dumpster b6c188ba, Generator fc560f93, OilDrum 0f97b955, Crate 6a8bdc63, PalletStack fa37edc6, PicnicTable 9c90f6c9,
  Bench 99666571, HospitalBed d92ae363, Workbench 1723fe44, SandbagWall 479de9d8, JerseyBarrier 36eae730,
  BurnBarrel 3770440f, HVAC 8d0cd9a8, RazorCoil 6d54f2f0, SecurityCamera 2122f97e, UtilityBox 7634796b
- [ ] Kit facade helpers (window, doorway, parapet, plinth, stain, patch, vent, paint, camera, utility box, razor wire)
- [ ] Per-section detail workflow (each agent edits only its section; dry-run build in isolation)
- [ ] Rebuild, visual review, navigation re-check

## Resumed 2026-10-17
- [x] Previous section-detail workflow had failed on the usage limit before any agent ran; Studio state verified (Kit facade helpers present, 5,075 parts, 5/5 materials done)
- [x] Async mesh batch: 4/16 had succeeded (Dumpster, RazorCoil, UtilityBox, SecurityCamera); 12 failed "Unable to generate Model" (batch too large). Retrying 3 at a time: OilDrum, Generator, Crate, PalletStack, PicnicTable, Bench, HospitalBed done
- [x] All 16 prop meshes generated and stored in DevTools.HubAssets (crate and bench regenerated: first crate had nautical anchors, first bench glossy red)
- [x] Backups: DevTools.SectionsBackup_20261017, DevTools.KitBackup_20261017
- [x] DevTools.SectionGate: rebuilds a section from backup vs current and reports collidable/gameplay/rng/budget/buried diffs (negative-tested; ignores Studio "Assistant*" tags)
- [x] DevTools.LeakProbe: sphere (player-size) + thin-ray sweeps through old vs new builds; reports new gaps
- [x] Section detail: workflow v2 agents were cut off by the usage limit mid-run (reviewers never ran). Audited all sections inline: CellBlock, Supply, WestWing, Yard, FrontField complete and gated; Admin had 5 open ground-floor windows + 13 unsealed strip windows → sealed with Kit.window + full facade dressing. LeakProbe: 0 gaps in every section.
- [x] Kit mesh swaps with collision parity (crate, drum, fire barrel, bench, picnic table, jersey, sandbags, hvac)
- [x] Section props placed (afternoon session): HospitalBed (medical beds), Workbench (workshop), Generator, PalletStack, Dumpster
- [x] Rebuild, screenshot review per section; playtest navigation re-check (quartermaster, armory, vault terminal, medical, workshop, cell-block catwalk, pond bank all reached; no game-code errors)
- [ ] Unit tests not re-run (no server code changed this session)

## Realism pass (user request 2026-10-17: "as realistic as possible e.g. water reflection, real foliage")
- [x] Lighting: LightingStyle Realistic, full environment diffuse/specular, softer shadows, lighter grade, far-only depth of field, GlobalWind (7,0,3)
- [x] **Fixed a project-wide bug:** terrain rendered 1.5 studs above every floor (fill top -0.5 draws at +1.5), burying floors, plinths, bunks, crop rows and low props. Terrain now filled to -2.5 → surface -0.5.
- [x] Walls: generated CmuWall block masonry at 9 studs/tile; poured-concrete trim (PrisonConcrete) at 18 studs/tile
- [x] Water: terrain pond in the front field (x 64..86, z 56..86) written via liquid channel; reflective organic puddles (Kit.puddle) in the yard; reflective road ruts
- [x] Foliage: 12 PBR pine variants (Letaij, Creator Store, script-free) replace block pines (Kit.pine, rng-neutral); new Nature section: 48 near pines, 70 bushes/ferns, fence weeds, pond plants, 16 ivy patches (Kit.ivy); StreamingMesh LOD on trees
- [ ] Not possible from scripts: Lighting.Technology (user sets Future in Studio)
- [x] Terrain ground, rain and the day/night cycle were done in the afternoon session (see below)

## Living hub (user request 2026-10-17: "i want it to feel alive ... gta5 feels alive") — PARKED
- [x] Design written inline: docs/living-hub.md (5 steps; cosmetic, client-side, props found by name)
- [x] Step 1 soundscape: `Config.Ambience` + `Controllers.AmbienceController` (in ClientMain), verified in the
      G playtests (ids load, 24 prop loops, ravens, indoor switch and reverb, no errors). User to judge volumes by ear.
- [ ] Steps 2–5 (NPC routines, VFX, crows, day/night + events) parked. Revisit scope first: the user
      says the hub already has "too much going on", so the pass should favour sound and motion over more stuff.

## Hub polish backlog (user 2026-10-17: "tackle this later … we need to make more headway with other things first")
Make all changes in DevTools.HubBuilder sections/Kit, never in Workspace.Hub (a rebuild overwrites it).
- [x] **Signage:** superseded by the user's "get rid of all the signs" (2026-10-17): all 71 removed, wayfinding and
      function labels included. See "Signs out, boulders in" below.
- [ ] **"Too much going on":** declutter the exterior (prop/foliage/dressing density); give the yard a clear focal
      hierarchy so vendors, lockers and the gate read first.
- [ ] **Plain interiors:** interior dressing pass (furniture, wall detail, lighting variety) for the Quartermaster and
      Armory rooms, Services lobby/registry, cell blocks, medical and workshop. With the signs gone, the vendor
      partitions above the service windows are now bare walls.
- [ ] User action: set Lighting.Technology = Future (Explorer → Lighting → Properties → Technology), then save/publish.

## Living hub + realism, afternoon session 2026-10-17 (recorded afterwards from its transcript)
User: "do these" (living-hub pass, missing props), then fog/rain, realistic water, "a thunder storm with lightning",
TWD easter eggs, and "the courtyard ground is 2d it looks horrendous in first person".
- [x] Five missing props placed as meshes with box-collision parity (gated, rebuilt; hospital beds screenshot-checked)
- [x] Day/night: Shared.Rules.WorldClock + Config.World + Controllers.WorldController; build-time light tags
      (NightLamp, InteriorLight, FireLight); NPCs stream atomically and carry their hip height
- [x] Weather: fog, rain, thunderstorm with visible bolts and distance-delayed thunder (licensed sounds); puddles
      and ruts get wetter in rain. User: "storm looks good"
- [x] Ground: flat textured slabs replaced by terrain (Sections.Surface): dirt, mud, real grass, relief. A pebble/
      stone/rubble scatter was added, then removed at the user's request ("just get rid of the rocks")
- [x] Unit tests 134/134 (9 new WorldClock_Test), run before the ground work
- [ ] Stopped (connection dropped) at the start of NPC routines; nothing changed for it
- [ ] Not started: night/wildlife ambience, crows, subtle TWD easter eggs, pond reflection trick, verify pass
      (playtest, perf, navigation, tests)

## Signs out, boulders in (user 2026-10-17: "add some bigger rocks not just pebbles... get rid of all the signs and
## make the entire hub look more realistic")
- [x] Signs: every Kit.sign and Kit.paint call removed from Yard, CellBlock, Supply, WestWing, Admin and FrontField,
      plus what only held or lit a sign (notice-board posts, garden sign post, gate-gantry sign frame and its two
      NightLamp spots, wire ties). Kept: grime stains, hazard striping. Kit.sign/Kit.paint stay in the Kit, unused.
      Backup: DevTools.SectionsBackup_20261017c
- [x] Verified: hub text objects 71 -> 0; parts 7,342 -> 7,247; lights 81 -> 79 (NightLamp 29 -> 27); Vendor 2,
      VaultTerminal 2, HubSpawn 8, Npc 10 unchanged. SectionGate on all six: shared rng unchanged, only sign parts
      removed, no buried decor. LeakProbe flagged 1,544 deeper sweeps; every one traced to a removed sign
      (freestanding or hung under a canopy/gantry), 0 unexplained
- [x] Boulders: generate_mesh shapes looked cartoonish (painted cracks, neon moss); kept the shapes, cleared the
      textures, built-in Rock / Slate / Granite materials. Templates DevTools.HubAssets.BoulderA/B/C
- [x] Nature section: 9 boulders placed first (3 on the pond banks, 6 in the grass beyond the east and south fences),
      sunk and tilted, hull collision; pond plants skip covered spots while keeping their random draws (9 plants
      dropped, every other plant unmoved). Gate PASS. Screenshots: pond, east fence, cell-block front
- [x] Camera bug root cause: the Studio MCP tools set the edit camera Scriptable ~0.05 s after every call. Each
      call now ends with a delayed reset to Fixed (tasks/lessons.md)
- [ ] Not done: playtest walk past the pond boulders; user to judge the boulders and say if they want more
- [x] Realism: asked what reads as fake first; user answered "yes" to the list (siding walls, sphere bags, disc
      puddles, toy bus/dumpster) and named the game **No Dawn**. Planned below as "Realism pass v2"

## Realism pass v2 (user 2026-10-17: "its time to do the realism pass"; "the garage or vehicle workshop needs to look way better its so bare")
Survey (7 first-person Edit-mode shots, camera restored after): walls read as wooden siding (CmuWall); poured
concrete (PrisonConcrete: plaza, guard hut, columns) carries big cartoon "painted" cracks; walkways are bright
paper-thin strips; puddles are stacked blue discs; trash bags are black balls; chain-link is a grey see-through
sheet; terrain grass blades are huge ribbons; every generated prop (benches, barrels, crates, dumpster, jersey
barriers, sandbags...) has the generator's toy textures; the bus is 13.5 studs long (a real one is ~36), the pickup
and sedan are toy-scale too; the workshop is a dark, nearly empty box (car on stands, 4 benches, 2 tool boards).

Technique, proved before planning: PBR maps authored in Python at real-world proportions (texgen/ in the
scratchpad), served on localhost, `upload_image`, then a MaterialVariant. A/B on the cell-block wall read as real
block masonry. `MaterialService.NoDawn.CmuWallV2` exists but nothing uses it yet. Creator Store is still mostly spam
and impersonated "Roblox" accounts, so nothing is sourced there.

Rules carried over: texture relief only for millimetre-deep detail (block joints, concrete pores); anything deeper is
geometry. No signs or text. Collision parity checked with SectionGate, LeakProbe 0 unexplained, layouts rng-neutral,
shared modules edited on a draft copy first, backups before edits, camera protocol on every Studio call.

### Stage 1 — Surfaces (everywhere you look) ✅ (details and rulings: tasks/realism-v2-ledger.md)
- [x] Walls: NdCmuExt (weathered painted block, 16 x 8 in running bond, 5.8 studs/tile); interiors NdCmuInt
- [x] Poured concrete NdConcrete (no cartoon cracks): columns, bands, roofs, plaza, hut, sills, lintels
- [x] Walkways as real slabs (Kit.walkway): joints, settling, cracked slabs, broom finish, darker; plaza ring paint faded
- [x] Chain-link: see-through wire texture both faces (Kit.wireMesh), round pipe posts; gate leaves too
- [x] Puddles: terrain water tried and dropped ("looks like the ocean"); muddy hollows + NdWetMud standing water instead
- [x] Grass: Terrain.GrassLength 0.4, set by the client at start
- [x] Boarded windows in weathered grey plywood; hazard striping matte and faded
- [x] User additions mid-stage: permanent foggy rainy night (Config.World.Fixed, rainfog, day-for-night render hour,
      night skybox), calm pond water, basketball court removed
- [x] Verify: gates + leak probes, night captures (Edit + play), unit tests 137/137

### Stage 2 — Props off the toy textures ✅
- [x] Tents in the camp and the stand outside the garage (the sorting stall) rebuilt to look real (user request)
- [x] Trash bags: generated knotted bag shapes in a custom crinkled black plastic (NdBinBag)
- [x] Benches, picnic tables, crates, drums, jersey barriers, dumpster rebuilt from parts with PBR materials; other
      generated props off their cartoon textures (Kit MESH_LOOKS)
- [x] Burn barrels: particle flames, smoke and sparks instead of the cartoon Fire
- [x] Verify: gates, leak probes (all attributed), cross-model buried check, captures

### Stage 3 — Vehicle workshop
- [x] First version (previous session, cut off by the usage limit before review): sedan, lift, hoist, engine stand,
      tool chests, benches, pegboards, tyre rack, compressor, welding cart, jack, creeper, drain pan, jerry cans, lamps,
      all built from parts. Review 2026-10-17: still reads as blocks (engine = boxes, car = box, jack = brick), and dark.
- Superseded by the "Realism pass v3" below (user 2026-10-17: "pick up where we left off, make the garage and other
  rooms and the farm more realistic").

### Stage 4 — Vehicles at real scale (2026-10-18; details and rulings: tasks/realism-v2-ledger.md)
- [x] Prison transport bus, 36 x 8.7 x 10 studs (10 x 2.5 x 2.8 m), Kit.busAsset: the generated flat-fronted coach
      (HubAssets.Props.PrisonBus) stretched to length with round wheels; barricaded with welded steel plates over the
      lower sides, bars over the window band and razor wire along the roof; flat front-right tyre with the body leaning
      onto it. Moved east-west in line with the east jersey barrier (x 66..102, z 98..107): along the old line it
      would have stood in the pond
- [x] Yard pickup at real size, Kit.pickupAsset: generated 1990s regular-cab truck (PickupGreen), 17.6 x 7.2 x 6.4
      (4.9 x 2.0 x 1.8 m), jerry cans and a crate in the bed, nose toward the field gate at (47, 36)
- [x] Wrecked sedan by the west fence (SedanBlue through Kit.sedanAsset): real proportions, hood up over the engine,
      front-right wheel off and lying in the grass, the body settled onto the bare brake disc (new `settle` option).
      Moved north to (15.5, 89.5): at full length it would have overlapped the west jersey barrier
- [x] No text: the parts fallback bus (Kit.bus) lost its "COUNTY TRANSPORT" board; the board is blank
- [x] Verify: SectionGate Yard and FrontField "shared rng: unchanged", no gameplay instance missing or new; WestWing
      PASS (the garage car through the edited Kit.sedanAsset); 0 parts within 0.6 of any new collider; pathfinding
      reaches the main gate, pen gate, behind the bus, garden, armory door, field gate and the sandbag post
- [ ] Captures: bus (front, side, rear, close-up) and pickup taken once Studio rendered again (Obsidian vault
      Screenshots/stage4); the wreck still needs one, then Stage 4 is done

### Stage 5 — Final verify
- [ ] SectionGate on every section, LeakProbe, budget (now 7,247 parts / 79 lights)
- [ ] Playtest walk (spawn → vendors → lockers → workshop → gate), console clean, unit tests if runtime code changed
- [ ] Before/after shot set; tracker and lessons updated

Not in this pass (follow-ups): the other interiors (Quartermaster, Armory, Services, cells, medical), decluttering
("too much going on"), the No Dawn UI.

## Realism pass v3: garage, rooms, farm (user 2026-10-17: "pick up where we left off, make the garage and other rooms
## and the farm more realistic")
Technique, proved before planning: `generate_mesh` with "realistic" prompts and explicit part segmentation now gives
props that read as the real thing (test: V8 engine on its stand, a 1990s sedan with chrome bumpers and hubcaps), far
past anything built from boxes. The floor-jack prompt came out upright and wrong, so every prop is checked in a capture
before use. Blocked routes, checked: `AssetService:CreateAssetAsync` ("not available yet" from MCP code), so no mesh
upload from Blender; the Blender bridge is not running. Parts stay for simple structure (shelving, rails, conduit,
fixtures, walls). Scale: 1 stud ~ 0.28 m.

Pipeline: generate (max 3 jobs at once) -> check in a capture -> normalise into `DevTools.HubAssets.Props.<Name>`
(pivot at bottom centre, front -Z, generation tags stripped, real-world size, materials per part: Metal for bare steel,
Plastic for paint, Glass for glass) -> a `Kit.asset` helper places it with an invisible box collider where a person
must be blocked, decor-only otherwise, and returns nil if the template is missing so the parts fallback still builds.
No text or logos on any prop (check each texture); rng-neutral layouts (own Random per room); backups first.

### A. Garage (WestWing Workshop)
- [x] Generated hero props: the weathered sedan on the lift (stretched 1.22 to real proportions, wheels kept round; hood
      turned up on its hinge over the generated engine, front-left wheel off and lying on the floor, rusty disc and
      caliper showing), engine on its stand, floor jack, tool chests, welding cart, compressor, engine hoist, tyre
      stacks, workbenches, stocked shelving (regenerated without the pseudo-text oil labels), pegboard tools
- [x] Walls and ceiling: steel joists, conduit runs and drops, outlets, the shop panel, copper air line with couplers,
      hose reel, extinguisher; twin-tube fluorescent battens (Kit.ceilingLight, hub-wide, same collision and light)
- [x] Lighting: six bright battens plus one by the south wall, with a weak bounce light each (no more black ceiling)
- [x] Oil stains as irregular glossy blotches (Kit.oilStain, keeps the ctx.rng draw); drums lose the corrugated
      variant (it smeared into a pinwheel on the lids)
- [x] Verify: SectionGate WestWing (rng unchanged), captures sent

### B. Other rooms (and the med bay crowd the user asked for mid-pass)
- [x] Medical ward: 9 generated beds, pleated curtains on rails (some drawn), IV stands with lines, exam couch behind a
      screen, instrument trolleys, oxygen trolleys, medicine cabinets, nurse station with a warm lamp, wheelchairs,
      greyed tile. 15 NPCs: 6 patients asleep under rumpled blankets (NpcStill), one propped up, one with a head wound
      on the bed edge, one examined on the couch, one waiting in a wheelchair; 4 medics (masks, blue gloves,
      stethoscopes) tending, checking a drip, with a clipboard, examining; the nurse at her station
- [x] Quartermaster: generated supply shelving, garment rack with boots; trader and a waiting customer in the queue
- [x] Armory: wooden rack of real rifles (NpcGear meshes), ammo stacks, generated armory shelving; a customer at the cage
- [x] Services: panelled registry counter with CRT and ledger, waiting chairs with people, water cooler, dead plants;
      generated locker banks with benches; offices of steel desks, chairs (one tipped), filing cabinets, CRTs, loose
      papers and a record keeper under a lit lamp; the checkpoint guard's desk
- [x] Cell block: all 64 cells get the steel double bunk and toilet-sink unit (every ctx.rng draw kept); guard-hall
      CRTs with a screen glow and a guard watching them; three residents (asleep, sat on a bunk, at the bars)
- [x] Verify: gates on Supply, Admin, CellBlock (rng unchanged, no vendor / terminal / spawn missing), captures sent

### C. Farm (FrontField garden and animal pen)
- [x] Crops on matte, flattened soil ridges: part-built corn (generated corn and tomatoes came out cartoonish), staked
      tomatoes with fruit, potato and bean foliage, squash with pumpkins, seedlings (PBR foliage library at crop scale)
- [x] Garden kit: weathered picket fence with an open gate (invisible panels keep the old collision line), IBC tote,
      drums, wheelbarrow, hay bales, spade / rake / hoe on the fence
- [x] Animal pen: round-post split rails, steel stock trough, corrugated lean-to with rafters and a plank back, hay,
      four pigs (second prompt; the first pig was a cartoon)
- [x] Verify: FrontField gate (rng unchanged), captures sent

### D. People (user, mid-pass: "fill the med bay up with patients, and doctors treating them and other npcs in the
### courtyard and surrounding areas")
- [x] Kit.npc poses (lie, sitUp, sit, sitSlump, tend, crouch, kneel, warmHands, carry, point, clipboard, reach,
      sitGround) on the AnimationConstraint attachments, solved at build time so held props and bandages sit right;
      rest heights, idle = false (NpcStill), medic gear, bandages, held props
- [x] New section People (own Random): courtyard fire barrels, benches, picnic tables with a meal, trading stall, a
      crate carried to the Quartermaster; camp; vendor queues (window centres kept clear for players); cells; garage
      apprentice; farmers and the pig feeder; gate guards. 59 NPCs hub-wide (52 breathing, 7 asleep)

### E. Final verify
- [x] Full rebuild: 10,488 parts, 109 lights. Gates: shared rng unchanged in WestWing, Supply, Admin, CellBlock,
      FrontField; no gameplay instance missing (the Medic moved). Playtest: server ~63 fps, client ~57, idle playing
      on all 52, posed rigs hold their poses, no game-code errors
- [ ] Not run: LeakProbe (no wall or perimeter edits), unit tests (no runtime code changed), a walked navigation pass
- [x] Lessons, ledger, memory updated

## Review
- 2026-10-17 v3: the generated-prop pipeline (generate_mesh -> PropTools.normalize -> Kit.asset) replaced the
  box-built props in the garage, ward, supply rooms, Services and cells; 40 props in HubAssets.Props (6 rejected
  generations kept in DevTools._RejectedProps; the Workspace staging folder removed). Organic subjects
  (crops, the first pig) come out cartoonish, so crops use the PBR foliage library and parts.
- Open: Stage 4 vehicles (SedanBlue is ready for the yard wreck; the bus and pickup are still toy-scale); the
  Quartermaster's customer side is still bare along its walls; the user should save the place.
- 2026-10-18 Stage 4: bus, pickup and wreck rebuilt at real scale (10,693 parts, 109 lights, 0 text, gameplay tags
  unchanged); gates and paths verified. Captures still to take and send (viewport not rendering); save the place.
