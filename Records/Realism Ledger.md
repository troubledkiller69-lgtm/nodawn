---
tags: [nodawn, record, ledger]
updated: 2026-10-21
---

# SDD ledger — plan: tasks/todo.md (Realism pass v2)

Back to [[No Dawn - Home]] · Decisions and numbers: [[Hub - Build History]] · History: [[Tracker]]

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

## Readability pass (2026-10-21)
- Backups: DevTools.SectionsBackup_20261021 (all 13 modules), KitBackup_20261021 (154,752 chars),
  TerrainBackup_20261021 (TerrainRegion of the compound, 89 x 17 x 85 cells from cell (-44, -6, -44)). Edited on
  KitDraft and SectionsDraft (Yard, Admin, Supply, FrontField, People, Detail), test-built, then promoted with
  UpdateSourceAsync. Changed: Kit (palette SteelPainted, Kit.busAsset band), Yard, Admin, Supply, FrontField,
  People, Detail.
- Pre-rebuild hand-edit check: the current code built into a detached folder (Ground with its terrain stubbed,
  Atmosphere and Surface skipped) matched the live Workspace.Hub part for part, except four parts the code builds
  that the place no longer had: the three CellBlock ScreenGlow monitor faces (with their lights) at the cell-block
  desk and one Nature ivy mesh at (-97, 7, -80). Someone deleted them by hand; the rebuild restored them. Nothing
  hand-added existed, so nothing was lost. Rechecked against the backups just before the rebuild: unchanged.
- Ruling: grey painted steel (Plastic) is a palette entry, `SteelPainted` — the Stage 2 Metal ruling applied to
  frames and housings, since Metal and DiamondPlate render near-black at night — cost if wrong: painted gate frames
  look less like galvanised pipe up close.
- Ruling: the field gate's solid LeafFrame stays as the invisible collider and a decorative tube frame goes round
  the wire (the Stage 2 invisible-collider pattern) — cost: none; collision identical.
- Ruling: destinations get light, not signs: floods (no shadows, decorative heads, NightLamp-tagged) at the vendor
  doors, the Services canopy, both field-gate aprons and the main gate. Garage and cell-block lighting were left
  alone; the destinations were raised past them — cost if wrong: perf, 13 more lights, none shadow-casting.
- Ruling: the vendor rooms differ by colour and furniture, not text — armory olive partition, gunmetal dado, hazard
  line; quartermaster green dado, blank-note board (paper is flat, so flat notes are allowed) — cost: none.
- Ruling: the bus band is plated over rather than re-textured, from its own Random(40418), so the existing plates
  keep their look — cost if wrong: the bus reads more like a wall above the windows.
- Gates (backup SectionsBackup_20261021, oldKit KitBackup_20261021): every section "shared rng: unchanged",
  collidable parts removed 0 / added 0 except People (the three moved NPCs). Admin PASS. Yard REVIEW = +2
  NightLamp SpotLights, 12 "buried" = the new tube frame inside its own invisible LeafFrame. Supply REVIEW = +4
  NightLamp PointLights (window lamps). FrontField REVIEW = +2 SpotLights, 7 "buried" = bus BandPlates inside the
  bus asset's own bounds collider. Detail REVIEW = +3 SpotLights. People REVIEW = Survivor6, 18, 20 moved; buried
  = their own accessory handles. All "buried" flags are visuals inside their own prop (Stage 2 ruling).
- LeakProbe Yard 70,180 sweeps, Supply 26,980: 0 deeper, 0 earlier.
- Lighting compared before and after the rebuild (Atmosphere rewrites it): unchanged.
- Build: 10,795 parts (+102 from this pass, +4 restored), 122 lights; Vendor 2, VaultTerminal 2, HubSpawn 8,
  Npc 52, NpcStill 7, NightLamp 38; 0 text. No runtime code changed, so no unit run.
- Console after the rebuild: "ContentId rbxassetid://203785492 failed: asset type does not match" — not referenced
  by any code in this pass; source not traced.
- Captures: `Screenshots/readability`, before and after from the same camera positions.

## Dollars in the UI (2026-10-21)
- The 2026-10-20 currency ruling, implemented. `Config.Economy`: CurrencyName "Dollars", CurrencySymbol "$",
  CurrencyCode "USD" (CurrencyShort "CH" removed). New `Shared.Util.Money.format(amount)` -> "$1,250" (whole
  dollars, comma groups, "-$45" for negatives); the HUD, inventory, survivor creator and vendor counter all format
  through it. Comments in Items, Vendors, TradeRules and SurvivorService and two test names now say Dollars.
- Ruling: `wallet.chits`, `Economy.StartingChits` and `Economy.MaxChits` keep their names until the schema pass
  (Open Items) — cost if wrong: none; no player ever sees them.
- Backup: DevTools.RuntimeBackup_20261021 (the 11 touched modules, each with an OriginalPath attribute).
- Tests: new `UnitTest.Cases.Money_Test` (4 cases) written first and seen failing (module missing), then the full
  suite on a playtest server: 150/150 passed. The playtest HUD read "$400". Console clean apart from the known
  plugins.

## Detroit overgrowth meshes (2026-10-21)
- [[Work Order - Detroit Overgrowth Meshes]] delivered by Claude, generated by code (numpy + Pillow; Blender 5.2 headless
  for review renders only). 38 OBJ, 18 PNG at 2048, manifest. Every file inside its triangle cap, every base at Y = 0,
  the 25 m ailanthus 89.29 studs.
- Ruling: the order's "albedo value 0.08–0.22" is read as **linear** reflectance (sRGB 0.31–0.51 in the PNG). Read as
  sRGB, the leaves rendered darker than wet asphalt in review — cost if wrong: leaves read lighter than meant; darken
  per instance with `SurfaceAppearance.Color` (it multiplies, so only the light reading can be corrected in place).
- Ruling: leaf cards are double-sided in the mesh, with the back copy 0.02 studs behind the front, rather than relying
  on `MeshPart.DoubleSided` — cost: twice the card triangles, inside every budget.
- Ruling: tree crowns are "super-tufts": tip cards at 1.75× (ailanthus) and 2.1× (mulberry) real leaf size, because
  3,500 tris buys about 100–150 twig tips against a real tree's thousands — cost if wrong: leaves look oversized
  inside about 5 m.
- Ruling: knotweed is built from clonal clumps (crossed card triples at mixed heights), not long wall planes. Wall
  planes read as a clipped hedge in review — cost: none.
- LOD1 budgets for vines (400) and grass (100) are ours; the order set none.

## Movement animation set v1 (2026-10-21)
- [[Work Order - Movement Animation Set]] v1 delivered by Claude: 14 clips in `Desktop\No Dawn\animation\out\ND_Movement_v1.rbxm`,
  with `manifest.txt`. Generated by code: human joint-angle curves (Winter walk, Novacheck run and sprint) time-warped
  to each clip's foot contacts, stance legs by IK on the real R15 dimensions, baked at 30 fps.
- Numbers: Walk 6 studs/s at 3.40 steps/s, step 1.76, duty 0.53. Jog 10 at 3.00, step 3.33, bob 8.5 cm. Sprint 20 at
  3.73, step 5.36, duty 0.18. Head (centre, standing 4.47): CrouchIdle 3.03–3.07, CrouchWalk 3.29–3.32, LandHard down to
  3.18, Slide 2.29. LandSoft dips 0.32.
- Gates (`checks.py`, on the real R15 meshes), every clip PASS: loop seam 0.000°; stance-foot slide 0.000 at the
  authored speed (Slide exempt); no sole below the floor; visible part-into-floor dig ≤ 0.011 (gate 0.02); every joint
  inside human range of motion (knee −2…150°, hip −30…125°, ankle −50…40°, elbow −2…145°); held poses balanced, centre
  of mass inside the feet (Idle +0.55, CrouchIdle +0.48 studs); limbs through each other ≤ 0.109 (Mantle); head pitch
  inside ±3° on locomotion; vault and mantle bodies out of the obstacle (≤ 0.002).
- Studio check: every clip rebuilt in Studio, played and sampled with `Animator:StepAnimations` on two rigs in a
  CoreGui WorldModel (nothing added to the place). Motor6D copy of the authored R15: every joint Transform within
  1.2e-4 of the authored pose and every part within 0.0005 studs of our forward kinematics, so the gates and the
  Blender review show what Roblox plays. A rig built from a HumanoidDescription here
  (`CreateHumanoidModelFromDescription`) has AnimationConstraint joints, not Motor6Ds (the avatar joint upgrade;
  Workspace carries `ImprovedAnimationConstraint`); all 15 joints driven, same transforms. Output clean apart from the
  known plugins.
- Ruling: footstep stride is the clip's `StepLength` attribute (walk 1.76, jog 3.33, sprint 5.36 studs), not the metre
  strides in [[Idea - Movement and Hitbox Reconciled]]; the R15 leg is 0.56 m, so cadence follows it — cost if wrong:
  footsteps timed off the metre strides fire out of step with the feet.
- Ruling: CrouchWalk rides 0.25 studs higher than CrouchIdle (head 1.16 below standing, not 1.4): a stud-thick R15 shin
  can't fold lower while stepping without sinking into the floor — cost if wrong: the moving crouch's head and hitbox
  sit 0.25 higher than the crouch idle's.
- Ruling: the vault is a one-hand tuck vault (left hand on the top, knees tucked through), not a speed vault: R15
  thighs are a stud thick and touch at rest, so legs stacked sideways pass through each other — cost: none seen.
- Ruling: SprintStop, Jump, Vault and Mantle carry a `RootPath` attribute that code moves HumanoidRootPart along. Vault
  is authored at a 3.2-stud obstacle with its near face 1.9 ahead, Mantle at a 4.0 ledge with the wall 1.25 ahead; for
  other heights code adds (h − authored) to y. SprintStop is authored against v = 20·e^(−15t), the notes' 0.2 s stop —
  cost if wrong: at a 4.5-stud vault the body rides 1.3 higher than the pose was built for; a different stop curve
  slides the planted feet.
- Captures: `Screenshots/animation-v1` — every clip at its key frame, the sheet from before this pass, gait strips, and
  vault, mantle and slide sequences.

## Detroit raid map, first build (2026-10-21)
- The raid place (PlaceId 120733035297566) built from `DevTools.MapBuilder` one section at a time, numbers in
  [[Map - How It Is Built]]. HubAssets copied from the hub; MapBuilder installed from `tools/mapbuilder` by Rojo build
  and Import Roblox Model, every module checked against its file.
- Found while building, fixed in code and rebuilt: Ground sank the whole map (trench codes were digits, which the
  run-length reader can't split); Shed 3 came out as 16 floating eave beams and both greenhouses had no roof (their
  builders read only rectangle roofs, and those three footprints only have triangles); the Grant house and greenhouse
  roof slopes were tilted the wrong way (eaves up, ridge down). A signature of every other building (part count,
  position and volume sums) was unchanged by the fix: 108 of 108.
- Ruling: Future is `LightingStyle = Realistic` with `PrioritizeLightingQuality` on; `Technology` is deprecated and
  hidden. The hub already reads that — cost: none.
- Ruling: Shed 3, a cross with chamfered inner corners in OpenStreetMap, is built as three rectangles (the east-west
  bar, the north and south arms), with the four chamfered corners open to the sky under the eave beam — cost if wrong:
  four triangles about 17 studs a side with no deck over them.
- Ruling: the greenhouses keep their real orientation, about 28° off the street grid; their glass gable is built in
  each greenhouse's own frame — cost: none.
- Boundary closes the frame: 612 level rays from the map centre (three heights) and both trench floors all stop at a
  collidable frame wall. Total 38,505 parts, 4 lights. Output clean apart from one ContentId error that predates the
  build.
- Captures: `Screenshots/detroit` — Adelaide Street at eye height, the roof of Eastern Cold Storage, the Cut at the
  Division Street bridge. They showed terrain bulging in front of the Cut's retaining walls; fixed the same day
  (`detroit_cut_division_bridge.jpg` before, `detroit_cut_division_bridge_walls_fixed.jpg` after; the walls' colour
  fields show for the first time).
- Ruling: every trench rim gets an **apron**, a concrete strip at street level from the back of the retaining wall to
  10 studs behind its face (7.6 studs, about 2.1 m), and Ground empties the terrain under it near the rim. Terrain
  alone can't meet a straight wall cleanly: full voxels draw past their cell, and thin ones over air render or not by
  their neighbours. Reads as a sidewalk along the Cut and the freeway — cost if wrong: a 2.1 m concrete strip on
  every rim; it runs under sidewalk slabs, into the four bridge decks and into the slaughterhouse's wall foot and
  loading dock on the Cut's west rim. The bank's measured fit is in [[Map - How It Is Built]].

## Overgrowth meshes in the raid place (2026-10-22)
- 38 OBJ imported and wired; numbers, texture ids and checks in [[Work Order - Detroit Overgrowth Meshes]].
- Ruling: imported from copies of the OBJ without their `.mtl`, so Import 3D uploads meshes only and the 18 maps go
  up once each through `upload_image` — cost if wrong: none; the importer would otherwise re-upload each species' maps
  for every file.
- Ruling: pivots stay as the importer set them, on each mesh's base, not `CFrame.new(-bbox_centre)` from the manifest:
  the importer turns meshes 180° about Y, which flips the manifest's x and z (measured on tree C's trunk vertices) —
  cost if wrong: none; all 38 agree with the manifest under the turn to 0.001.
- Capture: `Screenshots/overgrowth/overgrowth_detroit_lineup.jpg`, one of each species on an open lot at night, then
  removed. Alpha cut-outs work; knotweed and grass tufts read paler than the rest (open item).

## Design rulings (2026-10-20)
- Currency renamed **Chits → Dollars** (symbol `$`, code `USD`). The internal `wallet.chits` key stays until a schema
  pass; Phase 1 records keep the old name. Player-facing text in the live notes now says Dollars.
- Death loot stays **on the body**. A reanimated body carries the gear; the static death cache is rejected. A headshot
  kill or a melee finisher destroys the brain; otherwise the body rises and becomes a loot container on legs. See
  [[Idea - Death and Loss]].
- **Open servers replace the 50-minute batch.** No gameplay timer, late join is normal, loot respawns on a timer, and
  extraction is still the only bank. The server lives while it is populated. See [[Idea - Raid Frame]].
- **Procedural injuries and hunger/thirst are in v1.** Global HP stays 100; a wound is a status on a body part, not a
  per-limb HP pool. Hunger and thirst are the personal clock that replaces the server timer. See
  [[Idea - Injury and Survival]].
