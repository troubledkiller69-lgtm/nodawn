# SDD ledger — plan: tasks/todo.md (Realism pass v2)

## Setup (2026-10-17)
- Backups in ServerStorage.DevTools: SectionsBackup_20261017d (12 section modules), KitBackup_20261017d (65,559 chars),
  WorldControllerBackup_20261017d (19,552 chars). Rollback = copy them back + rebuild.
- SectionGate / LeakProbe: new optional 4th argument `oldKit` (a DevTools Kit backup) so Kit edits are diffed.
  Control: Ground vs identical Kit → GATE PASS, 0 diffs. Negative: baseline Kit with wider chain-link posts →
  GATE REVIEW, 255 collidable posts changed (caught).
- Ruling: no git repo (the project lives in the Studio place) — DevTools backups stand in for commits — cost if wrong:
  none; rollback is restore + rebuild.
- Ruling: build code (Kit/Sections) is verified by the deterministic gates (SectionGate with oldKit, LeakProbe,
  rebuild counts) plus first-person captures instead of unit tests; runtime Lua changes get a unit test first in
  UnitTest.Cases where the logic is testable — cost if wrong: a visual regression the gates cannot see, caught at the
  capture review.
- Ruling: screenshots go to the user after each stage without pausing (user: "send screenshots after each stage").
- Pre-flight: every stage edits the Kit and rebuilds the hub, so stages run strictly in order; one rebuild per stage.
- Camera protocol on every Studio call (delayed reset to Fixed; captures set CFrame + Focus, restore both).

## Stage 1 — Surfaces
- Materials authored (texgen/stage1.py, wetmud.py, nightsky.py) and uploaded: MaterialService.NoDawn.NdCmuExt
  (walls), NdCmuInt (interiors), NdConcrete (columns/roofs/plaza), NdSlab (walkways), NdWetMud (overrides Mud),
  chain-link Texture 101336812057315, night skybox (6 faces). Part Color multiplies the colour maps.
- Kit: palette on the new variants; Kit.wireMesh + round 0.42 pipe posts in Kit.chainLink; Kit.walkway (real
  slabs, 0.06 joints, settle, 14% cracked); Kit.puddle removed. Sections: Yard (walkways, puddle declarations with
  the same rng draws, gate leaves wired, court removed), FrontField (rut puddles, gate mesh wired), CellBlock (last
  PrisonConcrete patch), Surface (puddle hollows, Mud override), Atmosphere (authors the pinned night).
- Runtime: Config.World rainfog state + Fixed {Hour 23.2, Weather rainfog, RenderHour 13} + Terrain.GrassLength 0.4;
  WorldClock.clock()/weather() honour Fixed, new renderClock(); WorldController renders at renderClock and sets
  GrassLength; AmbienceController reads the world hour.
- Ruling: terrain-water puddles dropped — 4-stud voxels draw small puddles as flat rectangles that ripple ("looks like
  the ocean", user) or hide them; flat disc parts still read as discs — standing water now lives in the NdWetMud
  terrain material (mirror pools in the mud), in carved muddy hollows — cost if wrong: pools repeat with the 10-stud
  tile (Organic pattern softens it).
- Ruling: walkway slabs collide (tops 0.10) so feet rest on concrete — cost: none seen; tiny step at slab edges.
- Ruling: user's mid-pass requests folded into Stage 1: permanent night + rain + fog, basketball court removed; tents
  and the stand outside the garage go to Stage 2 — cost if wrong: none.
- Ruling: day-for-night. The engine draws the sky black after dark whatever the skybox, and a dark-coloured
  atmosphere blacks out sky and distance, so the renderer runs at RenderHour 13 (Brightness 1.2 as moonlight, night
  ambient, lit grey-blue fog, overcast night skybox, no sun/moon disc, clouds off); lamps and sound beds stay on the
  23.2 world hour — cost if wrong: hard "moon" shadows from a high angle; revisit if the engine's night sky changes.
- Ruling: fog test threshold restated (density >= overcast + 0.08, not >= 0.5): lit fog above ~0.5 goes black —
  cost: none.
- Ruling: AmbienceController call sites switched to WorldClock.clock() without a unit seam (covered by playtest
  only) — cost if wrong: night sound beds misfire; check by ear.
- Gates (oldKit KitBackup_20261017d): Yard REVIEW = posts, 80 slabs, court removed; rng/gameplay/buried unchanged.
  FrontField REVIEW = posts, ruts removed. Ground REVIEW = 255 posts only. CellBlock, Supply, WestWing, Admin,
  Detail, Nature PASS. LeakProbe Yard 4 / FrontField 4 thin-ray sweeps beside narrower posts above the panel top —
  all explained, 0 player gaps. (Gating Ground rewrites live terrain: always rebuild after.)
- Build: 7,254 parts (was 7,247), 79 lights; Vendor 2, VaultTerminal 2, HubSpawn 8, Npc 10, NightLamp 27; 0 text.
- Stage 1: complete (tests: RunUnitTest() on a playtest server → 137/137 pass, 3 new in WorldClock_Test; visual:
  night captures in Edit and in play)

## Stage 2 — Props
- Rebuilt in the Kit from parts with Roblox PBR materials (old primitive collision kept as invisible parts so
  movement is unchanged): Kit.bench, Kit.picnicTable, Kit.tent (canvas ridge tent: strips with sag, back wall,
  rolled door flaps, poles, guy lines + stakes, bedroll + pack), Kit.jersey (safety-shape profile, 4-stud
  segments). Real collision changed only where the visual is the collider: Kit.crate (WoodPlanks panels + corner
  posts + battens), Kit.drum / burn barrel (shell + hoops + chimes; barrel: draught holes, ember disc, particle
  flames/smoke/sparks replacing the Fire object), new Kit.dumpster (front-load, lids, pockets, skids).
- Trash bags: 3 generate_mesh shapes; 1 discarded (crumpled sheet); TrashBagA/C stored in HubAssets with the new
  NdBinBag material (crinkle normal, glossy). Kit.trashBags keeps every rng draw; spheres become invisible colliders.
- Remaining generated meshes (sandbags, generator, workbench, hospital bed, HVAC, utility box, camera, pallet stack,
  razor coil) lose their cartoon textures for Roblox materials (Kit MESH_LOOKS); unused HubAssets left in place.
- Yard stall outside the garage rebuilt (user request): timber poles + lashed beams + braces, two-panel sagging tarp
  with a drip emitter, plywood trestle table, salvage, hanging lantern (PointLight, tag FireLight → 80 lights).
- Ruling: painted steel uses Plastic, not Metal — Roblox Metal renders near-black at night and paint is a non-metal
  coat — cost if wrong: painted props look slightly less glossy.
- Ruling: walkway ends pulled 0.4 short of the facade so the last slab no longer buries the doorway threshold plates
  (found by a cross-model buried-decor check) — cost: none.
- Ruling: SectionGate "buried decoration" flags are expected now (visuals wrap invisible colliders inside their own
  prop); replaced for this pass by a cross-model check on the built hub: 3 found (thresholds), fixed, 0 left —
  cost if wrong: a same-model burial goes unseen (checked by eye in captures).
- Gates (oldKit): rng unchanged in every section; gameplay unchanged except +1 tagged lantern light; collidable
  changes only crates/drums/barrels/dumpster/stall/posts/slabs/court. LeakProbe CellBlock 0; Yard 516 deeper sweeps,
  all attributed (court 368, stall tarp/table/goods 144, fence-post hairline 4), 0 unexplained.
- Build: 7,874 parts (+620 over Stage 1), 80 lights; Vendor 2, VaultTerminal 2, HubSpawn 8, Npc 10, NightLamp 27.
- Stage 2: complete (no runtime code changed, so no unit run; visual: tents, stall, bags, picnic tables, barrel
  captures)

## Stage 3 — Workshop
- Stage 3 first version (previous session) built the garage from parts; superseded by Realism pass v3 below.

## Realism pass v3 — garage, rooms, farm, people (2026-10-17)
- Backups: DevTools.KitBackup_20261017e, SectionsBackup_20261017e (12 modules), WorldConfigBackup_20261017e.
- Ruling: generated meshes for complex man-made props, parts for simple structure — test shots showed the generated
  engine and sedan reading as real while the parts versions read as boxes — cost if wrong: texture memory (40 props).
- Ruling: no mesh upload pipeline — AssetService:CreateAssetAsync returns "not available yet" from MCP code and the
  Blender bridge was not running; every mesh comes from generate_mesh (published to the user's inventory) — cost: none.
- Tools: DevTools.PropTools (normalize: flatten segment sub-models, strip generation tags, measure real bounds from
  mesh vertices via CreateEditableMeshAsync, pivot at bottom centre, front -Z, Width/Height/Depth attributes) and
  Kit.asset (scale by height/width/depth, per-axis stretch with keepShape, back = wall-line placement, invisible box
  collider, nil without the template so every caller keeps its parts fallback). Kit.sedanAsset (hood on its hinge
  over the generated engine, a wheel off), Kit.oilStain, Kit.blanket (stretched rumpled-blanket mesh, recoloured by
  clearing its texture), Kit.curtain, Kit.ivLine, Kit.cornStalk, Kit.cropRow kinds.
- Ruling: rejected 6 generations: floor jack v1 (upright), shelving v1 (pseudo-text oil labels), single tyre
  (malformed), tomato and corn (cartoon), pig v1 (cartoon) — kept in DevTools._RejectedProps.
- Ruling: lying patients carry tag NpcStill instead of Npc, so NpcService (unchanged) plays no idle on them; no
  runtime code changed, so no unit run — cost if wrong: a sleeper might be wanted to breathe later.
- Ruling: poses live on the AnimationConstraint Attachment0 (the rigs have no Motor6Ds); the build solves the joint
  chain itself so held props, masks and bandages weld to the posed limbs; posed humanoids get PlatformStand and
  EvaluateStateMachine = false — checked in play: poses hold under the idle animation.
- Ruling: rng neutrality kept by construction: Supply still builds the old parts shelving into a scrap folder (same
  draws), CellBlock keeps the upper-bunk draw, the crop rows keep two draws per plant, People/Medical/Garden/Pen use
  their own Random.
- Gates (backup SectionsBackup_20261017e, oldKit KitBackup_20261017e): WestWing, Supply, Admin, CellBlock, FrontField
  all "shared rng: unchanged", no vendor / vault terminal / spawn missing; collidable diffs are the swapped props,
  the new colliders and the NPC bodies. Parts: WestWing 896 -> 1351, Supply 607 -> 562, Admin 693 -> 851, CellBlock
  3057 -> 3303, FrontField 626 -> 1478. Hub 10,488 parts, 109 lights, 59 NPCs.
- Playtest: server ~63 fps, client ~57 fps (Studio cap 60), idle playing on all 52 Npc. Console errors only from
  Studio plugins cloud_4771536187 (a shop/trading/chat UI kit) and cloud_7352299243 ("Base Qi" UserThumb), which are
  not in the place.

## Stage 4 — Vehicles at real scale (2026-10-18, side session)
- Backups: DevTools.SectionsBackup_20261018 (all 13 modules), KitBackup_20261018 (145,718 chars). Kit edited on
  DevTools.KitDraft and the two sections on DevTools.SectionsDraft, test-built, then promoted (UpdateSourceAsync).
- Generations (7, one at a time or two in parallel): PickupGreen (regular cab, kept), PickupExtCab (extended cab,
  parked), three buses (prison bus, 40 ft school bus, 12 m coach) and one prompt refused with "Character limit
  exceeded" (~640 characters; 437 worked). Parked in DevTools._RejectedProps: PrisonBusShort, SchoolBusShort,
  PickupExtCab. New templates: HubAssets.Props.PrisonBus (the coach), PickupGreen; both carry measured attributes
  (bus: WallX, SkirtY, RoofY, WindowBottom/Top; pickup: BedFloor, BedFront/Back, BedHalfWidth, RailTop, CabFront/Back).
- Ruling: generate_mesh cannot make a long vehicle: every bus came out 1.7-1.9 times as long as wide (14-16.5 studs
  at 8.5 wide; a real one is 4.2), whatever the prompt or size box, and part-built bodies read as boxes (v3 ruling).
  So the flat-fronted coach is scaled 1.195 and stretched 2.02 along its length: its front and back faces
  (windshield, grille, lights) lie across the stretch and keep their detail, the continuous window band stays
  continuous, and the stretched lower sides and wheel arches are covered by welded steel plates, which also make it
  the barricaded bus the brief asks for — cost if wrong: a smeared texture on the band above the windows (to judge
  in captures).
- Ruling: the bus moved from (76, 96) along Z to (84, 102.6) east-west, nose west to the road, in line with the east
  jersey barrier and the burn barrel (a barricade line across the field; the road stays open). Along the old line a
  36-stud bus would stand in the pond or under the gate tower. Gaps: 3.9 studs to the burn barrel, 8 to the pen, 23
  to the south fence; the tower deck (20.3) is out of jump reach from the roof (10) — cost if wrong: the field reads
  differently; paths round both ends verified.
- Ruling: the pickup is the regular-cab generation stretched 1.26 (the sedans' 1.22 precedent) to 17.6 studs, a real
  4.9 m regular-cab truck; nose to the field gate at (47, 36), clear of both lanes and the sandbags, its tail corner
  7.1 studs from the collidable armory awning (top 11.0): from the tailgate (4.6) a jump reaches about 5.9 studs out,
  so the truck is no step up onto it — cost: none.
- Ruling: the wreck (SedanBlue, real proportions) moved from (13, 100) to (15.5, 89.5): at full length it would have
  overlapped the west jersey barrier. Nose 1.8 studs from the west fence; 4.6 tall, so no climbing aid.
- Ruling: a body leans onto a flat tyre or a bare brake disc on its springs: only the body rotates, the wheels stay
  planted (leaning the wheels too sank two of them 0.1-0.25 into the ground). Kit: leanOnto, flatTyre, isWheel;
  Kit.sedanAsset gains opts.settle (the garage car does not use it: WestWing gate PASS, 0 diffs).
- Ruling: Kit.bus (the parts fallback) loses its "COUNTY TRANSPORT" sign for a blank board, and the toy meshes
  (HubAssets.Bus, Pickup) leave the fallback chains (left in HubAssets, unused); Sedan stays the wreck's fallback.
- Gates (backup SectionsBackup_20261018, oldKit KitBackup_20261018): Yard REVIEW, parts 823 -> 852, shared rng
  unchanged, gameplay unchanged, collidable: toy pickup out, 2 colliders and the bed crate in; FrontField REVIEW,
  1478 -> 1654, rng unchanged, gameplay unchanged, collidable: toy bus and sedan out, 2 colliders in. The "buried"
  flags (27 / 21) are each vehicle's own visuals inside its own collider (expected, Stage 2 ruling). WestWing PASS.
- Checks on the rebuilt hub: 0 parts within 0.6 of any new collider (NPCs, Detail, Nature included); pathfinding
  (radius 1.5) field gate -> main gate, pen gate, behind the bus; booth -> pen; plaza -> armory door, field gate,
  sandbag post; road -> beyond the wreck: all Success. The garden gap needs radius <= 1.0 (drums, hay and the IBC
  tote by its gate; pre-existing, players fit).
- Build: 10,693 parts (+205), 109 lights, 0 text; Vendor 2, VaultTerminal 2, HubSpawn 8, Npc 52, NpcStill 7,
  NightLamp 27 unchanged.
- Captures: not taken. After the usage-limit break every screen_capture came back blank white (only the view cube
  drawn), even from the user's own view, so the viewport was not rendering. Visual check still owed.
