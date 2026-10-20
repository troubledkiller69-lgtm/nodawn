---
tags: [reference, art, detroit]
updated: 2026-10-20
---

# Reference - Detroit Overgrowth

Visual source pack for [[Idea - Detroit Map]]. 1 stud = 0.28 m. Heights are metres first, studs second. Anything marked (est.) is an estimate.

## 1. References

| # | Reference | Steal this |
|---|---|---|
| 1 | [DetroitUrbex](https://detroiturbex.com/) | Sequenced decay, room by room. Real ruins are **less** green than game art assumes. The brick / limestone-sill / grey-block palette. |
| 2 | [Commons: Packard Plant](https://commons.wikimedia.org/w/index.php?search=Packard+Plant+Detroit&title=Special:MediaSearch&type=image) | A concrete frame a century old in a wet climate. The window bay as a rhythm unit. Growth takes the roof edge and slabs, not the facade. |
| 3 | [Urban prairie](https://en.wikipedia.org/wiki/Urban_prairie) | Lot-scale geometry: mown strip, bramble core. The hard, straight edge where maintenance stops. Human absence, not jungle. |
| 4 | [Varosha](https://en.wikipedia.org/wiki/Varosha,_Famagusta) | Vegetation entering through balconies and windows. The interior vine curtain, and how leaves gutter a room's light. |
| 5 | [Pripyat](https://en.wikipedia.org/wiki/Pripyat) | Birch through asphalt, moss indoors. The green tunnel: canopy meets overhead while the road stays walkable. |
| 6 | [Chernobyl Exclusion Zone](https://en.wikipedia.org/wiki/Chernobyl_Exclusion_Zone) | Rooftop trees, streets under canopy. The cool grey-green grade — exposed concrete goes pale, not mossy. |
| 7 | [Hashima Island](https://en.wikipedia.org/wiki/Hashima_Island) | The control image: collapse with almost no vegetation. Some buildings must stay bare so the green ones read. |
| 8 | [Craco](https://en.wikipedia.org/wiki/Craco) | Trees in vertical stone cracks. How little soil a mature tree needs, and how roots follow a joint line. |
| 9 | [Dying Light 2](https://en.wikipedia.org/wiki/Dying_Light_2) | Vertical staging, not species: rooftop plots, vines over parapets, hard pools of UV cutting the dark. Villedor is temperate European — copy the composition, not the plants. |
| 10 | [The Last of Us Part II](https://en.wikipedia.org/wiki/The_Last_of_Us_Part_II) | Seattle staging: ivy curtains, moss mats on flat roofs, litter drifted against thresholds. Halve the density. |
| 11 | [Children of Men](https://en.wikipedia.org/wiki/Children_of_Men) | The grade. Wet grey-green, sodium and fluorescent in one frame, overcast diffusion, no directional key. |
| 12 | [Stalker (1979)](https://en.wikipedia.org/wiki/Stalker_(1979_film)) | Damp sepia-green, fog as a wall rather than a haze, standing water indoors. |
| 13 | [Simon Stålenhag](https://www.simonstalenhag.se/) | Composition only: hard geometry against soft mass, low saturation, one accent to aim the eye. |
| 14 | [Unsplash: abandoned Detroit](https://unsplash.com/s/photos/abandoned-detroit) | Colour truth for wet brick. Calibrate albedo, not composition. |

## 2. Species and placement

Detroit is USDA zone 6a–6b with roughly 180 frost-free days (est.). A shorter season than Seattle or Poland means leggier growth, more visible trunk, less moss. Do not import Villedor's lushness.

| Species | Grows in | Visual signature | Height | Build as |
|---|---|---|---|---|
| Tree of heaven | Vacant lots, alley edges, scuppers, parapet joints, ballast | Pale straight trunk, huge compound leaves, dense root suckers | 15–25 m / 54–89 st; suckers 2–4 m / 7–14 st | Mesh, two trunks plus a sucker clump |
| Staghorn sumac | Rail corridors, lot edges, rubble berms | Clonal thicket, fuzzy red-brown stems, maroon seed cones | 3–6 m / 11–21 st | Cheap mesh clump |
| Japanese knotweed | River corridors, alley seams, asphalt cracks | Hollow canes in a solid leaf wall; dead canes persist all winter | 1.5–3 m / 5–11 st | One wall mesh, not individual canes |
| White mulberry | Alleys, fence lines, ledges, gutters | Crooked dark trunk, glossy leaves, purple staining below | 10–15 m / 36–54 st | Mesh, one or two hero variants |
| Grape, Virginia creeper | Fences, poles, facades, window openings | Grape: rope lianas. Creeper: flat adhesion, crimson in autumn | 5–20 m / 18–71 st | Mesh curtain plus decal |
| Boxelder, cottonwood | Roof decks, parapets, lots | Weak-wooded; seed lands in every gutter, so roofs grow trees | 10–30 m / 36–107 st | Hero mesh only |
| Moss, algae, lichen | North faces, splash zones, gutter lines, interior plaster | A film 1–3 mm thick, blackens when wet | centimetres | Texture only |
| Grasses, ragweed, goldenrod | Lot interiors, curb seams, slab cracks | Waist-high fuzz that moves and breaks silhouettes | 0.5–1.5 m / 2–5 st | Cards or small clumps |

Model: tree of heaven, sumac, knotweed, mulberry, vine curtains. Texture: all moss, algae, lichen, litter, most grass.

**The variety fact:** tree of heaven is shade-intolerant. It owns 5–15 year sites and dies out under a closed canopy, leaving 25-year stands as mulberry, boxelder and cottonwood with an open, legible understory. That gives the age bands a real ecological reason to look different.

## 3. Staging over time

| Age | What changed | Read |
|---|---|---|
| **5 years** | Gutters clogged, weeds in seams, saplings to 3 m (11 st), moss on north walls, vines to the first floor | Sightlines intact. Spawns, extraction edges, contested streets |
| **12 years** | 6–10 m (21–36 st) ailanthus, sumac walls, knotweed monocultures, shrubs swallow ground floors, roof growth starting | Cover without losing silhouette. Most of the map |
| **25 years** | Closed canopy 12–18 m (43–64 st), vines to the third floor, trees on roofs, parapet collapse, rubble berms, dark wet interiors | 15–20% of the map only. Eats readability and performance |

Build density as a gradient, not as districts. Every route crosses a band boundary at least once, and the boundary is visible from cover.

## 4. Palette and lighting

Night foliage is never saturated green. Albedo value 0.08–0.22, hue 95–140, saturation 8–25% (est.). Under cool fog leaves read teal-grey; under sodium, olive-tan. Wet leaves need specular, not saturation.

Wet surfaces drop 30–40% in value and shift blue-grey. Brick goes deep maroon, asphalt near-black with specular streaks. Puddles become the brightest thing after the lamps and double every source — the cheapest depth cue, and a free footstep tell.

Fog: exponential plus a ground layer at 0–1.5 m. Detail reads to 30 m (107 st), mass to 80 m (285 st), silhouette beyond. Tint fog with the key light, never neutral grey.

With signage banned, chroma comes from practicals: sodium amber, cold LED, hazard amber, fire. Pick two. Avoid jungle green (#2E7D32), sunlit lime (#8BC34A), swamp mud (#4E5B31), and any golden-hour warmth.

## 5. Roblox translation

**Meshes:** trunks (three LODs), sumac clumps, knotweed walls, vine curtains, window vine clusters, roof shrubs, grass clumps.

**Terrain:** the soil plane on lots, earth berms, rubble mounds. Skip terrain grass decoration — it dates the build instantly.

**Texture / decal:** moss films, algae staining, wet streaks, soot, litter, puddle films, blank sign panels (frames and brackets, no lettering).

**Scale rule** that enforces no painted depth: under 4 cm (0.14 st) is texture; 4–15 cm is a shallow mesh or decal; above 15 cm must be geometry. Cracks become recessed strips, gravel and rubble become meshes, grass becomes cards.

**Repetition:** 3–4 variants per species × 3 scales × rotation, plus vertex tint. Cluster in odd numbers with one outlier per cluster; twenty identical bushes read at 20 m (71 st).

**Budget:** cards past 40 m (143 st), LOD0 inside 15 m (54 st).
