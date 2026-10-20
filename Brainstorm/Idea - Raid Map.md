---
tags: [idea, raids, map]
status: dead
updated: 2026-10-20
---

# Idea - Raid Map

> **Superseded 2026-10-20 by [[Idea - Detroit Map]].** The 800 × 800 highway exit was written for a world where the density math counted players only. Keep this note for the reasoning about small maps and the size fork; the map itself is replaced.

Reconciled against [[Idea - Raid Frame]], [[Idea - Movement Frame]], [[Idea - Combat Frame]] and [[Master Project Context]] §3–§5. This answers the question the other notes were waiting on: **where does any of it happen.**

**The pitch:** not a county. One highway exit that failed — truck stop, motel, container yard, six houses, a farm at the edge, and a quarantine cordon nobody came back from. Seven zones inside 224 metres, so a run crosses three of them and the horror stays in the room.

## The size reality, stated once

800 × 800 studs is **224 × 224 m — five hectares.** For scale, a Walmart supercentre and its car park is six to eight hectares. So the map is not a district, and it is not "farms, wooded roads and scattered homes" as a region. At 0.28 m per stud, the only American places that fit are a highway exit, a motel and its forecourt, or a single town block.

That is not a limitation to apologise for. It is the design:

- every gunfight is a decision, because help is forty seconds away
- landmarks do the work of a compass, which matters because there is no text
- a run is fifteen to twenty-five minutes of dense choices instead of forty minutes of walking

If the fantasy needs a horizon, the map has to be 2,400+ studs and 24–32 players, and half the numbers in every other note get rewritten. That fork is at the bottom.

## What the ChatGPT proposal gets right

- One contiguous region, not a slice that expands later. Expansion fragments both the loot economy and the player population.
- A town centre with dangerous landmark buildings around it.
- Greybox the entire loop before any art.
- American rural-to-suburban identity. It matches the hub, and it is not military science fiction.

## Where it breaks

1. **Four threat bands need kilometres.** Rural outskirts → residential → commercial → high-threat only reads as a gradient if travel between bands costs something. In 224 m the "safe" band is thirty seconds from the "extreme risk" band. The bands become labels, not geography.
2. **Banding a small map makes it emptier.** Sixteen players across four rings is four per ring. The contact-density target in [[Idea - Raid Frame]] dies in three of the four.
3. **Everyone starts at the safe edge and pushes inward.** That is a predictable runway into a camped choke. An extraction map needs several routes that are all live at minute one.
4. **"Farms, barns, wooded roads" is the most expensive terrain and the least useful.** Trees block sightlines, which are what make fights readable; terrain eats performance; barns are large empty boxes. The hub already taught this lesson with foliage and organic props.
5. **Four high-threat hotspots is four landmarks that each need art.** Hospital, police, military, research. Pick one.

## The counter-proposal: one exit, not one county

Compress the gradient **horizontally**. Instead of bands you cross, make zones you choose between. All seven sit twenty to forty seconds apart, so a run naturally touches two or three, and which two is the player's decision.

Threat becomes **density and sightline**, not distance. The farm is quiet because it is open and empty. The container yard is lethal because it is tight and full of noise. Same map, same minute.

### Layout

Coordinates in studs, origin at the south-west corner, x east, z south.

| Zone | Footprint | What is there | Threat |
|---|---|---|---|
| A Truck stop | x 180–380, z 180–330 | Fuel canopy over four islands, convenience store, diner, two-bay garage, gravel rig park with three dead tractor-trailers, tank farm | High |
| B Motel | x 180–360, z 470–660 | Two-storey L, sixteen rooms, external walkway, office, manager's flat, empty pool, wrecked lot | Medium |
| C Residential | x 450–700, z 400–660 | Six houses on a cul-de-sac, detached garages, fenced yards, corner store, one flooded basement | Low–medium |
| D Container yard | x 470–700, z 190–350 | Thirty containers stacked one to three high, warehouse shell, forklift, flatbeds, chain-link | High |
| E Farmstead | x 40–170, z 330–600 | Farmhouse, barn with loft, sixty-stud silo, equipment shed, crop rows, fence lines | Low |
| F Quarantine cordon | x 60–200, z 120–180 | Highway checkpoint: tents, barriers, decon frames, one leaning watchtower, command tent | Extreme |
| G Substation and clinic | x 600–700, z 560–640 | Four cells, evidence locker, front desk, six-bed clinic next door | Extreme |

Three silhouettes visible from anywhere, so a player is never lost and never needs a sign: the **water tower** by the junction, the **grain silo** at the farm, and the **lit fuel canopy** at night.

## Extraction points

Five, each testing a different system. Requirements are never permanent gear — the same rule as the grapple: nothing you can lose may gate an exit.

| Extract | Where | Requirement | What it costs |
|---|---|---|---|
| South gate | County road, z 780 | none | Eighty studs of open road, watched from the substation |
| Farm track | West edge, z 520 | none | A hundred and twenty studs of open field, no cover |
| Highway overpass | North, z 60 | none | A ramp choke and a sightline from the cordon |
| Cordon decon | Inside the cordon | Keycard found in the command tent, consumed | A ninety-second hold that emits noise at eighty studs |
| Motel roof | Via the fire escape | Generator started in the garage | Lights the motel and broadcasts for two minutes at a hundred and twenty studs |

The last one is the showpiece. It is the only extract that turns the map's own systems — noise, power, light — into a decision. Cut it first if v1 is tight.

## Threat without rings

Static density per zone, then noise moves it. No dynamic heatmap in v1: it depends on infected AI and a noise system that do not exist yet, and designing a map around an unbuilt system is how maps get rebuilt.

| Zone | Baseline infected | Pulled by gunfire from |
|---|---|---|
| Farmstead | 6–8 | the cordon, the motel |
| Residential | 8–10 | the substation, the truck stop |
| Motel | 10–12 | the truck stop, the container yard |
| Truck stop | 12–15 | everywhere within 220 studs — it is the loudest place on the map |
| Container yard | 6–8 | the truck stop |
| Cordon | 12–16 | the highway |
| Substation | 8 plus the keyholder | the residential street |

Baseline totals 62–77 against a sixty-alive cap, so the map breathes instead of stacking. The elite keyholder patrols between the cordon and the substation and drops the key to the substation armory — one key, two places to fight over it.

## Day, dusk, night

The hub is permanent night, so light is the thing you leave the prison for.

- **Day:** 600-stud visibility, fields and forecourts exposed, infected sluggish in the open, loot weighted down one tier.
- **Dusk:** 300 studs, the best light in the game, the most players.
- **Night:** 200-stud fog, interiors black, canopy and one motel lamp lit, cordon floodlight flickering, density up twenty per cent, night-only spawns, loot weighted up one tier.

Weather is the hub's system reused, lighter by day. No live cycle in v1: time of day is a property of the server.

## The look

The exit that failed. Everything is about the road: a queue of abandoned cars on the highway, a coach on its side, luggage, a barricade of furniture. No text anywhere — the hub's rule holds, and it costs nothing because silhouette and light already do the wayfinding.

Colour is the identity and it is free: warm sodium at the truck stop, cold blue-white at the cordon, dying green fluorescent at the motel, no light at all on the residential street. Fog density per zone does the rest.

## Loot arithmetic

Ninety-two containers, using the tiers in [[Idea - Raid Frame]]:

| Zone | Common | Uncommon | Rare | Elite |
|---|---|---|---|---|
| Farmstead | 10 | 1 | — | — |
| Residential | 18 | 2 | — | — |
| Motel | 16 | 3 | — | — |
| Truck stop | 12 | 3 | 1 | — |
| Container yard | 8 | 2 | 1 | — |
| Cordon | 5 | 2 | 1 | — |
| Substation | 4 | 1 | 1 | 1 |
| **Total** | **73** | **14** | **4** | **1** |

At the note's placeholder values that is roughly 8,000 Chits of map value. Sixteen players sampling twenty per cent, with half of them extracting, yields about 500 Chits each — just above a 400-Chit kit. That is the correct side of tight, but it is arithmetic on invented prices. **Pull the real values out of `Config/Items` before any of this is quoted as fact.**

## What it costs

- **Build.** The hub is 10,693 parts after weeks of passes. Seven zones with interiors is 30,000–50,000 parts. The greybox alone should target 3,000.
- **Streaming.** 224 m with interiors needs `StreamingEnabled`, which the hub has never used. This is the largest technical unknown in the project.
- **Infected navigation.** Sixty agents with interiors needs a pathfinding budget or a flow field. The hub's fifty-nine NPCs are static and prove nothing.
- **Server cost at sixteen players** has not been measured even in the hub, and that number gates the whole player-count ladder.

## The fork

1. **Dense exit — recommended.** 800 × 800, seven zones, horror in the room. Two or three passes to greybox, playable sooner, and sixteen players is the right number for it. The identity survives: the farm, the residential street, the commercial strip and the high-threat landmark are all here, just closer together.
2. **Vast country.** 2,400 × 2,400 or more, 24–32 players, streaming mandatory, and the first playable version is a corner of it. This is the only version where the ChatGPT gradient is real geography. Every loot, noise and traversal number in the other notes gets rewritten.
3. **Two maps, later.** Ship the exit, then a rural map once the loop is proven. The cheapest way to have both.

## Open questions

1. Does the south spawn exist at all, or do players arrive by truck at the junction and start at the centre?
2. Is the motel roof extract worth its system cost, or does it become a ladder camp?
3. One keyholder roaming two buildings, or one fixed elite per high-threat zone?
4. How much of the residential street is interior? Six full houses is a lot of rooms for the loot they hold.
5. Does the east boundary need a rail line, or is a treeline enough?
