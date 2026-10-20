---
tags: [idea, raids, servers]
status: shaping
updated: 2026-10-20
---

# Idea - Raid Frame

**The pitch:** the server is the world; the raid is your deployment. No countdown. Players join and leave while the map stays alive. Loot refills. Extraction is the only way to bank.

Supersedes the batch model in the 2026-10-18 version of this note. Reconciled against [[Master Project Context]], [[Idea - Detroit Map]], [[Idea - Death and Loss]], [[Reference - The Walking Dead Online]].

## 1. Why the batch died

The old model ran a 50-minute server and banned late joiners. Both were workarounds for one problem: shared containers were single-use, so a long-lived server stripped itself bare.

Loot respawns remove the problem. A container refills on a timer, so a server can run for hours, a late joiner finds a living map, and no one needs a countdown to force a reset.

[[Reference - The Walking Dead Online]] proves the shape on Roblox: a normal server, join and leave at will, saved character, no raid timer, loot and infected respawning. Its persistence is the character, not the world. No Dawn does the same: the hub profile persists, the raid server lives while it has players.

## 2. The frame

| | Value | Why |
|---|---|---|
| Maps | 1 | The loop is unproven |
| Map | Detroit, 1,600 × 3,200 studs (0.40 km²) | [[Idea - Detroit Map]] |
| Server life | while populated; no gameplay timer | Open-ended; closes when empty |
| Capacity | 24 launch, 32 ceiling | 60 players/km² at 24; see §5 |
| Average concurrency | 16–20 | Late join fills; peak 24 |
| Containers | ~500 nodes, respawning | See §4 |
| Infected | 4–5 per player, cap 100, 20–24 local | Scales with population |
| Extracts | 5, 3 active per server | Always open; no timer |
| Bosses | none | The elite keyholder is enough |

## 3. The server model

- A raid server is an instance of the Detroit map. It starts when the first player deploys and closes when the last player leaves. There is no batch, no 50-minute timer, no end-of-server extraction.
- **The player's deployment** starts at spawn and ends at an extract or death. A player can extract at any active extract at any time. That is the raid.
- **Late join is normal.** Matchmaking fills servers with space; a party joins together if the server can hold it; otherwise a new server starts.
- **Join friend** uses Roblox's native join; the party leader's server is preferred.
- The world state — loot timers, corpses, reanimated bodies — lives in server memory for the life of the server. When it closes, the world resets. The hub profile is the persistent layer.
- **No gameplay timer.** If the server runs for six hours, that is the players' doing. A soft recycle after 4–6 hours is an operations safeguard for memory, not a raid timer; it should never be player-facing.
- **Server shutdown.** If Roblox shuts the server down with players inside, the default is to treat them as extracted. It is rare and not player-triggerable; revisit if it becomes exploitable.

## 4. Loot respawns

The container is a tap, not a one-shot.

| Tier | Respawn after empty | Notes |
|---|---|---|
| Common | 10–15 min | Houses, cars, desks |
| Uncommon | 18–25 min | Workshop, clinic, pharmacy |
| Rare | 30–40 min | Armory cage, manager's office |
| Elite | 45–60 min | Behind the key / elite keyholder |

- Refill only when unobserved: no player within ~40 studs, no line of sight. A due container is checked against nearby players only; one scheduler loop, not 500 threads.
- Global caps: the server tracks active rare and elite items; if the cap is reached, the container waits.
- **Late join no longer arrives at a stripped map.** The map is always refilling; the question is where, not whether.
- **Per-player anti-farm** is not in v1. If telemetry shows one player looping the same rare route, add a 20–30 minute per-player cooldown on rare containers. Do not build it first.
- The map's value is a throughput, not a fixed pool. Placeholder: 500 nodes, ~60% active, 1–2 items each. Values in `Config/Items` are still the source of truth.
- **No world text.** A refilled container reads by its closed lid and prompt, not a label.

## 5. Capacity and density

The old 12 vs 16 argument was for a batch and an 800 × 800 map. The Detroit map is 0.40 km². At 24 players that is 60/km²; at 16–20 average, 40–50/km². The infected are the baseline population, not the players.

- **Launch cap 24.** 24 players on 0.40 km², concentrated by loot and noise, gives PvP contact roughly every 7–13 minutes per player. That is an extraction shooter, not a deathmatch.
- **Ceiling 32.** Only after measuring server frame time, client fps and replication at 24 with the infected cap running. 32 is the number to test, not the number to promise.
- 50, the TWD Online cap, is for a much larger world. 50 on 0.40 km² is 125/km² and a meat grinder. If He wants 50, the map has to grow; the district expansion is 0.7 km², still not enough.
- **Late join changes the population, not the map.** A server at 8 players is quiet and lucrative; a server at 24 is loud. Both are valid. Do not shrink the map for the low end.
- Minimum population: none. A low-pop server closes when empty, not when it hits a threshold. A solo player on a quiet server is a legitimate farm; the risk is that someone else joins.

## 6. Infected with a moving population

The fixed 80/20 was sized for 16 players in a 50-minute batch. With open servers, the count should follow the players.

- **4–5 active infected per player**, cap **100**; **20–24 local** within 200 studs of any player, server-wide.
- At 8 players: ~40 active. At 24: ~100. At 32: cap 100–120, test.
- Continuous respawn: spawn out of sight (>60 studs, behind geometry, not in a player's view), despawn >150 studs from every player, with a global cap.
- Player reanimated bodies reserve slots; they are a death event, not a spawn.
- Noise still drives investigation. Gunfire pulls a radius; suppressors shrink it. The market centre is a permanent hotspot; the prairie and the Cut hold more than the roofs.
- This answers the 80/20 vs 120/30 question: neither fixed number. Scale with population, cap for cost.

## 7. Extracts

- Five extracts, **three active per server**, chosen at server start. The active set is fixed for the server's life and marked in the HUD; no world text.
- Always open. No global timer, no "last five minutes."
- Hold 8–10 seconds to leave; taking damage resets the hold, leaving the zone resets it. A camper can deny the extract; use another or clear them.
- Extraction is the only bank. Walking into an extract with loot is the end of the deployment.
- Cost asymmetry stays: the Cut and the roof extract are fast and exposed; the market floor and basement are slow and covered. See [[Idea - Detroit Map]].

## 8. Enter and exit at will — the exact rule

- **Enter:** deploy from the hub to any server with space. Spawn at one of 24 spawns, chosen away from players (>120 studs) and from recent combat. Late join is the same path.
- **Exit:** extract at an active extract. Loot banks atomically; the deployment ends.
- **Leave:** "Return to hub" without extracting is an abandoned deployment. It counts as death. The body stays, the gear stays on it, and it reanimates if the brain is intact. The UI warns once, plainly.
- **Disconnect:** same as leave, with a short reconnect grace. The body stays in the world; the player is killable. Rejoining the same server is allowed if it has space and the body has not despawned.
- **What "at will" does not mean:** logging off with loot. If leaving banked the gear, the hub vault would be an infinite farm and extraction would be decoration. TWD Online can do that because it has no hub bank; No Dawn cannot.

## 9. The economy under infinite time

No timer means no natural end to a session. The sinks have to carry the load.

- **Carry cap** (backpack slots) is the first limit. The player must extract to convert loot into progress.
- **Death** removes the kit and the haul. That is the biggest sink.
- **Contested rare loot**: armory, clinic, keyholder. The good stuff is where the fights are.
- **Vendor stock**: firearms and rare gear are limited per restock; the hub is not a supermarket. The rotating stock already in the plan does this.
- **No hunger/thirst in v1.** It is the obvious missing sink for long sessions. If the economy trivializes, add a light hunger/thirst system before adding a timer; a timer is the thing we removed.
- Watch the faucet: Dollars per hour, extracts per hour, death rate. If the average extract pays 1.5–2× the kit, the loop is healthy; if it pays 5×, the loot tables are too rich.

## 10. Death and recovery

The full rules are in [[Idea - Death and Loss]]. The open-server changes:

- The victim returns to the hub. The body stays in the server and reanimates if the brain is intact.
- **Late join is normal, so the victim can return to the same server** if it has space and the body has not despawned. The hub stores the last raid job id and offers "return to the district" for the body timer.
- The party marker shows the death point. The victim's own map shows it too.
- If the server is full, the victim cannot rejoin, but the squad is already inside and can still recover the body. If the body has despawned, it is gone.
- Re-entry is a knife run unless the vault holds a spare kit. The body is the prize.

## 11. Matchmaking

- Party up to 4.
- Fill existing servers with space before starting a new one; a party joins together if the server can hold all of it.
- Prefer servers with comparable party sizes; relax after ~90 seconds.
- No skill rating in v1. The equal-power rule is the protection; measure extraction rate per player for later.
- Join friend is native; no server browser in v1.

## 12. World events (v1.1)

TWD Online's helicopter crash is the model: a periodic, announced, contested loot site that pulls players and infected to one place.

- **Supply drop** every 25–40 minutes: a flare and a distant engine, a high-tier container, a horde that follows the noise. One event type, not five.
- It gives a long-lived server a heartbeat and a reason for late joiners to move.
- Not in v1. Ship the core loop first.

## 13. Still unknown

1. Server cost at 16, 24 and 32 players with the infected cap running.
2. Loot respawn values: timers, active caps, value per hour. `Config/Items` first.
3. Server shutdown policy: extracted or secure-container-only.
4. Hunger/thirst: add or not, after the first economy read.
5. Extract hold: 8 or 10 seconds; damage reset or not.
6. Soft recycle after 4–6 hours: needed or not.
7. Does the active extract set rotate during a long server, or stay fixed? Recommend fixed for the server's life; the HUD marks it.

## 14. Implementation order

1. Server lifecycle: start on first join, close on empty, no timer; late join; spawn selection.
2. Loot respawn scheduler; container state; global caps.
3. Extracts: always open, hold timer, atomic bank.
4. Death, body, reanimation, rejoin-by-job-id, party marker.
5. Infected scaling with population.
6. Matchmaking and party fill.
7. Performance gates at 16 / 24 / 32.
8. World event, second map, everything else.

The weapon prototype is a separate track and does not block any of this — see [[Weapon - M1911A1 Build Spec]].
