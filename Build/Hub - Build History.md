---
tags: [nodawn, hub, history]
updated: 2026-10-18
---
# Hub — Build History

Back to [[No Dawn - Home]] · Decisions and numbers: [[Records/Realism Ledger]] · Full checklist: [[Tracker]]

## 2026-10-16: greybox and first detail
- **Milestone B:** the prison laid out in greybox: perimeter wall and guard towers, yard and spawn, cell blocks,
  supply (quartermaster and armory), medical, workshop, services, vault room, deployment sally port, circulation.
- **Catalog avatars and NPCs:** curated Marketplace clothing for survivors; 10 dressed NPCs (vendors, guards, clerk,
  medic, mechanic, camp survivors) with idle animations.
- **Building detail pass:** facade helpers (windows, doorways, parapets, stains, vents...), generated materials and
  the first generated prop meshes.

## 2026-10-17: realism, weather, living hub
- **Section detail** on every building, gated with the new SectionGate and LeakProbe tools (Admin had open windows; sealed).
- **Realism pass:** Realistic lighting; fixed a project-wide bug where terrain drew 1.5 studs too high and buried every
  floor; block-masonry walls; a terrain pond; 12 real PBR pine variants and a Nature section (pines, bushes, ivy).
- **Living hub and weather:** soundscape (5 ambient beds, 24 prop loops, ravens, indoor reverb), day/night, fog, rain
  and a thunderstorm with lightning; the flat courtyard slab became real terrain (dirt, mud, grass). A pebble scatter
  was tried and removed at your request.
- **Signs out, boulders in:** all 71 signs and painted words removed; 9 big boulders at the pond and beyond the fences.

## Realism pass v2 (2026-10-17)
- **Stage 1, surfaces:** custom PBR materials authored at real-world scale (weathered painted block walls, poured
  concrete, walkway slabs, see-through chain-link, wet mud with standing water). The hub became a permanent foggy,
  rainy night (rendered "day-for-night", because Roblox draws night skies black).
- **Stage 2, props:** tents and the sorting stall rebuilt to look real, knotted trash bags, benches, picnic tables,
  crates, drums, jersey barriers, dumpster; burn barrels with particle fire.

![[stage1_front_field_fog.jpg|600]]
![[stage2_stall_outside_garage.jpg|600]]

## Realism pass v3 (2026-10-17): garage, rooms, farm, people
- New pipeline: realistic `generate_mesh` props, normalized into `HubAssets.Props` and placed by `Kit.asset`
  (40 props by the end of v3).
- **Garage:** sedan on the lift with the hood up and a wheel off, engine stand, jack, tool chests, welding cart,
  compressor, hoist, tyre stacks, shelving; real ceiling lights.
- **Rooms:** medical ward (9 beds, curtains, IV stands, trolleys, 15 NPCs: patients and medics); quartermaster and
  armory stock; Services (registry counter, waiting chairs, lockers, offices); every cell with a bunk and a toilet.
- **Farm:** crop rows, picket fence, IBC tote, wheelbarrow, hay, an animal pen with pigs.
- **People:** 59 NPCs hub-wide in poses (lying, sitting, tending, carrying...), 52 breathing, 7 asleep.

![[garage_car_hood_up.jpg|600]]
![[medbay_wide.jpg|600]]
![[farm_pig_pen.jpg|600]]

## Stage 4 (2026-10-18): vehicles at real scale
The bus, pickup and wrecked sedan were toy-sized (the bus was 13.5 studs; a real one is about 36).
- **Prison transport bus, 36 × 8.7 × 10 studs (10 × 2.5 × 2.8 m).** The mesh generator can't make long vehicles:
  every bus came out about twice as long as wide. So a generated flat-fronted coach is stretched to length with
  round wheels. Welded steel plates over the lower sides hide the stretching and make it a barricaded bus. It also
  has bars over the windows, razor wire on the roof and a flat front-right tyre. It stands east–west in line with
  the east jersey barrier (along its old line, at full length, it would have stood in the pond).
- **Pickup, 17.6 studs (4.9 m):** a weathered green 1990s regular-cab truck, fuel cans and a crate in the bed, nose
  toward the field gate.
- **Wrecked sedan:** real length, hood up over the engine, a wheel lying in the grass, the body slumped onto the bare
  brake disc, dragged nose-first to the west fence.
- Gates: Yard and FrontField "shared rng: unchanged", no gameplay piece lost; garage unchanged. Hub 10,693 parts.

![[bus_front_three_quarter.jpg|600]]
![[bus_rear_three_quarter.jpg|600]]
![[pickup_side_yard.jpg|600]]

More shots: `Screenshots/stage4` (bus close-ups, pickup stretch comparison).
