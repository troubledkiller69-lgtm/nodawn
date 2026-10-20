---
tags: [idea, combat]
status: shaping
updated: 2026-10-18
---

# Idea - Combat Frame

The plan to approve before anyone models a pistol. Reconciled against [[Master Project Context]] §6, [[Addendum - Infected and Firearms]] B, [[Weapon - M1911A1 Build Spec]], and what Phase 1 actually built.

**The pitch:** server-owned state, client-owned feel. Hitscan, hit zones, noise as a first-class event. No ballistics simulation, no lag rewind, no second inventory.

## 1. The decision that shapes everything

Roblox gives us a client that renders at 60 fps and a server on a different machine, 40–90 ms away. Every combat design is a choice about which one is allowed to be wrong.

**Authority:** the server owns ammo, fire rate, reload validity, hit registration and damage. The client owns animation, muzzle flash, tracers, recoil and sound. The client predicts its own ammo count so the HUD does not stutter; the server's next state packet corrects it.

**Hit registration:** the client sends a shot with origin, direction, sequence number and client timestamp. The server validates that the shooter is alive, equipped with that weapon, has ammo, is not mid-reload, and has not fired faster than the weapon allows. Then **the server raycasts**.

No client-side hit claims. A cheater can still aim perfectly — that is unfixable — but cannot shoot through walls, invent ammo, or hit a target the server cannot see.

## 2. The lag compensation trap

Tarkov-style rewind (restoring target positions to the shooter's past) is the "correct" answer and the wrong first move on Roblox. It needs position history for every character, snapshot interpolation, and it multiplies the cases where hitboxes disagree with what the player saw.

**v1:** server raycast against current positions, with a small tolerance — a hit counts if the ray passes within ~0.6 studs of the hitbox. At 0.28 m/stud that is about 17 cm of forgiveness, roughly one ping's worth of movement at sprint speed. This is the "favor the shooter" budget.

Measure the miss rate players complain about. If it is high, add rewind later as a bounded 100 ms buffer, not as the foundation.

## 3. The weapon state machine is the hard part

Not the model, not the damage numbers. The state machine, because every exploit lives in a transition.

States, per weapon instance:

```
idle → aiming → firing → cycling → idle
idle → reloading → idle
firing → empty → locked → reloading → idle
any → equipping / unequipping / lowered / dead
```

Rules that have to be enforced server-side:

- A shot requires `idle | aiming | firing` and a live round.
- The last shot plays its full cycle, then locks the slide. No auto-return.
- Reload commits the magazine at the insertion moment, not at the start. Cancel before insertion and nothing changes; cancel after and the magazine is spent.
- Re-equipping or dying during a reload must not leave a stuck state or duplicate ammo.
- Every request carries a sequence number; the server rejects out-of-order and repeated ones. `RequestGuard` already does rate limiting and `rid` dedup — extend it rather than building a second one.

## 4. Ammo model

Per weapon instance: `mag`, `chamber`, `reserve`.

- The M1911 is 7 + 1. The chambered round is tracked separately because the spec requires tactical reloads to preserve it.
- Firing consumes `chamber`, then cycles the next round from `mag` into `chamber`.
- A tactical reload keeps the chambered round and swaps the magazine. An empty reload leaves the chamber empty and needs the slide release.
- Reserve ammo lives in the inventory as an ammo item, not as a number on the weapon. Reloading moves rounds from inventory to the magazine as one transaction through `InventoryTx`.

That last point matters: ammo is an item, so it can be looted, sold and lost on death without inventing a parallel system.

## 5. Damage model

HP plus hit zones. Not a wound simulation.

| Zone | Multiplier | Notes |
|---|---|---|
| Head | 3.0 | Lethal for .45 against an unarmoured player |
| Torso | 1.0 | Causes bleeding |
| Limbs | 0.7 | No dismemberment in v1 |

Player HP 100. Standard infected 80.

| Round | Damage | Torso shots to kill a player | Head |
|---|---|---|---|
| .45 ACP | 50 | 2 | 1 |
| 9×19 later | 35 | 3 | 1 |
| 5.56 later | 55 | 2 | 1 |

Bleeding: a torso hit applies 1 HP/s until bandaged, capped so it cannot kill alone from full health. It exists to make fights feel expensive, not to add a second health bar.

Armour is out of v1. When it arrives it is a penetration check against a class rating, not a flat damage reduction.

## 6. Noise is a system, not a side effect

This is the piece that makes the game No Dawn instead of a shooting range. It also has to exist before infected AI, or the AI gets wired directly into the weapon code and never comes apart.

```
NoiseService.emit(position, radius, loudness, sourceType, sourcePlayer?)
```

| Source | Radius (studs) | Notes |
|---|---|---|
| .45 unsuppressed | 220 | About a quarter of the 800 × 800 map |
| .45 suppressed | 60 | Reduced, not silent |
| Melee | 15 | Quiet is the point |
| Sprinting | 25 | Movement leaks information |
| Infected vocalisation | 40 | Pulls other infected |

The server emits on every shot. Infected AI subscribes later. Audio mixing and player-facing "where was that" cues read the same event, so gunfire becomes information for everyone, not just the AI.

## 7. Recoil, spread, sights

The spec says shots must follow the actual aiming solution, not the centre of the screen. In a real pistol those are different axes and they converge at one distance. Every FPS cheats this.

**Decision:** raycast from the camera axis, and build the sights so that at the chosen zero distance they sit on that axis. Raycasting from the muzzle along the bore looks correct in a screenshot and produces parallax misses at close range, which players read as broken hit detection.

- ADS: 0.25 s transition, FOV narrowing, sway from Composure, near-zero spread.
- Hip fire: a 2–3° cone. Server rolls the spread; the client draws the tracer where the server said it went.
- Recoil is camera impulse plus recovery, applied client-side for feel, with the server validating only fire rate. Recoil that affects the actual ray must be server-known, so the client reports its aim direction per shot and the server trusts direction but not origin.

## 8. Attachments

Data-driven compatibility, not a menu of everything.

- Each weapon declares mount interfaces: `muzzle = "threaded_45"`, `optic = none`, `light = none`.
- Each attachment declares what it mounts to and what it changes: noise multiplier, recoil, weight, ADS speed.
- A classic M1911A1 has no rail and an unthreaded barrel. It gets iron sights and, with the correct barrel configuration, a suppressor. No light, no optic, unless the model represents a compatible variant.
- Attachments are inventory items with their own `uid`, so they can be looted and lost.

## 9. Melee

Shares the equipment slot, not the firearm code path.

- Short spherecast in front of the camera, 3.5 studs reach, 120° arc.
- 8 stamina per swing, 0.6 s cooldown, noise radius 15.
- Damage: 30 blunt. Two swings for a standard infected, three for a player.
- The hub kit already grants a pipe wrench, so melee is the first thing a dead player has.

## 10. Stamina and attributes

Combat reads the six attributes; it does not re-implement them.

| System | Attribute | Effect |
|---|---|---|
| Sprint | Endurance | Drain rate and recovery |
| Aim steadiness | Composure | Sway amplitude, flinch on hit |
| Melee | Strength | Damage and swing speed |
| Reload speed | Agility | Small multiplier, capped |
| Carry weight | Strength | Already enforced by Phase 1 |

Keep every effect modest and readable. A low stat should never make a mechanic unusable.

## 11. Integration with what already exists

| Need | Existing thing | Change |
|---|---|---|
| Damage | `DamageService.apply` | Extend with weapon, hit zone, distance |
| Transfers | `InventoryTx` | Reuse for ammo and attachments |
| Remotes | `RemoteRouter`, `RequestGuard` | Add `Weapon.Fire/Reload/Equip/Attachment` |
| Items | `Config/Items` | Weapon, ammo, attachment definitions |
| Persistence | `ProfileRepository`, schema v2 | **Per-instance weapon state** — verify the inventory can hold it before building |
| Attributes | `Config/Attributes` | Read-only consumer |
| Noise | nothing | New `NoiseService`, server-side only |

The one unknown that can bite: Phase 1 items store `uid`, position and quantity. A weapon needs `mag`, `chamber`, `condition` and attachments per instance. If the inventory schema cannot carry that, it is a migration to v3, and that lands before any weapon code, not after.

## 12. v1 scope

Build exactly this, then stop:

- One weapon: M1911A1, iron sights, one suppressor configuration.
- One ammo type: .45 ACP, as an inventory item.
- One melee: pipe wrench.
- Hit zones, bleeding, no armour.
- Camera-axis raycast, server-authoritative.
- Noise events, no infected consumer yet.
- A dev-only test range — targets at 10, 25 and 50 studs — so hit registration and sight zeroing can be tested without a raid.

No malfunctions, no attachments beyond the suppressor, no second firearm, no armour, no ballistics, no rewind.

## 13. Test plan

Unit:
- Ammo arithmetic across tactical reload, empty reload, cancel and death mid-reload.
- Fire rate rejection for every interval below the weapon's cycle.
- Attachment compatibility: a rail-less frame refuses a light.
- Damage by zone for each weapon.

Studio, two clients:
- A shot fired by one client appears on the other with correct state.
- Reload state replicates and cannot be interrupted into a duplication.
- Weapon state after respawn is clean.

Exploit pass:
- Fire while empty, fire during reload, reload twice from one input, negative ammo, foreign weapon uid, remote spam, rapid sequence replay.

## 14. Decisions to approve

1. **Camera-axis raycast** rather than bore-axis. Recommended: yes. It is the difference between sights that feel right and hit detection that feels broken.
2. **No lag rewind in v1.** Recommended: yes, with a 0.6-stud tolerance and measurement before adding complexity.
3. **Ammo as inventory items**, not a counter on the weapon. Recommended: yes. It makes ammo lootable and losable for free.
4. **HP plus hit zones**, not per-limb health. Recommended: yes for v1; per-limb health is a Project Zomboid import that costs a lot and reads poorly at Roblox fidelity.
5. **Melee on the same framework**, sharing cooldown and stamina but not the firearm state machine. Recommended: yes.
6. **The inventory schema question** — if weapons cannot carry per-instance state today, do we migrate the schema first? Recommended: yes, migration before weapon code.
