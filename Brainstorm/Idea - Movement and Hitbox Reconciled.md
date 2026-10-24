---
tags: [idea, combat, movement]
status: shaping
updated: 2026-10-20
---

# Idea - Movement and Hitbox, Reconciled

Four research passes — movement systems, damage models, Roblox engine constraints, camera and feel — folded into one design. **Where this note contradicts [[Idea - Hitbox Model]] or [[Idea - Movement Frame]], this note wins.** Sources are named inline; anything I could not verify is marked.

## 1. The five things that changed

### The server's pose is approximate, not identical

[[Idea - Hitbox Model]] says the server "solves the same pose the player sees". Too strong. `Motor6D.Transform` is **NotReplicated** — it is local to the machine that wrote it. What replicates is the animation *track*: each peer runs the same clip on its own clock, one simulation step behind its own input, plus replication delay. So the server has the right *shape* at the wrong *time*. On weak clients `Animator.EvaluationThrottled` reuses the previous pose, which widens the gap further.

Consequence: animated parts are still the right source of truth for what a body *looks like*. But no amount of hitbox cleverness fixes a target that moved three studs since the server last knew about it.

### Rewind the root, not the pose

This is the correction that matters. The dominant error is translation, not posture:

| RTT | Error at 16 studs/s | Error at 24 studs/s |
|---|---|---|
| 50 ms | ~1.9 studs | ~2.9 studs |
| 100 ms | ~2.4 studs | ~3.6 studs |
| 150 ms | ~3.2 studs | ~4.8 studs |

A torso is about two studs wide. At 100 ms the server is aiming at where the target *was*. A 0.35-stud sphere does not cover that; it just turns a systematic miss into a random one.

**The cheap fix (~100 lines):** keep a 250 ms ring buffer of each character's `HumanoidRootPart` at 20 Hz. On a shot, rewind to `now − clamp(rtt/2 + 100 ms, 0, 180 ms)`, lerp between snapshots, rebuild the part CFrames from the **current** joint offsets, and cast there. Clamp to 180 ms and reject any rewind that moves the target more than 8 studs.

That fixes the 2–4 stud error and ignores pose rewind, which is the ten-times-the-cost, ten-per-cent-the-gain version. Pose rewind is a v3 luxury.

This also survives the pose-approximation problem: a held crouch is held for seconds, so the pose is right even when the clock is late. Only transitions are wrong, and they last 0.2 s.

### A uniform sphere inflates thin limbs

0.35 studs on a one-stud arm is +35% radius; on a two-stud torso it is +17%. So the sphere makes arms disproportionately fat and **manufactures the exact arm-eats-the-shot problem the priority window then tries to patch**.

`Spherecast` also has two documented traps: it does not detect parts it *starts inside* (point-blank on a zombie), and its `RaycastResult.Distance` is the sphere's travel distance, not origin-to-hit — which would quietly corrupt the 0.6-stud window. *(Distance semantics to be verified in Studio; the docs wording is ambiguous.)*

**Use a plain `Raycast` with `RaycastParams.Margin`** for thickness, and a `GetPartBoundsInRadius` fallback at 0.15 studs when the ray starts inside a body. Margin is the engine's own expansion; it does not have the start-inside blind spot.

### Group by character first, then by zone

The old rule compared every hit against the nearest hit, across everyone. In a horde, a back-row head within 0.6 studs of a front-row arm gets promoted and the bullet goes through a body.

**New rule:**

1. Cast, collect hits, discard `HumanoidRootPart` and accessories.
2. Group by character. The character with the nearest hit is the one you shot.
3. Inside that character only: if two zones are within 0.15 studs, the higher priority wins; otherwise nearest wins.
4. **Carry-through:** if a limb is hit and a torso or head on the same character is within 1.0 stud behind it, apply torso damage at 0.5× in addition. That removes the "my arm ate his whole chest" complaint instead of guessing intent.

### Limb hits need consequences or they are just nerfed damage

Tarkov's per-limb health pools are the cautionary tale: legs are unarmoured, spill damage at 1:1, and produced years of **leg meta** that BSG is still nerfing. Hunt does it better — limbs are cheap, unambiguous, and cost you *tempo*, not health.

**No per-limb pools.** Accumulate per-zone damage as integers and drive three states:

| State | Trigger | Effect |
|---|---|---|
| Limp | leg damage ≥ 35 | −25% movement speed, no sprint |
| Recoil | arm damage ≥ 30 | +40% visual recoil |
| Bleed | torso damage ≥ 25 | 0.8 HP/s, max 2 stacks, stops at bandage |

Roughly thirty lines, and it is the entire reason a limb hit will feel like something.

## 2. The hitbox we ship

- **Animated parts as the source of truth.** Unchanged, and correct.
- **Six joint gaskets.** Small invisible spheres at neck, shoulders, elbows, hips, knees, parented to the character, queryable. R15 has literal gaps at the joints and rays slip through. These are gaskets, not a second hitbox system.
- **`HumanoidRootPart.CanQuery = false` at spawn.** It is an invisible 2×2×1 box inside the torso and a known source of nondeterministic torso reads. `CanQuery` only applies when `CanCollide` is false, so set both.
- **Accessory handles: `CanQuery = false`.** A hat is not a head, and big hair should not be a bigger headshot.
- **Locked avatar scale and no UGC bundles in PvP.** Smaller packages are smaller hitboxes — that is free advantage, not cosmetics.
- **One ray, thickened by Margin,** with the radius fallback above.
- **Per-character grouping, 0.15-stud window, 0.5× carry-through.**
- **Root rewind** in v1.5, before any PvP is switched on.

### Zones

| Zone | Multiplier | Notes |
|---|---|---|
| Head | 3.0× | Any calibre of 34+ one-shots. The .45 does 150 |
| Torso | 1.0× | Upper and lower torso merged |
| Upper arm | 0.70× | Widened from 0.8 — the gap to torso has to hurt |
| Forearm, hand | 0.55× | |
| Thigh | 0.70× | |
| Shin, foot | 0.50× | |

Player 100 HP, standard infected 80. The M1911A1's .45 does 50 — two torso shots, or one head. 9×19 is 35 and 5.56 is 55 when they arrive, both still one-shot to the head at 3.0×. Shotgun: 8 pellets × 7, so a centred blast kills and a glancing one does not.

**Cut for v1:** armour, wall penetration, per-limb pools, stomach as a separate zone, distance falloff under 60 studs, per-pellet falloff, dismemberment.

## 3. Movement: what we take, what we refuse

**Take, in order of value per unit of cost:**

1. **A short stance ladder** (stand, crouch, prone) that changes speed, noise and silhouette.
2. **Weight-scaled speed.** Loaded sprint is slower, and heavy load removes sprint entirely.
3. **Stamina with a visible consequence** — breath and sway, not a bar.
4. **Lean that exposes the torso.** The peek is a decision.
5. **A short acceleration ramp** — 0.25 s to jog, 0.6 s to sprint. This is what kills the arcade juke.
6. **Height-gated vault and mantle**, so waist-high walls stop being invisible walls.
7. **Fall damage**, so verticality costs.
8. **Slide as an escape**, not a movement tech.

**Refuse:**

- **Tarkov's inertia stop.** The 2021 patch made stopping feel like input lag and the community never forgave it. Ramp *up*, not *down*.
- **Hunt's no-jump, no-prone minimalism.** Good for Hunt, wrong for a game with rooftops.
- **Squad and Ready or Not pacing.** At 224 metres across, you need escape velocity when a horde turns.
- **Lean spam and slide spam.** Both need a cooldown and a stamina cost or they become the only way anyone moves.

### Numbers

| State | Studs/s | Real |
|---|---|---|
| Walk | 6 | 1.7 m/s |
| Jog | 10 | 2.8 m/s |
| Sprint | 20 | 5.6 m/s |
| Sprint, loaded | 16 | 4.5 m/s |
| Sprint, heavy | 13 | 3.6 m/s |
| Crouch | 4 | 1.1 m/s |
| Prone | 2 | 0.56 m/s |

### Gravity is a decision nobody has made

Roblox's default is **196.2 studs/s²**. At 0.28 m per stud that is **5.6 g** — the reason Roblox characters feel snappy and why a realistic jump reads as floaty at real gravity (35 studs/s²).

Gravity is global: changing it changes props, ragdolls, vehicles and every arc in the game. **My recommendation: drop it to ~110–120** (about 3 g), which keeps jumps readable and makes falling weighty, and tune jump height to compensate. Do not chase realism here; chase consistency. **Flagged as a decision, not a default.**

## 4. Feel

**Camera.** Sprint bob 2.2 cm at 2.6 Hz, strafe roll 1.8°, landing dip 7 cm over 150 ms with a 280 ms settle, ADS FOV 60° from 85° base. Apply everything in `BindToRenderStep` at `RenderPriority.Camera + 1`, or the default camera scripts overwrite roll and bob. Use `Humanoid.CameraOffset` for crouch, lean and bob; `HipHeight` for body height.

**Viewmodels.** Roblox has no viewmodel FOV, no weapon-depth pass, and parts parented to the camera clip through walls. The workaround is a viewmodel 0.6–1.2 studs out, in a no-collision group, that fades or drops when a forward ray hits within half a stud. There are **no additive animation layers**: give the viewmodel its own `Animator` or drive its joints procedurally. `IKControl` handles support hand and foot planting.

**Audio.** Never a looping timer — it assumes constant speed and every step drifts. Use a distance accumulator:

```
traveled += horizontalSpeed * dt
if traveled >= stride then playStep(nextFoot); traveled -= stride end
```

Stride: walk 0.75 m, jog 1.05 m, sprint 1.5 m. Four to six samples per surface and gait, ±4–6% pitch, and never the same sample twice in a row.

**Accessibility, non-negotiable.** Bob, roll and shake each get a 0–100% slider with no gameplay penalty, plus FOV 75–110 and a reduce-motion preset. Tarkov's 0.2 floor is the anti-pattern.

**Readability.** A prone body at distance is a log, not a person. Raise the head and shoulders, bend one knee, add a contact shadow and breathing audio. Posture has to change the *outline*, not just the height.

## 5. What this costs

- **Hitbox:** the service is written and its zone table is unit-tested (9/9). Remaining: joint gaskets, accessory and root handling, per-character grouping, carry-through, and the root rewind. ~2 days.
- **Movement:** state machine, camera, stamina, surface audio — 3–5 days. The real cost is asset work: ~19 body clips, ~8 first-person clips, ~110 audio files.
- **The blocker:** there is no animator and no animation set. A stance system without clips is a camera and a speed change, which is exactly the lie this design exists to avoid.

## 6. Decisions to approve

1. **Ray with Margin**, not Spherecast. Recommended: yes.
2. **Root rewind in v1.5**, before PvP. Recommended: yes.
3. **Per-character grouping, 0.15-stud window, 0.5× carry-through.** Recommended: yes.
4. **Limb states instead of limb health pools.** Recommended: yes.
5. **Zone multipliers as tabled**, head 3.0. Recommended: yes.
6. **Locked avatar scale, no UGC bundles, no accessories in PvP.** Recommended: yes.
7. **Gravity 110–120.** Recommended: yes, with jump height retuned.

## 7. Open questions

1. Does the first map need prone, or does crouch-plus-lean carry v1? Prone is the most expensive stance to animate and the easiest to make unreadable.
2. Viewmodel: separate `Animator` with authored clips, or fully procedural arms? Procedural is cheaper and reads worse under stress.
3. Do PvE-only raids ship first, which would let us defer rewind until PvP exists?
4. Is 0.45 the right prototype calibre if 50 damage makes headshots trivially lethal? A 9 mm at 26 would teach us more about the zone table.
