---
tags: [idea, raids, map, detroit]
status: shaping
updated: 2026-10-20
---

# Idea - Detroit Map

**The pitch:** a 1:1 crop of Eastern Market and the Dequindre Cut, overgrown, at night, in the rain. Low brick sheds, warehouse roofs, a sunken rail corridor, and a prairie eating the blocks east of St. Aubin. The grapple is the roof route. The infected are the population.

Supersedes [[Idea - Raid Map]]. That note proposed one highway exit at 800 × 800 studs; this one changes the place, the size and the density argument.

## 1. The density argument, corrected

The old note used a PvP density table: 16 players across an area, and anything under ~40 players/km² was called a walking simulator. That is true for a PvP-only extraction shooter. No Dawn has infected, and they are the baseline population, not set dressing.

The metric is not players per square kilometre. It is **encounter cadence**:

| Encounter | Target | Filled by |
|---|---|---|
| Infected contact | every 45–90 s of movement | 80 infected, noise-driven |
| Point of interest | every 60–90 s on foot, 40–60 s on roofs | loot nodes, landmarks, extracts |
| PvP contact | every 10–20 min per player | 16 players, concentrated by loot and noise |
| Extract reachable | within 2–3 min of any point | 5 extracts |

At **0.40 km²**, 16 players is 40/km² and 80 infected is 200/km². The infected outnumber the players five to one. The city is populated; it is just not populated by people. A player can cross 896 m in 160 s at sprint, so the map is never a hike — and the quiet between fights is the game, not the failure.

The PvP density table is still correct for *PvP*, and the extract layout below still uses it. It is simply not the whole population.

## 2. The place

**Eastern Market + the Dequindre Cut**, Detroit. Bounded by Mack Ave to the north, Gratiot to the south, I-75 to the west and the rail viaduct / St. Aubin to the east.

Why this slice and not the others:

| Candidate | Verdict |
|---|---|
| **Eastern Market + Cut** | **Pick.** Shed trusses 8–10 m, warehouse roofs 12–24 m, Cut walls 6–9 m, streets 15–30 m — every height inside the 60-stud rope's band. Three parallel lanes, cross-links every block, a sunken spine, honest overgrowth. |
| Corktown / Michigan Central | Great tower, bad map. The rail yard is 300–400 m of flat gravel — wider than the whole playable frame. MCS is restored, which fights the premise. |
| Milwaukee Junction / Packard | Best single ruin, worst map. The plant is ~700 m long, so a 448 m frame is one building. It was also demolished by 2024, so a 2026 setting cannot use it standing. Keep it for a boss map. |
| Downtown / Woodward | Towers are 20–40 storeys. A 17 m rope cannot traverse them; DL2 needed a paraglider for exactly this. The skyline belongs in the background, not the play space. |
| Brush Park | Ten blocks, mostly new-build. No. |
| Riverfront | A park with no interiors and no height. No. |

**Build the grid rotated ~28° west of north.** That is the fastest way to tell a local the map is real, and the fastest way to tell everyone else it is not.

## 3. The frame

**1,600 × 3,200 studs = 448 × 896 m = 0.40 km².** A 1:1 crop, not a compression.

Every block, street width and building footprint stays true. Only the *count* of blocks is cut. That matters because the rope is tuned to 60 studs; a compressed map makes every gap a rope-length longer and breaks the swing without changing a single number.

- The whole district is ~0.7 km². That is the expansion, not the first map.
- 16 players → 40/km² raw. Add five vertical layers and 15–18 enterable buildings and the same 16 players are spread across far more floor area, so the effective density is lower than the footprint suggests.
- End to end: 896 m, 160 s at sprint, ~2 minutes on the roof line with a rope.

**Engine budget, PC-first:**

| Item | Target |
|---|---|
| Parts | 50–70k (hub is 10,693) |
| Unique meshes | 150–250 |
| Lights | 60–80, of which 8–12 shadowed |
| Texture memory | 300–450 MB |
| Stream target | 600–800 studs, min 64 |
| Enterable buildings | 15–18, the rest shells with locked doors |

The 1,600 × 1,600 "sane ceiling" from the engine research assumes dense downtown interiors at every address. Eastern Market is the opposite: big sheds, open floors, few unique assets per square metre. That is why a frame twice as long fits the same part budget. Mobile is out of scope for this map; a 1,200-stud version would be a different build.

## 4. The seven zones

| Zone | Height | Role | Growth age |
|---|---|---|---|
| **The Cut** | −7 m / −25 st, walls to street | Sunken rail spine. Quiet, wet, rope-only exits at the bridges. The dangerous shortcut. | Wet green: sumac, knotweed on the embankments |
| **Russell Street** | 2–4 storeys, 8–15 m / 29–54 st | Main street. Shopfronts, awnings, fire escapes, the densest loot. Loud. | 12 years: vines to the first floor, weeds in the seams |
| **Shed 3** | Trusses 8–10 m / 29–36 st, ridge 10 m | The arena. Four wings, steel trusses, open floor, roof route above. | 5 years inside, 12 on the edges |
| **St. Aubin warehouses** | 3–6 storeys, 12–24 m / 43–86 st | East wall. Cold storage, loading docks, fire escapes. The vertical fight. | 12–25 years: roof trees, parapet collapse |
| **Gratiot frontage** | 24–40 m / 86–143 st | South gate. Two towers, the landmark you navigate by. | 5–12 years: wind, exposure, little green |
| **The prairie** | 0–3 m | East of St. Aubin. Vacant blocks, rubble berms, sumac thickets, low cover. | 25 years: closed canopy 12–18 m |
| **The viaduct** | +3 m / +11 st, rail deck | East boundary. Elevated rail, gantries, the generator extract. | 12 years: ballast trees, grape |

Five extracts, cost-asymmetric:

1. **Cut north portal** — covered, slow, rail tunnel.
2. **Gratiot tower roof** — exposed, fast, rope or stairs.
3. **Shed 3 loading dock** — centre, always open. The Hunt-style fixed pressure point.
4. **The viaduct** — needs a generator switched on.
5. **I-75 storm drain** — far, covered, the safe-but-long exit.

Rotation: two of the five are inactive per server, so no extract becomes "the good one".

## 5. Routes and layers

Three long lanes: **the Cut** (quiet, sunken), **Russell** (loot, open, loud), **the roof line** (fast, exposed). Cross-streets every block keep any one lane from becoming a tunnel, and a dogleg at Gratiot kills the end-to-end sightline.

Five vertical layers:

| Layer | Studs | What lives there |
|---|---|---|
| Basement / Cut | −25 to 0 | rail tunnel, storm drain, service rooms |
| Street | 0 | market floor, wrecks, infected, the loud route |
| Mezzanine | 11–44 | awnings, fire escapes, loading docks, the Cut bridges |
| Roof | 43–110 | shed truss, warehouse decks, water tanks, roof camps |
| Sky | 110–170 | viaduct, cranes, the Gratiot towers |

**Height is a currency, and the rope only spends it.** Every swing loses height. The route ladder descends: warehouse roof → shed truss → truck roof → Cut floor. The map has to sell height back with stairs, hatches, ladders and the viaduct ramp, or the roof line is a one-way trip.

**The ground has to compete.** Ground route: 0.75× the roof route's length at 2× the risk. Two of the three main extracts sit at street or basement level. Roofs get wind, exposure and silhouette; streets get cover, smoke, awnings and the infected.

## 6. Grapple lines

The rope locks at the distance you attach, up to 60 studs. Anchors are authored 35–45 studs (10–13 m) above the takeoff deck, spaced 35–45 studs along a route.

| Crossing | Studs | Metres |
|---|---|---|
| Single rope | 70–85 | 20–24 |
| Chained, two ropes | 90–130 | 25–36 |
| Anchor above deck | 35–45 | 10–13 |
| Anchor spacing along a route | 35–45 | 10–13 |
| Minimum swing length | 20 | 5.6 |

The geometry: an anchor 40 studs up with a 55-stud rope gives √(55² − 40²) ≈ 38 studs of horizontal reach at deck level, so one rope crosses about 75 studs. A low anchor gives a short pendulum, which is a drag, not a swing — do not author anchors below waist height.

Three named lines to build first:

- **The Cut bridges.** From the Cut floor, attach to a bridge underside 25 studs up, swing under it, land on the opposite embankment. The only way out of the Cut that is not a ladder.
- **Shed 3 truss → St. Aubin roof.** Attach to a warehouse parapet from the shed ridge, cross St. Aubin, release onto the loading dock roof.
- **Viaduct → Gratiot tower.** The long line. 110 studs to 143, two ropes, exposed to the whole southern map. It should feel like a bad idea that works.

A swing line is a kill zone. Attach point, length and release fix the landing within 2–3 m, and anyone who sees the rope knows where it ends. The counterplay is the low point of the arc, the landing lip, and the fact that a solid hit severs the line.

## 7. Loot, spawns, infected

- **500 loot nodes** — one per ~800 m². Hot: market stalls, Shed 3, the pharmacy on Russell. Warm: warehouses, offices, cold storage. Cold: prairie shells, the Cut service rooms.
- **15–18 enterable buildings**, each with a full ground floor, a stair core and at least one upper floor. Below ~150 m² of enterable floor, seal it; one-room stubs teach players that doors are empty.
- **24 spawns in 8 clusters**, ringing the long edges 80–120 m in, with three clusters pushed deeper. 120 m minimum separation plus a line-of-sight check. 8-second freeze, facing inward.
- **80 infected**, ~20 active within 200 studs of a player, respawning at the edges. Noise-driven: gunfire pulls them, the market centre is a permanent hotspot, the prairie and the Cut hold more than the roofs. They are the pacing system that fills the ten minutes between PvP contacts.

## 8. Vegetation

A gradient, not a district:

- **5 years** — the market floor, Russell, the Gratiot frontage. Weeds in the seams, moss on north walls, vines to the first floor. Sightlines intact. This is where the map is still readable as a city.
- **12 years** — most of the map. 6–10 m ailanthus, sumac walls, knotweed monocultures, ground floors swallowed, roof growth starting. Cover without losing silhouette.
- **25 years** — the prairie and the north sheds. Closed canopy 12–18 m, vines to the third floor, trees on the roof, parapet collapse. 15–20% of the map only, because it eats readability.

The paved market stays bare, the Cut is green, the prairie is a forest. That contrast is the map's identity: **green edges, hard centre.** Do not let the whole thing become Villedor; Detroit is zone 6a with a short season and less moss than Seattle or Poland.

Palette, fog and species are in [[Reference - Detroit Overgrowth]].

## 9. What to cut, and what could go wrong

- **No signage.** The Cut's graffiti is the signature the rule deletes. Replace it with abstract colour fields, blank enamel panels and structural marks, or the corridor reads sterile.
- **The Cut is a natural funnel.** Give every segment at least three exits — bridge, ladder, rope — or it becomes a death pit with one mouth.
- **40/km² is not "rare PvP" by arithmetic.** It is rare by behaviour: players avoid each other, move quietly, and only converge on loot and noise. Test it. If it feels crowded, lower the player count to 12 before shrinking the map.
- **15–18 enterable buildings is a compromise.** If the map feels empty, add interiors before adding area.
- **80 Humanoids is a server cost.** Twenty active is the budget; the rest are dormant. Measure before authoring the spawner.

## 10. Open questions

1. Is the whole district (0.7 km²) the expansion, or do we push north into the market sheds?
2. Is the Cut playable at night, or a black hole that nobody enters? It needs its own light source — a few sodium lamps under the bridges.
3. Does the first map ship at 16 players or 12? The density argument says 12 for the feel in this note; 16 is the design ceiling.
4. Is the Gratiot tower the only landmark visible from everywhere, or does the map need a second silhouette for the north end?
5. Does the infected count scale with players, or is it fixed at 80?

## 11. Decisions to approve

1. **Eastern Market + the Cut**, built on the real ~28° grid. Recommended: yes.
2. **1,600 × 3,200 studs, 1:1 crop, 0.40 km².** Recommended: yes.
3. **Five cost-asymmetric extracts, two rotated.** Recommended: yes.
4. **80 infected as the baseline population, 20 active.** Recommended: yes, pending a server measurement.
5. **Five vertical layers and a descending roof route.** Recommended: yes.
