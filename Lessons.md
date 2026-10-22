---
tags: [nodawn, lessons]
updated: 2026-10-21
---

# Lessons

Back to [[No Dawn - Home]] · Related: [[Working Agreement]], [[Design Rules]]

## Studio MCP
- **SurfaceGui text needs a moment to render.** A `screen_capture` taken right after a rebuild showed blank signs. Never capture in the same batch as a rebuild; render first, then capture. Don't "fix" blank signs before re-capturing.
- **Very distant aerial captures can time out and drop the MCP bridge.** This happened from 350 studs. Keep overview cameras within ~150 studs of the target. If the bridge drops, check `Get-Process RobloxStudioBeta`, wait ~15 s, then call `list_roblox_studios` again.
- **`Lighting.Technology` is deprecated and hidden from the Properties panel** (2026-10-21). It is superseded by
  `LightingStyle` (Realistic / Soft) and `PrioritizeLightingQuality`, which MCP code can read and write. Future is
  `Realistic` with quality prioritised; the hub already reads that, and MapBuilder's Atmosphere sets it.
- **Pathfinding uses coarse voxels and doesn't model jump exploits.** Pair navigation checks with physical probes (PivotTo + MoveTo + Jump).
- **`execute_luau` keeps one require cache for the whole Studio session.** After editing a module, `require(path)` in a later call can still return the old version, and that applies to every dependency the module requires by path. To get fresh code, require a clone placed beside the original (so relative paths still resolve), or pass freshly required config in explicitly (as `AppearanceLibrary.build(only, config)` does). Verify a just-edited value before trusting a run.
- **Layered clothing on the blocky R15 body:** layered jackets and overshirts look good (real 3D collars, zips, folds). Layered pants merge the legs into a skirt shape, so pants stay classic. Raw layered assets from `GetObjects` often have no body Attachment; fetch them through `Players:CreateHumanoidModelFromDescription`, which adds it.
- **`generate_mesh`: at most 3 async jobs at a time.** A batch of 16 failed 12× with "Unable to generate Model", and 3 more at once hit "Failed to publish assets … Too Many Requests". Synchronous calls can time out at the MCP layer yet still finish: check Workspace for the model before retrying.
- **Generated meshes ignore the requested aspect ratio** and fit the longest axis into the box (HospitalBed 1.65×1.85×3.4, JerseyBarrier 3.5 long). Read the real size before scaling or tiling.
- **Edit shared modules on a draft copy first** (`KitDraft`), build every section with it next to the live version, then promote with `ScriptEditorService:UpdateSourceAsync`. Other agents keep working against the live copy meanwhile.
- **Gate parallel builder agents with a deterministic diff, not trust.** `DevTools.SectionGate` rebuilds a section from the backup and the current module and diffs collidables, tagged gameplay instances, shared-rng consumption, budget and decor buried in walls.
- **Smooth terrain draws a solid voxel's surface at about centre + occupancy × 4 studs**, not at the fill height. `FillBlock` to y -0.5 rendered the ground at +1.5 and silently buried every floor for two sessions. Fill to -2.5 for a surface at -0.5, and check with a visible marker part, not only raycasts.
- **Terrain collision lags terrain writes inside the same `execute_luau` call.** Raycasts right after a rebuild returned stale heights. Measure in a separate call, or read voxels directly.
- **Terrain water is its own liquid channel.** `WriteVoxels` with `Material.Water` is silently dropped. Use `Read/WriteVoxelChannels` (`SolidMaterial`, `SolidOccupancy`, `LiquidOccupancy`). Liquid fills from the voxel bottom: occupancy 0.8 in the -4..0 row puts the waterline at -0.8.
- **Background agents can die mid-edit on usage limits.** Afterwards, audit every module they owned with the gate and the leak probe before trusting it. The Admin agent had cut openings and died before sealing them.
- **Creator Store: watch for impersonated creator names.** "Roblox" padded with invisible Hangul filler characters is not Roblox. Insert only into a quarantine folder and check for scripts before use.
- **`generate_material` ignores proportions in the prompt.** Fix the look with `StudsPerTile` and compare variants on test panels before changing a palette. Some generated variants render flat; check each one.
- **Write negative test cases from the actual generated data.** The first "unreachable cell" failure was a test bug: the cell's door was randomly closed. Read generated attributes before asserting.
- **Studio MCP tools leave the edit camera `Scriptable`, which freezes the user's viewport** (2026-10-17, user-reported 3 times). Seen after a timed-out `screen_capture` and after a timed-out `generate_material` (which also moved the camera somewhere unrelated). Every capture also renders through the user's own viewport, so moving the camera yanks their view. Protocol: (1) take as few captures as possible while the user is in Studio and say so first; (2) for each capture, save `CurrentCamera.CFrame`, set the shot with `CameraType = Fixed` (never pass `camera_position` to `screen_capture`), capture, then restore the saved CFrame in the very next call; (3) after ANY generate_* or capture call, success or timeout, read `CameraType` and set it back to `Fixed` if it is `Scriptable`; (4) never stage test objects far from the hub (e.g. y = 300).
- **Never take over the client camera or move the character during a playtest the user may be driving** (2026-10-17). The user was walking around while I set `CameraType = Scriptable` and pivoted their character for screenshots, which fought their controls. Before any client-side camera or character change, ask, or use Edit-mode captures (camera `Fixed`) instead. If a takeover happens anyway, restore it at once: `CameraType = Custom`, `CameraSubject = Humanoid`.
- **RULE (user, 2026-10-17): no painted-on 2D detail, anywhere.** If something in the real world has depth (ground cover, pebbles, gravel, grass, mud, rubble, leaves, bricks, cracks, debris, tire ruts), build it as 3D: terrain for open ground (organic material blends, real grass blades, gentle relief) and real meshes or parts scattered on top. Flat photo textures on big slabs look printed on in first person, because Roblox has no parallax mapping; the first courtyard (a 128×96 slab with a cobble texture) was called "horrendous". Flat is only acceptable for things that really are flat: signs, paint, stains, posters, floor tiles. Before adding any surface or detail, ask: would a player standing on it in first person see a picture or an object? Keep blade-growing grass away from thin floor parts.
- **ROOT CAUSE of the recurring camera bug (2026-10-17, measured):** the Studio MCP tools set `workspace.CurrentCamera.CameraType = Scriptable` about 0.05 s after *every* `execute_luau` call returns, and `multi_edit`, `generate_*` and captures do the same. No game script touches `CameraType`. A reset inside a call is therefore undone as soon as the call ends, so the old "set it back in the next call" rule never stuck. Fix: end every `execute_luau` with `task.delay(0.5, function() if cam.CameraType == Enum.CameraType.Scriptable then cam.CameraType = Enum.CameraType.Fixed end end)`; after a `multi_edit`, `generate_*` or capture, run a short `execute_luau` that arms the same delayed reset. Make sure the last Studio call of every turn arms it.
- **Studio's `generate_mesh` output is stylized** (cartoon colour textures, painted-on cracks, neon moss). For natural stone, keep the generated shape, clear `TextureID` and use the built-in `Rock` / `Slate` / `Granite` material in a muted grey. It reads as real stone, and no seams showed on the generated UVs.
- **Creator Store rock searches are mostly spam re-uploads**: random-name accounts, fake "Roblox" names padded with invisible Hangul filler, and ripped Megascans (`Aset_rock_*`), even with `verifiedCreatorsOnly`. Don't source rocks there.
- **Removing collidable props makes LeakProbe flag "deeper" sweeps by design.** Attribute each flagged sweep to the part it used to stop on; only sweeps not explained by a removed part are real gaps. Removing the signs flagged 1,544 sweeps, all explained.
- **Collidable props among random vegetation:** place the props first, then make the plants skip covered spots while still drawing their random numbers, so every other plant keeps its exact place. Verify by diffing plant pivots between the backup build and the new build.
- **The edit camera re-aims at `CurrentCamera.Focus`** when the MCP tools flip `CameraType` (2026-10-17). Setting only
  `CFrame` for a shot gave captures pointing at the user's old focus point. Set `CFrame` and `Focus` (the shot target)
  together, re-apply both in the delayed reset, and save and restore both.
- **`ContentProvider:PreloadAsync` reports Failure for every asset from MCP code**, even textures already rendering in
  the scene. It can't verify uploads; apply the asset to a part and capture instead.
- **Custom PBR textures work end to end:** write maps with Python (`py -3.14` has numpy + Pillow; the `python` on PATH
  is a venv without them), serve them with `py -3.14 -I -m http.server 8765 --bind 127.0.0.1`, pass the
  `http://127.0.0.1:8765/<file>.png` URLs to `upload_image`, and use the returned ids in a MaterialVariant. They
  rendered at once (no moderation wait). This gives exact real-world proportions, which `generate_material` ignores.
- **`Terrain.Decoration` and `Terrain.GrassLength` need the Environment capability:** MCP code can't read or write them
  ("not a valid member"). Set them from a game script or ask the user.
- **Never gate Ground or Surface without rebuilding after.** Their builds write the live terrain (Ground calls
  `terrain:Clear()`), so a SectionGate or LeakProbe run on them leaves the yard unpainted until the next full build.
- **A part's Color multiplies its MaterialVariant's colour map** (a dark-red tint turned grey block red-brown). Author
  maps light and neutral and set the final colour through the palette.
- **New uploads need ~20–60 s before they render** (white until then): wait before judging. `upload_image` takes ~5–9
  s per 1 MB PNG and the call times out near 60 s, so send at most ~6 large images per call. Upload names are random
  hashes; the inventory lists newest first, so a timed-out batch can be mapped back by order (then confirm visually).
- **Small terrain-water puddles don't work** (2026-10-17). 4-stud voxels smooth a one-column hollow almost flat;
  liquid fills to bottom + 4 × occupancy whatever the solid, so the water either floods out as a flat, rippling
  rectangle (user: "looks like the ocean moving around") or vanishes. Flat disc parts still read as discs, even with
  PBR. What works: standing water inside the terrain material (MaterialService:SetBaseMaterialOverride on Mud with a
  variant whose pools are mirror-smooth), in carved hollows. The override works from MCP code.
- **Night skies in Realistic lighting:** after dark the engine draws the sky black whatever the skybox, and an
  Atmosphere with dark colours blacks out the sky and the distance even by day. Shoot night day-for-night: a daytime
  render hour, low light levels, a dark custom skybox, lit grey-blue fog, no sun/moon disc. Dynamic Clouds show the
  skybox through gaps even at Cover 1, so turn them off under an overcast night sky.
- **Terrain ground yes, rock scatter no** (user, 2026-10-17). Converting the yard and compound slabs to terrain (dirt, mud, real grass, relief) was liked. ~2,000 generated pebble, stone and rubble meshes across the ground were rejected ("get rid of the rocks"). Ask before scattering small props over the ground. A few big boulders are wanted, though ("add some bigger rocks not just pebbles"): 9 placed at the pond and beyond the fences.
- **`generate_mesh` makes realistic man-made props when asked** (2026-10-17): "realistic ... weathered, worn, dirty ...
  no text, no logos" gave cars, an engine on its stand, tool chests, beds, lockers, desks and shelving that read as
  the real thing, far past part-built props. Organic subjects (corn, tomato plants, the first pig) come out
  cartoonish; "photorealistic, anatomically accurate, not cartoon" fixed the pig, but crops are better from the PBR
  foliage library and parts. Check every prop in a capture: a jack came out upright, a tyre malformed.
- **Generated props can carry pseudo-text** (gibberish labels on oil jugs, keyboard legends). The hub has no text, so
  zoom in on packaging and regenerate with "plain unlabelled ... no labels".
- **Publishing generations is rate-limited:** after ~40 in a session a job failed "Too Many Requests". Keep to two at
  a time and retry the failed one later.
- **Segmented generations nest each part in its own sub-model** (`[\"car body\"`, `\"hood\"`), so FindFirstChild on
  the template misses them: flatten first (PropTools.flatten). Segmentation labels can also be wrong (a "rear left
  wheel" sat on the front right). Make name cleaning idempotent: re-cleaning "CarBody" gave "Carbody".
- **A generated MeshPart's box can be larger than what you see** (a car's body box reached below its wheels, so it
  floated). Measure real bounds from the vertices: `AssetService:CreateEditableMeshAsync(Content.fromUri(MeshId))`
  works on the user's own generated meshes, scaled by Size / MeshSize.
- **No mesh upload from MCP code:** `AssetService:CreateAssetAsync` returns "not available yet", and an EditableMesh
  MeshPart is not saved with the place. Meshes come from generate_mesh (or the user's own 3D import).
- **A MeshPart's TextureID ignores Color.** To recolour (blankets), clear TextureID and use a material + Color; the
  real folds still shade.
- **The hub's R15 rigs use AnimationConstraints, not Motor6Ds.** Pose by rotating each joint's Attachment0 (the rest
  pose animations play on top of); limbs move after a frame in edit mode, so solve the chain yourself (Part1 =
  Part0 * A0 * A1:Inverse(), root outward) before welding anything to a limb. Positive X swings a limb forward.
- **Rebuild near the vendor windows with care:** NPCs in a queue must leave the window centre (where the prompt is) free.
- **`generate_mesh` cannot make long objects** (2026-10-18). Four bus prompts ("prison bus", "40-foot school bus,
  ten windows per side", "12 metre coach") all came out 1.7-1.9 times as long as wide; a real bus is 4.2. Cars come
  out about a fifth short. For a long vehicle pick a generation with flat ends (its front and back faces then lie
  across the stretch and keep their detail), stretch it along its length with the wheels kept round, and cover the
  smeared sides with something real (plates, bars). Don't spend generations on wording.
- **`generate_mesh` prompts over ~600 characters fail** with "Character limit exceeded"; 437 worked. Keep them
  under ~450.
- **A blank white `screen_capture` with only the view cube drawn means Studio isn't rendering its viewport**
  (2026-10-18, after a long idle: window minimized or screen locked). `execute_luau` still works. Don't retry
  captures in a loop: keep doing the non-visual work and ask the user to bring Studio to the front.
- **`execute_luau` output is capped at 100,000 characters** (2026-10-21). The Kit (155k) came back cut short with
  no warning. Return big sources in `string.sub` chunks and check the total against `#Source`.
- **A long call can outlast the MCP and still finish.** A full `HubBuilder.build()` came back "Target is not
  reachable (ExecuteLuau.runCode, DMN_ID_EDIT)" but completed. Check `Workspace.Hub` (part count, a part the change
  adds) before running it again.
- **`StudioMCP.exe` needs ~10 s after starting to see Studio.** An immediate `list_roblox_studios` returns an empty
  list even with the toggle on. Wait, then list.
- **Find hand edits in `Workspace.Hub` before a rebuild.** Build the current code into a detached folder (Ground's
  `workspace.Terrain` swapped for a stub that reads through and drops writes, Atmosphere and Surface skipped) and
  diff part keys against the live hub. Key rotations by their vectors, not Euler angles (`-0,180` and `0,-180` are
  the same), and compare NPCs as whole models: live limbs settle and accessory welds convert.
- **A module whose `require` failed stays failed for the whole Studio session.** So a red test run in Edit mode
  (`RunUnitTest(filter)`, before the module under test exists) must run a fresh clone of the case under a new name,
  or the real case stays broken. Run the green check as the full suite on a playtest server: it starts with an
  empty require cache.
- **Parts cap at 2,048 studs a side.** The raid map is 3,500 long: frame walls, fences and long path segments must be
  built in pieces. Check the longest generated segment before building.
- **`WriteVoxels` regions must sit on the 4-stud lattice.** Put a terrain grid's origin on a multiple of 4 (the map's
  first grid started at Z -1910 and would have misaligned every chunk).
- **Dry-run a section in a detached folder** to catch runtime errors without touching a place: build into a
  `Folder` that is never parented to Workspace, count, destroy. Never for sections that write terrain or Lighting.
- **The shell tool turns `\\n` written in a heredoc into a real newline**, which breaks generated Lua strings and
  Windows paths (`\v` became a tab). Write generator scripts with the file tool, or use `string.char(10)` and forward
  slashes.
- **OpenStreetMap: one Overpass query, from a mirror.** The main server answers scripted queries with 406 (send a
  User-Agent and POST) or "at capacity"; `maps.mail.ru/osm/tools/overpass/api/interpreter` returned the district
  (5 MB) in one go. Credit "OpenStreetMap contributors" (ODbL) in the game's credits.
- **`py -3.14` is not on Lando's machine.** Plain `python` (3.11) there has Pillow.
- **Studio plugins cloud_4771536187 and cloud_7352299243 throw errors in every playtest** (a shop/trading/chat UI kit,
  a "Base Qi" thumbnail GUI). They are not in the place; ignore them when judging the game's console.
- **Installing modules from disk into a place** (2026-10-21). MCP code can't fetch them: `HttpService` is off in the
  raid place, and `InsertService:LoadLocalAsset` needs RobloxScript. `rojo build` a one-off project into an `.rbxm`,
  then Explorer → right-click the parent → Insert → Import Roblox Model. Check every module's `#Source` against the
  file size afterwards (Rojo turns CRLF into LF, so a CRLF file comes in short by its line count).
- **Import Roblox Model from a long path inserts an empty `Script`.** Studio got the 8.3 short name `MAPBUI~1.RBX`,
  didn't recognise `.RBX` and made a Script named after it. Copy the file to a short path first
  (`%TEMP%\ndmb\MapBuilder.rbxm`).
- **The open session is not on `C:\`, so the Studio MCP is missing** (2026-10-21). It was registered for the `C:/`
  project only. It is now also registered (local scope) for the vault folder; a session opened anywhere else
  starts without the Studio tools.
- **Run-length rows need non-digit codes.** `Geo.runs` reads `(%d+)(%D)`; the trench grid used digit codes
  (`"415091560"` is 415×0, 9×1, 56×0), so every row decoded to nothing and Ground sank the whole map to y -25 without
  an error. Codes are letters now, and Ground asserts every decoded row is `nx` cells long.
- **Rigs built from a HumanoidDescription here have AnimationConstraint joints, not Motor6Ds** (2026-10-21).
  `CreateHumanoidModelFromDescription` returned 15 `AnimationConstraint`s, 14 `BallSocketConstraint`s and no `Motor6D`
  (the avatar joint upgrade). The Animator drives `AnimationConstraint.Transform` exactly as it drives a Motor6D, so
  clips play unchanged; find joints by `Attachment1.Parent`, not `Part1`.
- **`RegisterKeyframeSequence` ids load asynchronously.** Right after `LoadAnimation`, `track.Length` is 0 and the joints
  sit at rest. Wait for `Length > 0` before sampling poses.
- **Exact pose checks need one fresh rig per clip.** Stopping a track and loading the next on the same Animator, with
  only `StepAnimations(0)` between, left the stopped clip blended into the next one's samples (rotation-matrix errors
  up to 1.1). On fresh rigs the same clips matched to 1e-4.
- **Throwaway rigs go in a WorldModel under CoreGui.** Animations play there, nothing is added to the place or sent to
  Team Create collaborators, and nothing has to be staged far from the hub.

## Asset generation (Claude side)
- **Blender 5.2 installs headless from pip** (`pip install bpy==5.2.2`, Python 3.13): enough to re-import delivered
  OBJ files and render review shots with Cycles on CPU, with no Blender install.
- **Say linear or sRGB when a spec gives an albedo value.** "Value 0.15" as sRGB is darker than wet asphalt; as
  linear reflectance it is an ordinary leaf. Ruled linear for the overgrowth set ([[Realism Ledger]]).
- **Coplanar double-sided cards render black in Cycles** when the back copy wins the depth tie. Offset the back copy
  (0.02 studs); Roblox culls back faces anyway, and the offset also rules out z-fighting.
- **R15 thighs are a stud thick and touch at rest.** The legs can't cross or stack sideways, so poses that need it are
  out: the vault is a one-hand tuck vault, the slide keeps the hips 0.5 above the floor, crouches splay the knees 8–15°.
- **Roll a toed-out foot about its own heel and toe edges.** Pivoting a yawed foot about the un-yawed edge slid the
  planted foot 0.12 studs a step; the pivot now includes the yaw.
- **The R15 shin overlaps its own foot block.** Shin vertices inside the foot's footprint are hidden by the foot, so the
  floor check skips them and counts only visible dig.
