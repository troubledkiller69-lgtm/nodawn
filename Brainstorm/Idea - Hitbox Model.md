---
tags: [idea, combat, movement]
status: shaping
updated: 2026-10-18
---

# Idea - Hitbox Model

Resolves the open problem in [[Idea - Movement Frame]] and feeds [[Idea - Combat Frame]]. One system, because it is the same question asked twice: **where is the player's body, and can a bullet reach it.**

**The pitch:** damage reads the body's actual animated pose. No capsule, no second set of invented boxes, no stance logic inside the hit code. One source of truth, so what you see is what you can hit.

## 1. The correction

[[Idea - Movement Frame]] says custom hitboxes are mandatory because prone cannot change the collider. Half right, and the wrong half sends the build down a bad road.

The collider is `HumanoidRootPart`, not the body parts. The R15 limbs are driven by Motor6D joints, the animation is replicated, and **the server solves the same pose the player sees.** So prone, crouch, lean and slide already move the parts on the server.

| | Capsule | Body parts |
|---|---|---|
| What it is | HRP plus Humanoid floor logic | Fifteen R15 limbs on Motor6D |
| Follows prone, crouch, lean | No | Yes |
| Should damage read it | Never | Yes |

Which flips the conclusion: synthetic boxes would be *worse*, not better. They would have to be posed independently of the animation by a system that guesses, and every frame of disagreement is "I shot him in the head and he lived."

## 2. Why the parts are enough

- Fifteen parts with clean semantic zones.
- Fifteen raycast candidates per shot is nothing, even at twenty-four players.
- Every stance, slide, vault, mantle, death pose and future ragdoll works for free, because they are all the same rig.
- No duplication: the thing you see is the thing the server tests.

**The one unproven assumption, to verify in Studio before anything else is built:** a crouch or prone animation that translates the `Root` Motor6D moves the server's part CFrames on the same frame as the client's. It should, because the Animator replicates the track and the server runs it. Verify with a marker part welded to the Head and a print of `Head.Position.Y` while prone. If that number does not drop, this whole note is wrong and we go back to synthetic volumes.

## 3. The ray

Not a thin ray. A **spherecast**, radius 0.35 studs.

Why: a thin ray finds the gap between an arm and a torso, or between the legs, and misses a target the player was clearly aiming at. At 0.28 m per stud, 0.35 studs is ten centimetres of forgiveness, under the width of a hand, and roughly one server tick of movement for a sprinting target. It is the smallest number that hides the seams in a blocky rig.

```lua
local params = RaycastParams.new()
params.FilterType = Enum.RaycastFilterType.Include
params.FilterDescendantsInstances = { character }

local hit = workspace:Spherecast(origin, 0.35, direction, params)
```

Two passes, always:

1. **World** with every character excluded. If the wall is closer than the body, the shot is blocked. No exceptions, no shooting through railings.
2. **Characters** with only the hitbox group included.

### Multi-hit

An arm across the chest should not eat a torso shot, but a body behind an arm should not be reachable through it. So: take up to three hits, each cast starting just past the last, and pick the highest-priority zone **within 0.6 studs of the nearest hit**. Anything further back is occluded.

That single rule is the whole "limbs feel fair" problem, and it needs a test range rather than a spreadsheet: a target at ten studs with an arm-shaped occluder, shot from five angles.

## 4. Zone map

| Part | Zone | Multiplier |
|---|---|---|
| Head | Head | 3.0 |
| UpperTorso, LowerTorso | Torso | 1.0 |
| LeftUpperArm, RightUpperArm | Limb | 0.8 |
| LeftLowerArm, RightLowerArm, LeftHand, RightHand | Limb | 0.65 |
| LeftUpperLeg, RightUpperLeg | Limb | 0.75 |
| LeftLowerLeg, RightLowerLeg, LeftFoot, RightFoot | Limb | 0.6 |
| HumanoidRootPart | **Excluded** | — |

Upper arms at 0.8 rather than 0.7 on purpose: a sideways target hit three times in the arm should be in trouble, not confused.

Torso hits apply bleeding. Limbs do not. Head is lethal for .45 against an unarmoured player, which is the number the combat frame already committed to.

## 5. Stance is input, never a hitbox parameter

This is the part that stops the two systems drifting apart:

> `HitZoneService` never asks what stance a player is in. It reads whatever pose exists.

Stance affects movement, stamina, camera and legality. It does not affect damage. A player mid-transition, half-leaned, caught standing up, or sliding on their side is hit where their body actually is, because the body is the only input.

The moment a hitbox has a `stance` argument, someone writes a case for a stance the animation does not match, and the game starts lying.

## 6. Lean, done honestly

Camera-only leaning is the classic exploit: your view moves, your body does not, and you can see around a corner with nothing exposed. The alternative is not a synthetic hitbox — it is **animating the lean**.

- Root joint translates 0.4 studs laterally. Legs stay planted; the hips shift.
- Spine bends about 12 degrees on top of that.
- Head roll for feel, applied to the camera only.
- Total head travel is about 0.7 studs. That is the peek.
- The first-person camera rides the Head part, so the view comes along by construction. No `CameraOffset` hack, no desync.

**Clearance check.** Before the state is entered, cast laterally 0.9 studs at chest height. Blocked, refuse the lean. Without it, players lean their heads through walls and see the other side. That is the number one lean exploit and it costs one raycast.

**The cost is real:** leaning exposes your head, and the head is a 3.0 multiplier. Peeking is a decision, which is exactly what the movement note asked for.

## 7. Crouch, prone, slide

- **Crouch:** the animation lowers the body about 1.4 studs. The collider does not change, and in v1 it does not need to, because no geometry requires crawling under anything. If the map later adds crawl spaces, resize HRP then, not now.
- **Prone:** v2. Body flat, head at about 0.8 studs, collider untouched. It will look wrong against waist-high cover and be correct against bullets, which is the right trade.
- **Slide:** animation-only. Head drops to about 2.2 studs. Hit zones follow.

If a stance needs to pass under geometry, that is a collider question, and it gets answered when the map asks it. Damage never needs the answer.

## 8. Ownership and validation

| Thing | Owner | Rule |
|---|---|---|
| Stance state | Server | One attribute, replicated read-only to the client |
| Stance request | Client | Remote, sequence-numbered, rate-limited by `RequestGuard` |
| WalkSpeed, jump | Server | Written on every stance change |
| Camera, bob, roll | Client | Cosmetic, never trusted |
| Lean clearance | Server | Re-checked; a refused lean never replicates |
| Stamina | Server | Client predicts, server corrects |
| Hit registration | Server | Spherecast against replicated poses |

Legal transitions, enforced server-side:

```
stand  → crouch, sprint, lean, vault, mantle, slide
crouch → stand, lean, slide
lean   → stand, crouch
sprint → stand, crouch, slide, vault
slide  → crouch, stand
prone  → stand, crouch          (v2)
```

Transition durations come from [[Idea - Movement Frame]]: crouch 0.2 s, prone 0.5 down and 0.8 up. A request that arrives faster than the duration is dropped, not applied late.

**Speed.** The server writes `WalkSpeed` and compares displacement over one second against the cap plus fifteen per cent. Sustained overspeed for a second corrects the position to the last trusted spot. That is a rubber-band, it is ugly, and it is the honest ceiling for a Humanoid without a custom controller. Write it down as a limitation rather than pretending it is solved.

## 9. What this costs

- One module for the zones and the ray, perhaps six hundred lines.
- One module for stance and validation, perhaps four hundred.
- Animations: lean left and right, crouch idle and walk, prone idle and crawl, slide, vault, mantle, stand-up. Twice, for first-person arms and third-person body.
- A test range with targets at 10, 25 and 50 studs, an arm occluder, and a corner to lean around. This is now a hard dependency for both this note and the M1911.
- The tolerance is the tuning knob. Everything else is a constant.

## 10. Decisions to approve

1. **Damage reads the animated parts.** No synthetic boxes, no stance parameter in the hit path. Recommended: yes.
2. **Spherecast at 0.35 studs,** tuned by measurement on the test range. Recommended: yes.
3. **Lean is an animation with a server-side clearance check,** not a camera offset. Recommended: yes.
4. **Collider unchanged in v1.** HRP resized only when the map contains something worth crawling under. Recommended: yes.
5. **One server-owned stance attribute.** Client requests, server decides. Recommended: yes.
6. **Zone multipliers as tabled.** Head 3.0, torso 1.0, limbs 0.6 to 0.8. Recommended: yes.

## 11. Open questions

1. Do arms block torso shots? This note says nearest hit wins unless another zone is within 0.6 studs. Only the test range will settle it.
2. Does crouch reduce aim sway, or only lean add it? One of the two, not both.
3. Is prone v1 or v2? Currently v2, tied to whether the first map has low cover worth using.
4. Is vault trusted-with-a-postcheck or fully server-validated per destination? The second is safer and slower.
5. Does the first-person viewmodel need to match the lean angle exactly, or can it lag by a tenth of a stud without being noticed?
