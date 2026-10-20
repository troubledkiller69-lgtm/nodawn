---
tags: [idea, raids]
status: shaping
updated: 2026-10-18
---

# Idea - Raid Frame

Reconciled against [[Master Project Context]], [[Addendum - Infected and Firearms]], [[Phase 1 Handoff]].

**The pitch:** design the map and the systems for 24 players, ship the first raid at 16, and let the player decide when to leave while the server decides when it dies. One map, no bosses, infected that react to noise.

## What the master context changed

Five things in v0 were wrong or incomplete:

| v0 said | Master context says | Resolution |
|---|---|---|
| 12 players | Target 20–24, subject to perf | Design for 24, launch at 16, gate 20 and 24 on measured server cost |
| 20-minute session | Open-ended, not an arbitrary countdown | No player-facing timer. The server has a long life instead |
| No matchmaking | Squad-size-aware matchmaking, relaxed when needed | Add it, but it is a queue, not a skill system |
| One map, fixed night | Raids have day/night risk, night is worse | Time of day is a property of the server, not a live cycle |
| No death detail | Disconnect counts as death; fallback is clothes and a knife | Write both into the death rules |

## The frame

|             | Value                           | Why                                                                         |
| ----------- | ------------------------------- | --------------------------------------------------------------------------- |
| Maps        | 1                               | The loop is unproven                                                        |
| Footprint   | 800 × 800 studs (~224 × 224 m)  | Sized for 20–24. End-to-end walk ~50 s                                      |
| Server life | 50 minutes, then it closes      | Long enough to feel open-ended, bounded enough that loot can reset          |
| Containers  | 80–100                          | 1 per ~6,500 sq studs (~500 m²). You cross a room to find one, not a street |
| Players     | 16 at launch, 24 design ceiling | See below                                                                   |
| Infected    | 40–60 alive, noise-driven       | Not a horde shooter                                                         |
| Bosses      | none                            | An elite keyholder does the job                                             |

## Player count, staged

24 is not a design problem. It is a **population** problem and a **replication** problem.

- Population: 24-slot sessions only feel alive if there are hundreds of concurrent players. A launch with 60 concurrent players cannot fill two 24-slot servers with squads of comparable size. Matchmaking quality collapses and queue times kill retention.
- Replication: 24 players plus 60 infected plus 100 interactive containers, on a map that has to stream, has never been measured. The hub's 60 Hz / 63 fps came from a Solo playtest.
- Encounter density: 24 on 640,000 sq studs is 2–4 meetings per run, which is right. 24 on a smaller map is a meat grinder.

**Decision:** build the map and the systems for 24. Configure the launch place at 16. Raise the cap to 20, then 24, only after measuring server frame time, client fps and replication bandwidth at each step with the raid full.

The map must not be shrunk for 16. Instead, loot density scales with the live player count at server start, so a 16-player server is not a picked-over 24-player map.

## Session model

The master context says raids are open-ended. The loot says otherwise: shared, single-use containers mean a server left running for four hours is an empty map.

Both can be true at once:

- **The player has no timer.** Deploy when you want, leave when you want, from any extract.
- **The server has a life.** It is created for a batch of players, runs ~50 minutes, then warns at 45 and closes at 50. Everyone still inside is treated as extracted, with a flag on the result so it cannot be farmed as a free safe exit — the last five minutes are a squeeze, not a gift.
- **No late joiners in v1.** New deployments go to fresh servers. This keeps shared loot meaningful and avoids a join-in-progress player arriving at a stripped map.

That is the minimum machinery that satisfies "open-ended" without pretending loot can be infinite.

## Loot density

The number that matters is value per successful run: **1.5–2× the cost of the kit brought in**. Everything else follows.

For a 50-minute raid with 20 players, a realistic uncontested route loots 15–25 containers. So:

- 80–100 containers on the map.
- Most of them are disappointing on purpose.
- The value lives in the handful of places other players also want.

| Tier | Count | Value each | Where |
|---|---|---|---|
| Common | 65–75 | 20–60 | Houses, cars, lockers, desks |
| Uncommon | 12–18 | 80–200 | Workshop, clinic, security office, pharmacy |
| Rare | 4–6 | 300–700 | Armory cage, manager's office, locked storage |
| Elite | 1 | 800–1,500 | Behind the key |

Total map value lands around 6,000–8,000 Dollars. That is deliberately less than twenty players × a good haul: scarcity is the point. Not everyone gets rich in the same session.

**Loot half-life.** Common and uncommon containers can restock once, 25–30 minutes after being emptied, at a reduced tier. Rare and elite never restock. That keeps a long-lived server playable without turning it into a farming route.

*These values are placeholders until we pull real sell values out of `Config/Items`. Without those, the Dollar column is a guess.*

## Day and night

Night should be the reason a player chooses a route, not a lighting preset.

- Time of day is fixed per raid server and chosen when it is created: day, dusk, night.
- Day: better visibility, fewer threats, lower-value spawn weighting.
- Night: apex predators, worse visibility, better loot weighting, extract pressure.
- The player picks the risk at deployment. No live cycle in v1.

This also solves the tone problem: the hub is permanent night, so daylight becomes the thing you leave the prison for. The dark is where you are safe and the light is where the world is. That reads better than night everywhere.

## Infected roadmap

The addendum lists five archetypes. Shipping them together is how the first map dies.

- **v1 — standard infected.** Noise events, investigation, chase, lose target. Gunfire pulls a radius; suppressors shrink it. No omniscience.
- **v2 — fast pursuer.** One archetype, readable telegraph, counterplay in confined space.
- **v3 — heavy.** Barricade pressure, slow, area control.
- **v4 — bite infection, Antizin, reanimation.** This is a persistence-adjacent system; it needs the raid death rules stable first.
- **v5 — night apex predator.** Only once night raids exist and are worth playing.

Do not build two overlapping apex classes. The addendum itself flags that as unsettled.

## Bosses

Still none in v1. One **elite keyholder** — slow, high health, telegraphed melee, drops the armory key — turns a locked room into a contested landmark. It is an attention magnet with a health bar, which is the only job a boss has in an extraction shooter.

A named miniboss with phases and an arena is a v2 conversation, after the map proves it can hold a fight.

## Death and disconnect

- Equipped gear, backpack and unsecured loot drop on the body.
- Body persists five minutes, lootable by anyone.
- Vault and eligible secure-container contents survive.
- **Disconnect inside a raid counts as death.** A short reconnect grace (60–90 s) may exist, but the player stays in the world, standing still and killable, not removed from it.
- Fallback after death: basic clothing and a knife, with recovery supplies at the hub vendors. Never a free gun, armour, backpack or ammo bundle.

## Matchmaking

Simple, and only as much as the population can support:

- Party up to 4.
- Prefer same-size parties, relax when the queue exceeds ~90 seconds.
- Solo fills into mixed lobbies rather than waiting for a solo-only server.
- No skill rating in v1. Measure extraction rate per player instead; that is the input a rating would need later.

## Still unknown

1. Real sell values in `Config/Items` — needed before any loot number is real.
2. ~~Whether the map is one town block, a roadside commercial strip, or a checkpoint.~~ Answered in [[Idea - Raid Map]]: one highway exit, seven zones, 224 m, five extracts.
3. Server cost at 16, 20 and 24 players, measured with infected running.
4. Whether the raid place streams. A 640,000 sq stud map with interior detail is a streaming decision, not just a build.

## Implementation order

1. Greybox the map at 800 × 800. Measure traversal and sightlines before art.
2. Deployment, profile-lock hand-off, server lifecycle.
3. Containers, loot tables, InventoryTx integration, restock timer.
4. Extraction points and validation.
5. Death, drops, disconnect-as-death.
6. Standard infected with noise events.
7. Performance gates at 16 / 20 / 24.
8. Everything else.

The weapon prototype is a separate track and does not block any of this — see [[Weapon - M1911A1 Build Spec]].
