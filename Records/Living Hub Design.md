# Living Hub — design (2026-10-17)

> **Partly obsolete.** Step 1 (soundscape) is done; steps 2–5 are parked. Step 5 plans a day/night cycle, which contradicts the permanent-night ruling in [[Design Rules]] and is dead. See [[Open Items]].

Goal (user): "I want it to feel alive … GTA 5 feels alive." The hub should read as an inhabited
settlement: things make noise, move, react to you, and change over time.

## Principles
1. **Cosmetic only.** Nothing here touches profiles, remotes, damage or gameplay rules. Every
   system can fail or be removed without affecting the game.
2. **Client-side unless it must be shared.** Sound, particles, wildlife, light flicker and NPC head
   tracking run per client: no replication cost, per-player variety, and an NPC looks at *you*.
   The server owns only what every player must agree on: NPC positions (routines), the clock
   (day/night) and world events.
3. **Found, not placed.** `DevTools.HubBuilder` regenerates `Workspace.Hub`, so hand-placed
   emitters would be lost. Systems discover their props by name at runtime (FireBarrel,
   Generator, HVAC, CeilingLight, Floodlight, towers, NPC models). New props of a known name
   automatically get their ambience.
4. **One config.** `Shared.Config.Ambience` holds every sound id, volume, range, schedule and
   VFX setting, so tuning never touches code. All audio is Pro Sound Effects (Roblox-licensed).
5. **Budgets.** At most ~12 positional loops audible at once (RollOff ranges keep the rest
   silent), particle rates kept low, wildlife ≤ 12 birds, all updates ≤ 4 Hz except visual tweens.
   Target 60 fps on a mid PC and acceptable mobile.

## Steps (each verified in a playtest before the next)
1. **Soundscape** (client `AmbienceController`)
   - Beds (2D, crossfaded): outdoor wind; birds by day / crickets by night; indoor room tone.
     Indoor = solid roof within 40 studs above the camera (raycast, 4 Hz).
   - Positional loops on props: fire barrels (crackle), generator + HVAC (hum), ceiling tubes
     (buzz, a few only), guard booth (CB radio, intermittent).
   - One-shots on timers: ravens from the walls/towers, workshop hammering near the mechanic,
     rare distant artillery beyond the walls.
   - Indoor reverb switch (SoundService.AmbientReverb).
2. **NPC life** (server routines + client reactions)
   - Guards patrol waypoint loops; camp survivors drift between the fire, tents and crates;
     workers stay at their post with periodic idle variation. Walk animation while moving,
     server network ownership, routes avoid doors/counters/terminals.
   - Client: heads turn toward the local player within ~12 studs; vendors and staff greet you
     once when you approach (bubble line, cooldown).
3. **Motion and VFX** (client): smoke + embers on fire barrels, generator exhaust, a couple of
   flickering ceiling tubes, slow-turning searchlights on the towers at night.
4. **Wildlife** (client): crows perch on walls and razor wire, scatter with a caw when you get
   close, circle, and land somewhere else.
5. **Day/night + events** (server clock, client lighting): slow cycle with tuned lighting
   keyframes, exterior lights on at dusk, beds swap birds↔crickets; occasional world events
   (radio broadcast, siren drill, distant battle) announced through sound + HUD toast.

## Verify (every step)
Playtest captures, console clean of our scripts, a perf read (frame time, sound/particle counts),
and a walk past every door, counter and terminal to confirm nothing blocks them.
