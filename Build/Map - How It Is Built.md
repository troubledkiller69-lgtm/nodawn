---
tags: [nodawn, map, detroit, tooling]
updated: 2026-10-22
---

# Map - How It Is Built

Back to [[No Dawn - Home]] · Design: [[Idea - Detroit Map]], [[Reference - Detroit Overgrowth]] · Rules: [[Design Rules]] · Hub equivalent: [[Hub - How It Is Built]]

The Detroit raid map: Eastern Market and the Dequindre Cut, 1:1 on the real street grid, built from OpenStreetMap
data by code. It lives in its own place in the experience (raids are separate places; the hub never contains
anything that can deal damage). The raid place was added on 2026-10-22, with its default Baseplate and SpawnLocation
removed, and saved empty.

## The frame

| | Value |
|---|---|
| Area | 448 × 980 m = **1,600 × 3,500 studs** (0.44 km²) |
| North edge | Mack Avenue, with the quarantine line behind it |
| South edge | The Fisher Freeway trench (sunken I-75), fenced |
| West edge | Russell Street's west frontage |
| East edge | The Cut's east bank |
| Orientation | The street grid runs 27° west of true north. Map X runs across the grid (east), Z along it (south). True north is 27° clockwise from -Z |
| Scale | 1 stud = 0.28 m; street level Y = 0; trench floors Y = -25 (7 m) |

Deviation from [[Idea - Detroit Map]]: 3,500 studs long, not 3,200, so both short edges are real edges (Mack and
the freeway) instead of cuts through blocks.

## Pipeline

Source and scripts: `Documents/No Dawn/tools/mapdata` (README there). Data © OpenStreetMap contributors, ODbL —
the game's credits must say so; nothing in the world carries text.

1. `export.py`: the OSM snapshot → `mapdata.json`. Clips to the frame, cleans footprints, assigns a role and a
   storey count to each building (OSM has almost no heights here; landmarks are set by name).
2. `geo.py`: `mapdata.json` → `built.json`. Footprints squared to the grid, roofs as rectangles (or triangles),
   every wall edge classed by what it faces (street, alley, lot, court, party), sidewalk slab runs trimmed at
   crossings, curbs, centre lines, the Cut and freeway trenches, bridges, and the terrain material grid.
3. `tolua.py`: → `Data/BuiltGrid`, `BuiltStreets`, `BuiltBuildings` (generated Luau; never hand-edited).
   Run-length codes are letters: materials `G A C L P M`, trench `N` none, `C` the Cut, `F` the freeway.
4. Install the modules into the raid place's `ServerStorage.DevTools.MapBuilder` and rebuild. MCP code can't read
   files from disk, so install by hand: `rojo build` a project with MapBuilder as the root and Kit, Geo, Data and
   Sections under it, copy the `.rbxm` to a short path, then right-click DevTools → Insert → Import Roblox Model.
   Check each module's `#Source` against the file afterwards.

The pipeline is deterministic: a rerun on 2026-10-21 reproduced all three Data modules byte for byte. Run it from a
short folder; Windows won't start Python from a working directory with a very long path.

## The builder

`ServerStorage.DevTools.MapBuilder` (sources mirrored in `tools/mapbuilder`):

```lua
local m = game.ServerStorage.DevTools.MapBuilder:Clone(); m.Parent = game.ServerStorage
print(require(m).build()); m:Destroy()                -- every section
print(require(m).build({ "Buildings" })); m:Destroy() -- only these
```

| Section | Builds |
|---|---|
| Atmosphere | The hub's night (day-for-night, sky, grade, fog a touch thicker) and the No Dawn PBR materials, recreated from their uploaded maps |
| Ground | All terrain from the grid: asphalt streets, dirt and weedy lots, pavement lots, the trenches |
| Streets | Sidewalk slabs, curbs, worn double yellow lines, the four bridges over the Cut with a sodium lamp under each |
| Trenches | Retaining walls and copings, the Cut's greenway, ladders, rim railings and colour fields; the freeway fence |
| Buildings | All 111 footprints: walls by storey with real openings and infill by condition, facade trim, docks, fire escapes, roofs and plant; the sheds, the parking deck, greenhouses, containers, the Grant house |
| Boundary | The quarantine line north of Mack, side fences, invisible frame walls |
| Overgrowth | The overgrowth meshes by age band (below). Built last: it raycasts against the others. Also installs the client foliage LOD: `ReplicatedStorage.Foliage` (LOD0 templates, the `Lod` module) and `StarterPlayerScripts.FoliageLod`, from `MapBuilder.Runtime` |

Rules that differ from the hub:

- **Every section owns `Workspace.Map.<Section>` and its own Random.** Any section rebuilds alone and nothing
  else moves. There is no shared rng to protect.
- **Each building's Random is seeded from its OSM id,** so editing one building never changes another.
- Never hand-edit `Workspace.Map`. Change the section, or the pipeline, and rebuild.
- **Parts cap at 2,048 studs a side.** Anything as long as the map is built in pieces.

## Budget

Dry run, 2026-10-21: Streets 11,015 parts, Trenches 2,736, Buildings 22,448, Boundary 1,759 — about 38k before
overgrowth and props, against the note's 50–70k.

First build in the raid place (PlaceId 120733035297566), 2026-10-21, one section at a time:

| Section | Parts | Lights | Build | Checked |
|---|---|---|---|---|
| Atmosphere | 0 | 0 | 0.0 s | Lighting children, LightingStyle Realistic, six NoDawn MaterialVariants, Mud override |
| Ground | 0 | 0 | 1.7 s | 480 × 956 cells, 3,576,932 terrain cells. Before the rim fix: voxel diff against BuiltGrid 0 of 458,880 columns off; Cut 15,346 cells, freeway 19,100, both floors at y -25 |
| Streets | 11,015 | 4 | 0.7 s | Sidewalks 8,550, curbs 536, paint 1,823, bridges 106; all anchored, largest side 765, y -6.4 to 4.3 |
| Trenches | 2,848 | 0 | 0.3 s | Cut and freeway walls, copings, 112 rim aprons, greenway on the Cut floor (-25.1, -24.6); 108 chain-link Textures, no text |
| Buildings | 22,995 | 0 | 2.2 s | 111 footprints, world y -0.01 to 71.05, none floating or buried; 167 HVAC units from the HubAssets mesh |
| Boundary | 1,759 | 0 | 0.1 s | 6 invisible frame walls (x ±802, z ±1,752, y -50 to 550), quarantine line 441, fences 1,312; 612 rays from the centre and both trench floors all stop at a collidable wall |

Map total: **38,617 parts, 4 lights** before overgrowth (45,985 with it), against the note's 50–70k before props.

Captures, 2026-10-21 (`Screenshots/detroit`): Adelaide Street at eye height between Sheds 3 and 2; the north-west
corner of Eastern Cold Storage's roof (70.9 studs, the tallest building) looking down Adelaide; the Cut's greenway
looking north to the Division Street bridge.

![[detroit_street_adelaide.jpg|600]]
![[detroit_roof_cold_storage.jpg|600]]
![[detroit_cut_division_bridge_walls_fixed.jpg|600]]

**The trench rims.** The first build had terrain 1–2 studs proud of the retaining walls on both banks (rounded lumps
where lit, `detroit_cut_division_bridge.jpg`): a full voxel draws its face about 2 studs past its cell, so the cell
just behind each rim bulged through the wall. Now, near every rim:

- Ground empties the cells within 4.3 studs behind the face above the floor row, and thins the two rows above the
  floor for cells within 7.5 (so the cove where floor meets bank forms behind the wall). The floor row runs on under
  the wall foot.
- Trenches lays an **apron** on top: a concrete strip at street level from the wall's back to 10 studs behind the
  face, over the emptied cells and the terrain's rounded edge.

Measured along all 112 rim walls: 0 of 14,574 samples from y -24 to the top have terrain in front of the wall face;
the floor 0.6–3 studs out stays within 0.4 of y -25 in 7,209 of 7,287 samples (a lip up to 0.6 at the wall foot);
the street 3–9.5 studs behind the face has no dips. The 2,736 earlier Trenches parts are unchanged.

Ground's first run sank the whole map: the trench rows used digit codes, which `Geo.runs` can't split (see
[[Lessons]]). Fixed in `geo.py` and Ground (letter codes, plus a row-length assert) and rebuilt. Buildings' first run
built 22,448 parts with three footprints incomplete and two roofs upside down; after the fix (`rectsOf` and
`framesOf` in the Buildings section, the roof tilt sign) it is 22,995, and only those three buildings changed
(rulings in [[Realism Ledger]]).

## Overgrowth

Built 2026-10-22 from the 38 templates in `ServerStorage.DevTools.OvergrowthAssets`
([[Work Order - Detroit Overgrowth Meshes]]). **7,368 parts** (trees 368 with as many trunk colliders, saplings 222,
shrubs 507, grass and weeds 2,200, curb-seam weeds 1,859, vine curtains 507, roof plants 43, the Cut 1,294), 1.2 s.
Map total **45,985 parts**.

**Age is a field, not districts.** 12 years by default, pulled to 25 on the vacant ground of the north half (the share
of grass and mud cells around a point, blurred over ~50 studs, so built blocks among the lots stay younger) and within
~50 studs of the Cut, and to 5 on the market floor, Russell and the Gratiot frontage; each pull blends over ~120
studs. Frame by band: 5 years 25%, 12 years 58%, 25 years 17% (the note: 15–20%).

| Band | What grows |
|---|---|
| 5 years | Weeds in lots and curb seams, ailanthus suckers as saplings, vines to the first floor; many buildings bare |
| 12 years | Ailanthus 6–10 m (scaled 0.3–0.5) with two suckers each, sumac walls of three clumps on lot edges, knotweed against walls, vines to the second floor, roof growth from about 13 years |
| 25 years | Mulberry canopy 12–18 m; ailanthus only at its edges and as 12% emergents (shade-intolerant); open understory; vines to the third floor; young trees on big roofs |
| The Cut | Sumac, knotweed and grass on the floor 11+ studs off the greenway and clear of the ladders; vine curtains on the retaining walls |

Every sample draws from its own Random seeded from its layer and grid cell (buildings from their OSM id), so a rule
change in one place never moves a plant elsewhere. Foliage never collides or blocks raycasts; trees get an invisible
trunk collider (collides, stops raycasts). Checked: nothing under a roof, every vine facing out, 13 of 368 crowns
reaching a building wall at 60% of their box, one lot plant on asphalt.

**LOD.** Every plant in the place is its LOD1 mesh, tagged `NdFoliage` with its LOD0 template's name and scale. The
client (`FoliageLod`, every 0.2 s) clones the LOD0 mesh in for plants within 54 studs (15 m) of the camera and hides
the LOD1 with `LocalTransparencyModifier`; past 62 studs it swaps back. Streaming is on, so it follows the tag's
added and removed signals. Playtest: 4,564 plants streamed in at spawn, 4 swapped, Output clean. Edit-time captures run
the same module once for the shot.

![[overgrowth_market_adelaide.jpg|600]]
![[overgrowth_north_lots_scott.jpg|600]]
![[overgrowth_roof_dwsd_warehouse.jpg|600]]
![[overgrowth_cut_division.jpg|600]]
