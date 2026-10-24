---
tags: [idea, movement]
status: shaping
updated: 2026-10-20
---

# Idea - Movement Frame

First-person extraction shooter. Movement is not a traversal feature — it is how a player decides how much noise to make and how exposed to be. Reconciled against [[Idea - Combat Frame]] and [[Idea - Raid Frame]].

**The pitch:** walk, sprint, crouch, slide, mantle. Lean and prone later.

## What movement is actually for

In a shooter with no movement tech, position is binary: behind cover or not. Movement systems exist to make **exposure a dial**:

- Crouch: slower, quieter, lower profile.
- Prone: lowest profile, slowest, hardest to leave.
- Lean: see without exposing the whole body.
- Slide: cross a gap fast, loudly.
- Mantle: take a route that isn't a door.

Every one of those should cost something — speed, noise, stamina, or the weapon being stowed. If it costs nothing, everyone always does it and the dial disappears.

## The constraint that shapes all of it

Roblox characters are upright capsules driven by a `Humanoid`. They do not lie down, they do not lean, and the client owns their movement.

Three consequences:

1. **Prone and lean cannot change the collision capsule.** The body will visually lie down while the collider stays standing. That is only acceptable if damage uses **custom hitboxes**, not the Humanoid parts.
2. **Movement is client-authoritative by default.** The server can detect a speed hacker but cannot prevent one cheaply. Perfect prevention means writing a custom character controller, which is a project on its own.
3. **Every stance is an animation set on the R15 rig**, and first-person needs arms while third-person needs the body. That is double the animation work, and it is the real cost of this system.

**Decision:** keep the Humanoid, wrap it in a `MovementController` that owns stances and enforces caps, and use custom hitboxes for damage. Do not rewrite the character controller for v1.

## The hitbox problem, stated once

This is the thing that must be decided before lean or prone ships, and it is the same system combat already needs:

- The camera moves 0.7 studs sideways when leaning. If the hitbox does not, players will be shot where their body is not, and will hit air where the enemy's head appears.
- Prone drops the camera to ~1 stud. The standing capsule is still 5 studs tall.

So: the damage model reads the body's real pose and never the capsule.

**Correction, 2026-10-18.** The first version of this section proposed synthetic volumes rebuilt per stance. That is wrong, and it would have cost weeks. The R15 parts are driven by Motor6D joints, the animation replicates, and the server solves the same pose — so **the parts already are the hitbox**, and prone, crouch, lean and slide move them for free. Resolved in [[Idea - Hitbox Model]]: zone map, spherecast, lean as an animation with a clearance check.

What survives from the paragraph above: the Roblox collider is not the damage model, and never becomes it. That is what keeps lean and prone consistent, and it is what makes headshots honest.

## Stances

| Stance | Speed (studs/s) | Camera height | Noise radius | Notes |
|---|---|---|---|---|
| Stand | 9 walk / 20 sprint | 5.0 | 12 walk / 25 sprint | Default |
| Crouch | 5 | 3.2 | 6 | Can ADS and shoot normally |
| Prone | 2.5 | 1.0 | 2 | Cannot sprint; heavy sway if moving; slow to leave |

- Crouch transition 0.2 s, prone 0.5 s down, 0.8 s up.
- Prone is the ambush stance, not the travel stance. If it were fast, everyone would crawl everywhere and the map would look empty.
- Standing up is loud (15 studs). That is the cost of hiding.

Roblox default walk is 16 studs/s, which is a 4.5 m/s jog. These numbers are slower on purpose: at 0.28 m/stud, 9 studs/s is a brisk walk and 20 is a real sprint.

## Slide

- Trigger: sprint + crouch. Requires 15 stamina.
- 0.9 s: an initial 1.4× sprint burst decaying to crouch speed.
- Camera drops to 2.2 studs, 8° roll, weapon lowered.
- **Can hip-fire with heavy spread. Cannot ADS.** That is the skill expression.
- Noise 30 studs. Sliding is not stealth.
- Downhill extends it, uphill cuts it short. Uphill slides should feel bad.
- Ends into crouch if crouch is held, otherwise stand. 0.6 s cooldown.

The point of a slide is to cross open ground under fire. It should be the loudest, fastest, most committed thing a player can do on foot.

## Lean

- Q/E, 18° camera roll. The lean itself is animated: the hips shift and the spine bends, so the head travels about 0.7 studs and the camera rides it. [[Idea - Hitbox Model]] §6.
- Standing or crouching only. Not while sprinting, not prone.
- Movement drops to a 4 studs/s shuffle while leaning.
- Can shoot and ADS while leaning, with a small sway penalty.
- Upper-body hitboxes shift with the camera, because `HitboxService` says so.

Lean is the cheapest system in this list and the highest-value one for a tactical shooter. It is also the first thing that proves the hitbox model works.

## Mantle and vault

- **Vault:** obstacles 3–4.5 studs (0.8–1.3 m). Fast, ~0.4 s, noise 15.
- **Mantle:** ledges up to 4.5 studs. Slower, ~0.7 s, leaves the weapon lowered briefly.
- Server validates the destination: raycast to the ledge, check the space above it is clear, reject if not.
- Costs 10 stamina.

Without this, fences and waist-high walls become invisible walls, and the map reads worse than it is.

## Falling

Roblox has no fall damage by default, which makes verticality free. Falling has to cost.

- Safe fall: 12 studs (~3.4 m).
- Beyond that: 4 damage per extra stud, capped at 80.
- Landing noise: 20 studs, more from height.

This is what stops "climb up, jump down, repeat" from being a free rotation.

## Stamina and weight

Movement spends the attributes Phase 1 already defined.

| Action | Stamina | Modified by |
|---|---|---|
| Sprint | 10/s | Endurance, carried weight |
| Jump | 8 | Strength, weight |
| Slide | 15 | — |
| Mantle | 10 | Strength |
| Melee swing | 8 | Strength |

Regen: 6/s after a 2 s delay. Encumbrance lowers the regen rate and the sprint speed before it ever stops you moving — never make a player unable to walk.

## Server validation

Movement stays client-authoritative, but the server validates the **state**, not the physics:

- Stance transitions must be legal and take the declared time.
- Speed caps per stance; sustained overspeed is flagged and corrected.
- Slide requires sprint state and stamina at the moment it starts.
- Every state change is a server event with a timestamp, so a cheater has to fake a coherent history, not one packet.

That is not perfect. It is the honest ceiling for a Roblox Humanoid, and it should be written down as a limitation rather than pretended away.

## Map consequences

These systems are a level design language, not a feature list.

- Rooftops need to be real places with loot and risk, reached by ladders, fire escapes and stairs.
- Corners need to be built for leaning — a clean 90° edge, not a pile of props.
- Prone needs low cover that actually blocks a standing silhouette.
- Sliding needs open lanes between cover, not corridors full of clutter.

## Staging

Do not build all of this at once. Each stage multiplies animation, validation and map work.

1. **v1:** walk, sprint, crouch, jump, vault, mantle. Slide last in this stage because it needs the most tuning.
2. **v2:** lean, prone, and with them `HitboxService`.
Lean is cheap and proves the hitbox model. Do it first.

## Open questions

1. Does prone need to be physically lower, or is a visual plus hitbox state enough? The capsule says it has to be the latter.
2. Does sprinting lower the weapon, or can a player sprint and hip-fire?
