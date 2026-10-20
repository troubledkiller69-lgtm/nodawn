---
tags: [nodawn, hub, tooling]
updated: 2026-10-18
---
# Hub — How It Is Built

Back to [[No Dawn - Home]] · Gotchas: [[Lessons|Lessons]]

## HubBuilder
- `ServerStorage.DevTools.HubBuilder` builds the whole hub (`Workspace.Hub` and the terrain) from code. It runs at
  edit time only, never at runtime.
- **Sections, in build order:** Atmosphere, Ground, Yard, CellBlock, Supply, WestWing, Admin, FrontField, Detail,
  People, Zones, Surface, Nature. Each gets `ctx = { Kit, root, rng = Random.new(1337), interaction, ground }`.
- **Shared rng rule:** layouts draw from one shared `ctx.rng`. Any change must keep the same number and order of
  draws (or use its own `Random`), or every later layout shifts. SectionGate checks this ("shared rng: unchanged").
- **Kit** (about 155 k characters): the palette (No Dawn PBR materials), part helpers, facade helpers, props,
  fences, foliage, NPCs with poses (`Kit.POSES`), and the generated-prop helpers below.

## Generated props pipeline
1. `generate_mesh` with "realistic ... weathered, worn ... no text, no logos". Run at most two jobs at a time (a
   publish rate limit). Keep prompts under about 450 characters.
2. Check every result in a capture (pseudo-text on labels, cartoon looks, wrong shapes). Rejects go to
   `DevTools._RejectedProps`.
3. `DevTools.PropTools.normalize(model, name, opts)` flattens the segments, strips tags, measures the real bounds
   from the mesh vertices, puts the pivot at the bottom centre with the front facing −Z, and stores
   Width/Height/Depth. The result goes in `HubAssets.Props.<Name>` (42 templates now).
4. `Kit.asset(parent, name, cf, { height|width|depth|scale, stretch, keepShape, back, collide })` places it. It
   returns nil without the template, so every caller keeps a parts fallback.
   - Vehicles: `Kit.sedanAsset` (hood up, wheel off, `settle`), `Kit.busAsset` (stretched coach, plates, bars, flat
     tyre), `Kit.pickupAsset` (colliders that follow the silhouette, cargo).
- Organic things (crops, animals) come out cartoonish: use the PBR foliage library or parts. Long objects come out
  short: stretch a flat-ended generation and cover the stretched sides.

## Custom materials
PBR maps are authored in Python at real-world proportions (`py -3.14`, numpy + Pillow), served on localhost,
uploaded with `upload_image` and wired into MaterialVariants under `MaterialService.NoDawn` (NdCmuExt, NdCmuInt,
NdConcrete, NdSlab, NdWetMud, NdBinBag...). A part's Color multiplies the colour map.

## Night, weather, sound
`ReplicatedStorage.Shared.Config.World.Fixed` pins the hub at 23.2 h with rain and fog, rendered at hour 13
("day-for-night"). `WorldClock` / `WorldController` apply it; `AmbienceController` plays the soundscape.

## Safety net
- **Backups before every edit:** `DevTools.SectionsBackup_<date>` and `KitBackup_<date>` (latest: `_20261018`).
  Rollback = copy the backups back and rebuild.
- **Draft first:** edit shared modules on a copy (`KitDraft`, `SectionsDraft`), test-build them, then promote with
  `ScriptEditorService:UpdateSourceAsync`.
- **SectionGate:** `require(DevTools.SectionGate)(section, budget, backupFolder, oldKit)`. It rebuilds a section
  from the backup and from the current code, then diffs collidable geometry, gameplay tags, shared-rng use, the part
  budget and buried decoration.
- **LeakProbe:** player-sized sweeps through the old and new builds that find new gaps in walls and fences.
- Rules the gates protect: vendors, lockers, spawns and doors stay clear; nothing taller than about 8 studs within
  12 studs of a perimeter fence; invisible box colliders keep movement identical when a prop's look changes.
