---
tags:
  - reference
  - nodawn
  - combat
  - research
updated: 2026-10-28
---
# Reference - Grounded Survival Improvements

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]] · Related: [[Idea - Injury and Survival]], [[Idea - Death and Loss]], [[Design Rules]]

All entries below are proposals unless explicitly marked as an existing vault issue. Priority is suggested order of investigation, not authorization to implement.

## Research-backed principles

Valve's Left 4 Dead presentation separates creature behavior from population pacing. Its director creates changing intensity rather than constant combat; the slides explicitly distinguish pacing from difficulty. This is a useful precedent for quiet investigation followed by pressure. No Dawn has open servers and multiple squads, so a single four-player intensity meter would be an unsuitable direct copy. [Valve AI presentation, slides 77–91](https://steamcdn-a.akamaihd.net/apps/valve/2009/ai_systems_of_l4d_mike_booth.pdf)

DayZ's official 1.29 article describes recognizable distant weapon sounds, extended audibility and correction of indoor/outdoor inconsistencies. This supports treating audio as tactical information; its exact ranges are not calibration data for No Dawn. [DayZ update 1.29](https://dayz.com/article/game-update/dayz-update-129)

Project Zomboid's official B42 feature page describes adaptive music intensity responding to events. It is a design reference, not a current-version guarantee. Its May 2026 developer post also reports fixes for disappearing firearm magazines, duplicated items and weapon-condition desync: a practical reminder that state integrity remains central even in mature survival systems. [B42 feature overview](https://projectzomboid.com/blog/upcoming-features-b42/), [developer bug-fix report](https://projectzomboid.com/blog/news/2026/05/reinforcing-the-barricades/)

## Highest-value fixes to investigate first

| Candidate | Why it matters | Dependency/cost | Example acceptance |
|---|---|---|---|
| Camera/arms/ADS consistency | Every second of gunplay exposes it | Ownership audit and clear reference pose; medium | Same stance/pitch sequence stays connected and readable |
| Shot obstruction | Cover should protect the player regardless of framing | Explicit muzzle/aim contract; medium | Camera sees target but blocked muzzle cannot shoot through wall |
| Damage volumes matching stance | A visible prone/leaning body must be an honest target | Existing HitboxService work; medium–high | Debug hit volume agrees with visible exposed body |
| Reload and mag-check continuity | Mechanical state is part of the survival decision | Transactions and interruption policy; medium | Cancel/re-equip/death produces no duplication or visual contradiction |
| Native/remote pose consistency | Other players must see meaningful aiming/reloading cues | Replicated semantic state and two-client test; medium | Observer sees correct held weapon and action after late observation |
| Existing eyewear migration regression | Appearance and ownership should survive migration | Known 204/205 failure in vault; small/unknown | Old-profile migration preserves eyewear |

These are existing concerns or direct consequences of the current firearm contract. They are generally more valuable than adding gun variants before the foundation holds.

## Sound and sensory realism

**Proposal A — material/contact footsteps.** Tie a step to actual locomotion contact/stride, then choose surface, gait and load. Avoid one sound timer fighting the animation. A wet concrete corridor, grass and steel stair should be recognizable by ear. Cost: appropriate owned audio and contact/surface data. Gate: no double footsteps during a state transition.

**Proposal B — room acoustics.** Roblox's September 2026 release announcement says acoustic simulation is out of beta for the Advanced Audio API, with occlusion, diffraction and reverb. It requires global enablement and appropriate emitter/listener configuration. The announcement still says support for legacy Sound instances is being worked on. Rendering quality can reduce acoustic accuracy. [Roblox acoustic simulation release](https://devforum.roblox.com/t/full-release-acoustic-simulation-emit-audio-with-presence/4307121)

**No Dawn application proposal:** test one prison cell/corridor and one Detroit doorway first. Audit the current audio API, geometry and collision behavior. Keep infected hearing server-driven and independent of client acoustic quality. Cost: audio graph integration, owned source clips, geometry audit and device profiling. Gate: closing a door changes perceived sound plausibly while identical server noise inputs produce consistent AI reactions.

**Proposal C — explain infected knowledge.** Keep sight, heard location and remembered last-seen position distinct. Investigate a disturbance, search nearby plausible points, then lose confidence. Never substitute the player's current position after losing sight. The vault already reports last-seen searching; research should improve evidence and sensory consistency before adding another behavior layer.

## Physical presence and meaningful consequences

| Candidate | Concrete behavior | What could go wrong | Small initial experiment |
|---|---|---|---|
| Weapon lowering near obstacles | Long gun cannot occupy a wall freely | Camera can still exploit cover; rig may pop | One wall, one pump, explicit blocked-fire policy |
| Slope/step foot contact | Feet meet stairs and uneven ground | IK fights authored gait or floats hips | One slope and stair; blend only planted contacts |
| Injury feedback | Arm wound affects handling, leg injury changes locomotion cues | Excessive penalties compound into helplessness | Integrate one existing wound with capped feedback |
| Light discipline | Flashlight exposes location while enabling identification | AI becomes unfair or darkness unreadable | Fixed-night room with visible beam and measured detection |
| Inventory action continuity | Hands stay occupied during inspect/treatment/reload | Controls become tedious; interruption duplicates items | One interruptible action with clear ownership |
| Environmental storytelling | Prop placement explains occupation, repair and abandonment | Adds clutter/parts without information | One coherent medical or barricade scene under current budgets |

IK is an available animation tool, not proof that terrain contact will become cheap or stable automatically. Test it against the actual R15 geometry and authored clips.

## Pacing and additions worth exploring after the core fixes

**Local disturbance memory:** districts remember recent gunfire, breaking glass or a noisy fight for a bounded period. Nearby infected migrate/investigate rather than spawning in a player's face. Costs: spatial event aggregation, caps and multiple-squad fairness. First gate: two squads generating noise do not cause unbounded NPC growth.

**Small sensory distractions:** a thrown ordinary object attracts investigation at its impact position. Cost: shared noise contract and exploit/rate controls. Gate: observation can override sound, and the distraction does not reveal the thrower's current location.

**Hub routines:** medics work, guards change posture, vendors handle stock and distant activity follows a small schedule within permanent night. Favour motion and sound over extra props. Cost: authored contacts, animation budget and deterministic scheduling.

**Readable extraction conditions:** subtle lighting, sound and NPC/environment behavior communicate readiness and threat under the existing textless-world ruling. Cost: accessibility and player comprehension testing. Existing UI prompts remain governed by the game's UI rules.

## Defer until evidence justifies the cost

Do not bundle full ballistics/rewind, individual magazine persistence, random malfunctions, deep armour simulation, advanced apex AI, complex weather simulation or a larger weapon roster into camera repair. Each changes contracts, content and testing scope.

These may be worthwhile future systems. They need an explicit gameplay benefit, measurable acceptance and a narrow prototype. Permanent night, the no-grapple decision and no world signage remain in force.
