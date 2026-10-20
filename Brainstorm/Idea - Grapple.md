---
tags: [idea, movement]
status: shaping
updated: 2026-10-20
---

# Idea - The Grapple

**The pitch:** a rope you throw at a surface and swing from. Fixed length. No winch, no reel, no motor. You get out of it exactly the energy you brought.

This note replaces the "pull-to-point" version in [[Idea - Movement Frame]], which was Dying Light 1's hook, and then replaces my own "winch" addition, which was a solution to a problem the map should solve.

## 1. The correction

| | Dying Light 1 (Crane) | Dying Light 2 (Aiden) |
|---|---|---|
| What it does | Zips you **to** the point, no physics | Attaches a **rope**, and you swing |
| Momentum | Killed on attach | Preserved, redirected |
| Relationship to parkour | Replaces it | Extends it |
| Failure | Trivialises the city | Requires timing and a city built for it |

There is no motor in DL2's hook. It is a line, a hook, and a pendulum. That is the whole tool, and the honesty of it is what makes the movement read.

**Cut from the previous draft:** the winch, the auto-reel, the held reel, the 8 studs/s bite impulse. All four were invented to let a standing player climb, and all four turn a rope into an elevator. If the map needs an elevator, the map should have stairs.

## 2. What a fixed rope can and cannot do

This is the part that decides whether the tool is worth carrying.

A pendulum conserves energy. With a fixed rope, the highest point you can reach on the far side of a swing is:

```
rise = v² / 2g
```

At a 20 stud/s run and gravity 110, that is **1.8 studs**. At a 40 stud/s fall, it is **7.3 studs**.

So:

- **You cannot climb a building with it.** A rope with no reel cannot lift you. This is not a limitation to patch; it is the difference between a grappling hook and a jetpack.
- **You can cross a gap** by converting a run into a swing and releasing at the apex.
- **You can corner a wall** by throwing the line at it while running past, and letting the rope arc you around the anchor.
- **You can save a fall** and convert it into a rise, which is the best use of the tool and the one that will produce stories.
- **You can chain**, if the next anchor is within range at the moment you release.

Rooftop access therefore belongs to the map: ladders, fire escapes, stairs, a truck bonnet, a broken staircase. The grapple is for what happens *after* you are already up.

## 3. The three phases

**Catch.** No impulse. The line bites the surface and becomes a constraint. If you were moving, the rope converts your straight line into an arc in one frame — that snap is the entire feel of the tool, and it costs nothing to fake because it is real. If you were standing still, you hang there. That is a mistake the player makes once.

**Swing.** Fixed rope length, set at the distance you attached. WASD applies a small force along the tangent plane to steer and shape the arc — about 6 studs/s², a nudge rather than a motor. Gravity does the rest. Stamina drains, and the line releases on its own at zero.

**Release.** Keep the velocity. Add 3 studs/s upward. Air control returns after 0.15 s so you cannot cancel momentum instantly. Fall distance restarts at the release point, so a save is a save, and a botched one still kills you.

## 4. Simulation

The character is client-owned, so the swing runs on the client and the server validates the state. The model is a **position-based rope on top of engine gravity**: the engine integrates the pendulum, we enforce length and tangential input. No `RopeConstraint` — under a Humanoid it stretches, jitters, and cannot be validated.

```lua
-- client, every RenderStepped while attached
local v = hrp.AssemblyLinearVelocity
local r = hrp.Position - state.anchor
local dist = r.Magnitude
local dir = r.Unit

-- inextensible: pull inward only, never push outward
if dist > state.length then
    v += dir * math.clamp((state.length - dist) / dt * 0.35, -40, 0)
end

-- no outward velocity while taut
if dist >= state.length - 0.05 and v:Dot(dir) > 0 then
    v -= dir * v:Dot(dir)
end

-- steer along the tangent plane, never add radial energy
local wish = camera.CFrame:VectorToWorldSpace(Vector3.new(move.X, 0, move.Z))
local tangent = wish - dir * wish:Dot(dir)
if tangent.Magnitude > 0.01 then
    v += tangent.Unit * STEER * dt
end

v *= 1 - DAMP * dt
if v.Magnitude > 34 then v = v.Unit * 34 end
hrp.AssemblyLinearVelocity = v
```

There is deliberately no line that shortens `state.length`. That is the whole correction.

Three things this does not solve:

- **The Humanoid brakes the swing.** Enter `Enum.HumanoidStateType.Physics` on catch, return to `Freefall` on release. Otherwise the controller cancels your velocity every frame. *Verify `Physics` is enabled on the R15 rig.*
- **The character tumbles.** `AlignOrientation` on the HRP targeting horizontal velocity, high torque, responsiveness ~40.
- **Walls.** Predict 0.1 s ahead. Shallow hit: project velocity onto the wall and lose 30%. Head-on: cut the line and apply impact damage. Hitting a wall because you swung badly is correct, and it is the counterplay.

## 5. Attach rules

- Ray from the camera, 60 studs (80 upgraded). The anchor is the hit point offset 0.15 studs along the normal.
- **Any surface works**, including walls, ceilings, lamp posts, tree limbs and roof edges. That is the point of "throw it into a wall."
- The part must be anchored, or welded to an anchored assembly. Do not hook a swinging door.
- Line of sight from the head to the anchor, re-checked on the server.
- The anchor must be above the player's feet. Below that the rope is a leash, and the geometry already punishes it.
- Not while dead, ragdolled, grappling, or in the hub.
- One charge per attach, three per raid, refilled in the hub. A whiff costs the charge.

`CanGrapple = false` is the escape hatch for glass, thin signage and anything a map does not want hooked. Everything else is fair game, because DL2's hook works on the city, not on painted anchors.

## 6. Numbers

| Property | Value |
|---|---|
| Range | 60 studs base, 80 upgraded (17 / 22 m) |
| Rope length | fixed at attach distance, clamped 6–60 |
| Steer acceleration | 6 studs/s² |
| Damping | 0.35 /s |
| Speed cap | 34 studs/s (sprint is 20) |
| Max swing | 4 s, or until stamina hits zero |
| Attach cost | 20 stamina, 1 charge |
| Swing drain | 5 stamina/s |
| Cooldown after release | 1.2 s |
| Noise | 60 attach, 40 while swinging |
| Charges | 3 per raid |
| Wall impact | 0.5 damage per stud/s over 12, capped at 40 |
| Release assist | +3 studs/s up |

At 30 studs of rope and gravity 110, a full swing is about 3.3 s. At Roblox's default 196 it is about 2.4 s. Another argument for the gravity decision in [[Idea - Movement and Hitbox Reconciled]].

## 7. Networking

| Direction | Message | Contents |
|---|---|---|
| Client → server | `Grapple.Attach` | origin, direction, sequence |
| Server → all | `Grapple.State` | player, anchor position, length, phase, timestamp |
| Client → server | `Grapple.Release` | timestamp, end position |

- The server re-rays head-to-anchor, checks range, LOS, charges, stamina, cooldown and state. It does not simulate the swing.
- Every 0.5 s it samples: distance to anchor ≤ length + 2, speed ≤ 38, attached time ≤ 4.5 s. A violation ends the grapple and logs.
- The rope visual is a `Beam` from the left hand to a temporary attachment at the anchor. Beams are straight and a taut rope is straight. Never use a `RopeConstraint` for looks — it would apply physics.
- Third-person observers see a swing animation, so `HitboxService` reads a genuinely different silhouette. A swinging player is harder to hit in the legs and easier to hit in the arms.

## 8. Animation and audio

Three clips: **attach** (0.2 s, left arm up, torso twists), **swing loop** (legs trailing, body angled into the arc, 1 s), **release** (0.15 s).

Camera: FOV +6 on catch easing back over the swing, roll up to 6° into the turn, a 0.1 s shake on the snap. All inside the sway accessibility slider.

Audio: pneumatic thump and 0.4 s of line spool on throw, a metallic twang when it bites, wind loop scaled by speed, a click on release. The throw is the loudest thing the player owns after a gunshot, which is the tax.

## 9. Map consequences

A fixed rope is only as good as the things above the street, and the map must also provide the way up.

- Ladders, fire escapes, stairs and parked trucks for ascent. **The grapple never lifts you.**
- Lamp posts and utility poles every 30–40 studs along the highway, at 12–18 studs.
- Overhead cables and tree limbs.
- Roof edges and gaps sized for a 30-stud swing at 20 studs/s — about 12–18 studs of clear gap.
- **Swing lines**: deliberate sequences of two or three anchors where a fixed rope chains, the way a skate line is built.

If the map is a flat suburban block, this tool has nowhere to go and should not ship.

## 10. Risks

1. **The Humanoid will fight the velocity.** Everything hinges on `Physics` state. Test it before writing the controller.
2. **No reel may make it feel weak.** The honest test: can a player cross the highway gap from a run, without a winch? If not, the anchors are wrong, not the physics.
3. **It can still be an escape.** Charges, the weapon-down window, noise and wall collisions are the cost. If playtests show it is free, cut charges before touching the physics.
4. **Rope through geometry.** A sphere constraint does not wrap around corners. Accept it in v1; do not add rope simulation to fix a visual.
5. **It is a v3 feature.** It needs a map with verticality, three animations and a validation pass.

## 11. Open questions

1. Does the first map actually have swing lines worth building around?
2. Is pure momentum enough to make the tool feel good, or does the first swing need a one-time yank? *Default: pure. Test before adding.*
3. Can the line be cut by damaging the player mid-swing?
4. Do charges live on the tool (durability) or the raid (three per run)?
5. Is a pull-enemy upgrade ever acceptable in PvP, or is that PvE-only?
