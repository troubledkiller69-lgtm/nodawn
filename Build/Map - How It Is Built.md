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
| Ground | 0 | 0 | 0.7 s | 480 × 956 cells, 3,597,892 terrain cells; voxel diff against BuiltGrid: 0 of 458,880 columns off. Cut 15,346 cells, freeway 19,100, both floors at y -25 |

Ground's first run sank the whole map: the trench rows used digit codes, which `Geo.runs` can't split (see
[[Lessons]]). Fixed in `geo.py` and Ground (letter codes, plus a row-length assert) and rebuilt.
