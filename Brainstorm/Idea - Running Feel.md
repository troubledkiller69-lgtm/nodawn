---
tags: [idea, movement]
status: shaping
updated: 2026-10-18
---

# Idea - Running Feel

The detail layer under [[Idea - Movement Frame]]. Speeds, acceleration, stride, bob, stamina, surface, breathing. Realism here is not animation quality — it is **weight and consequence**.

**The pitch:** a survivor who takes half a second to get moving, can sprint for fourteen seconds, breathes hard enough to ruin their aim afterwards, and sounds different on gravel than on grass.

## What "realistic" has to mean

Not simulation. At Roblox fidelity, full realism reads as sluggish and broken. The 80/20 is:

1. **Acceleration** — you do not reach top speed instantly.
2. **Cadence** — footsteps follow speed, not a looping animation timer.
3. **Breath** — stamina is audible and visible in the weapon.
4. **Surface** — the ground changes how fast and how loud you are.
5. **Weight** — what you carry changes all of the above.

Everything else is polish.

## Speeds

Roblox's default is 16 studs/s — a 4.5 m/s jog. That is already faster than a real walk and it reads arcade. Drop it.

| State | Speed | Real equivalent | Notes |
|---|---|---|---|
| Walk (Alt held) | 6 | 1.7 m/s | Quiet, precise, rarely used |
| Jog (default) | 9 | 2.5 m/s | The speed players actually live at |
| Sprint | 20 | 5.6 m/s | Empty load only |
| Sprint, loaded | 16 | 4.5 m/s | 50–75% carry weight |
| Sprint, heavy | 13 | 3.6 m/s | 75%+ |
| Overweight | no sprint | — | Walk/jog only |
| Crouch | 4 | 1.1 m/s | |
| Prone | 2 | 0.56 m/s | |

At 0.28 m/stud a 20 stud/s sprint crosses the 800-stud raid map in 40 seconds. That is fast enough to matter and slow enough that crossing open ground is a decision.

## Acceleration and inertia

Speed changes are lerped, not set.

| Transition | Time to 95% |
|---|---|
| Stop → jog | 0.25 s |
| Jog → sprint | 0.60 s |
| Sprint → stop | 0.20 s |
| Any → crouch | 0.20 s |
| Any → prone | 0.50 s |

Why this matters more than it sounds: without the sprint ramp, players juke instantly and fights become twitchy. With it, committing to a sprint is a commitment. That is the whole realism argument in one number.

**Turning** is rate-limited as speed rises: 220°/s at jog, 150°/s at sprint, 90°/s when heavy. A sprinting player carves a wide arc. That single rule does more for the feel of weight than any animation.

## Stride and camera

Footstep cadence is derived from speed, not from a fixed loop.

| State | Stride | Steps/s |
|---|---|---|
| Jog | 3.5 studs | 2.6 |
| Sprint | 6.0 studs | 3.3 |

- **Bob** is vertical 0.10 studs at jog, 0.22 at sprint, with a small lateral figure-eight. Frequency tracks cadence exactly, so the head lands when the foot lands.
- **Camera roll** leans into strafing ±2°, and into turns ±3° at sprint.
- **Landing dip** of 0.3 studs, recovering over 0.15 s.
- **ADS suppresses 70% of bob.** Prone has none.
- **FOV** 70 default, +6 at sprint, −8 at ADS.

Every one of those numbers is a starting point, not a law. They get tuned against capture, not against a spreadsheet.

## Stamina

Pool of 100. Server-owned, client-predicted, same pattern as ammo.

| Action | Cost |
|---|---|
| Sprint | 7/s (14 s of sprint) |
| Jump | 8 |
| Slide | 15 |
| Mantle | 10 |
| Melee swing | 8 |
| Grapple | 20, plus 5/s reeling |

- Regen 5/s after 2.5 s without spending; 2/s while walking.
- Below 25: cannot start a sprint. Existing sprint continues.
- Below 35: audible breathing, sway ×1.6.
- Below 15: sway ×2.2, heartbeat, no ADS steadiness.
- Endurance changes drain and regen. Carry weight above 50% adds up to 3/s.

Fourteen seconds does not sound like much until you are crossing a street with a horde behind you.

## Surfaces

A raycast under the feet tags the surface. Speed modifier on the left, noise radius on the right.

| Surface | Speed | Noise (sprint) |
|---|---|---|
| Concrete | 100% | 22 |
| Wood | 95% | 28 |
| Grass | 95% | 10 |
| Gravel | 92% | 35 |
| Metal | 100% | 40 |
| Mud | 72% | 18 |
| Water | 60% | 30 |

Noise scales down with stance: crouch is half, prone is a fifth. The same street is a different risk depending on what it is made of. This is the cheapest environmental storytelling in the game and it costs one raycast.

**Slopes:** above 10° uphill, −15% speed and +20% drain. Downhill, +8% speed and −10% drain, with a risk of losing footing on wet terrain.

## Jumping and landing

- Jump height 4 studs (~1.1 m), 8 stamina, 0.4 s cooldown.
- Almost no air control. Steering mid-air should feel like a mistake, not a technique.
- Landing noise 20 studs, more from height.
- Recovery 0.15 s from a low jump, 0.45 s from 10+ studs.
- Fall damage begins at 12 studs ([[Idea - Movement Frame]]).

Jump spam is the single most arcade thing a Roblox game does. The cooldown and the stamina cost exist to kill it.

## Weapon and movement

Movement and shooting pull against each other.

- Sprinting lowers the weapon. No firing, no ADS. 0.25 s to raise it.
- Firing while sprinting cancels the sprint into a planted stop, 0.3 s before the first accurate shot.
- Low stamina shows up as sway, not as a locked trigger.
- Reloading while moving is allowed; reloading while sprinting is not.

## Breathing

The most underrated realism cue. After a sprint:

- 3 seconds of audible breathing, sway ×1.6, decaying.
- The breathing loop scales with stamina, so it doubles as a HUD without a bar.
- Silence is then a reward: a player who walked into position aims better than one who ran.

## Audio

- Footsteps at cadence, surface-specific, with a gear rustle layer at sprint.
- No footsteps while prone.
- Distant player footsteps should be audible enough to be information. That is a gameplay system, not a sound effect.
- Breathing and heartbeat at low stamina.

## What this costs

- A `MovementController` on the client owning speed, acceleration, stance and camera offsets.
- A `StaminaService` on the server owning the pool, with client prediction.
- A surface tag pass over the raid map: every floor material mapped to one of seven surfaces.
- Animation: jog, sprint with weapon lowered, sprint-to-stop, crouch walk, prone crawl, slide, vault, mantle, jump, land, breath idle — each in first-person arms and third-person body.
- Tuning sessions against capture. Numbers in this note are guesses until someone runs them.

## Accessibility

Non-negotiable, and cheap: **a bob intensity slider from 0 to 100%**, with zero gameplay penalty for turning it off. Motion sickness is common and the Roblox audience is young. A game that makes people nauseous loses them permanently.

Same for FOV and camera roll. Realism is a default, not a requirement.

## Open questions

1. Is there a walk key at all, or does everyone jog by default and walk only while ADS?
2. Does stamina regenerate in the hub, or is the hub a place to recover? (It should regenerate.)
3. How much does the stamina bar show — number, bar, or only breathing?
4. Does encumbrance affect acceleration as well as top speed? Recommended: yes, it is the cheapest way to make weight feel real.
