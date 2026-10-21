---
tags: [workorder, map, detroit]
status: in-progress
updated: 2026-10-21
---

# Work Order - Detroit Overgrowth Meshes

Back to [[Open Items]] · Source: [[Reference - Detroit Overgrowth]] · Map: [[Map - How It Is Built]] · Process: [[Working Agreement]]

```
TASK: Overgrowth mesh set for a 1:1 Detroit district at night: tree of heaven, sumac, knotweed, mulberry, vines, grass.
TARGET: Roblox Studio import (FBX or OBJ), PBR SurfaceAppearance-ready maps.
DELIVERABLE: per species the variants below, LOD0 + LOD1 each, one shared 2048 atlas per species, manifest.txt.
SCALE: 1 stud = 0.28 m. Pivot at the root/base centre, +Y up, -Z facing the viewer's default.
  - Tree of heaven: 3 variants, 18 / 22 / 25 m tall (64 / 79 / 89 st); pale straight trunks, huge compound leaves; plus 2 sucker clumps 2-4 m.
  - Staghorn sumac: 3 clonal clumps, 3 / 4.5 / 6 m (11 / 16 / 21 st), 4-7 m wide; red-brown stems, maroon cones.
  - Japanese knotweed: 2 wall segments, 2.5 m tall x 6 m long (9 x 21 st); one green, one dead canes.
  - White mulberry: 2 hero trees, 12 / 15 m; crooked dark trunk.
  - Vine curtains: 3 flat-backed panels 4 x 6 m / 3 x 10 m / window-size 1.5 x 2 m, for walls and window openings.
  - Grass and weed clumps: 4 small clumps 0.5-1.5 m.
BUDGET: trees <= 3,500 tris LOD0 / 900 LOD1; clumps and walls <= 1,800 / 500; vines <= 1,200; grass <= 300.
  One 2048 set per species (albedo, normal, roughness); alpha-tested leaves; no vertex colour.
CONSTRAINTS: night palette from the reference: leaf albedo value 0.08-0.22, saturation 8-25%, hue 95-140.
  Never jungle green or lime. Leggy, visible trunks (short season, zone 6a). Wet specular, not saturation.
  Clean silhouettes at 30 m; cards acceptable past 40 m.
ACCEPT:
  - imports at 1:1: the 25 m ailanthus measures 89 studs tall, the 6 m sumac 21
  - pivot sits on the ground plane at the trunk base (Y = 0 after import)
  - every species' leaves stay under 25% saturation in the albedo
  - tri counts inside the budget in the manifest
OUTPUT: mesh files + PNG maps + manifest.txt (file, species, variant, height m, tris, texture sizes). No prose.
DO NOT: add text, logos or signs; bake lighting or AO into albedo; model moss, algae or litter (those are textures).
```

## Where they go

The map's Overgrowth section (next pass) places them by the reference's age bands: 5-year weeds and saplings on the
market streets, 12-year ailanthus and sumac walls over most of the map, 25-year closed canopy on the north lots and
the Cut's banks (15–20% of the map). Nyx does the placement, LOD swap and collision; this order is the meshes only.
