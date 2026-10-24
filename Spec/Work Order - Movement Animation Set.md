---
tags: [nodawn, workorder, animation]
status: in-progress
updated: 2026-10-23
---

# Work Order - Movement Animation Set

Back to [[Open Items]] · Source design: [[Idea - Movement and Hitbox Reconciled]], [[Idea - Movement Frame]], [[Idea - Running Feel]] · Process: [[Working Agreement]]

```
TASK: Third-person body clip set for No Dawn movement, stages v1 to v3.
TARGET: Roblox Studio, stock blocky R15 (content/avatar/characterR15.rbxm). Authored in Blender 5.2.
DELIVERABLE: animation/out/ND_Movement_<stage>.rbxm (KeyframeSequences), animation/NoDawn_Movement.blend,
             animation/out/manifest.txt. Folder is Desktop\No Dawn\animation, outside the vault and its git repo.
SCALE: 1 stud = 0.28 m. Rig 5.0 studs tall; hip pivot 2.0 above the floor; leg hip-to-sole 2.0
       (thigh 0.822, shank 0.926, ankle-to-sole 0.252).
BUDGET: baked 30 fps, linear easing, 15 joints. One KeyframeSequence per clip.
CONSTRAINTS:
  - Pose names = R15 part names; every C0/C1 on the stock rig is rotation-free, so a Pose CFrame is the joint Transform.
  - Clips never move HumanoidRootPart. Bob, drop and lean ride the Root joint (LowerTorso pose).
  - Locomotion is in place; feet stay planted at the authored speed. Code scales playback by speed / authored speed.
  - Loops: first frame equals last frame.
  - Arms are unarmed. Weapon holds are separate upper-body Action clips, made with each weapon.
  - Head kept level during locomotion (camera and the head hit zone).
  - Footstep KeyframeMarkers ("Footstep", value "L" or "R") at each foot contact.
ACCEPT:
  - inserts into Studio and plays on the stock R15 with no missing-pose warnings
  - loop seam under 0.1 degree on every joint
  - stance-foot slide at the authored speed under 0.05 studs; sole never below the floor by more than 0.02
  - head pitch inside +/-3 degrees across each locomotion cycle
OUTPUT: rbxm + blend + manifest.txt (clip, length, loop, priority, authored speed, cadence, step length, markers).
DO NOT: touch the place's Animate script, default Roblox animation ids, or any Luau. Wiring is Nyx's.
```

## Clips

| Stage | Clip | Type | Length | Priority | Authored at | From the notes |
|---|---|---|---|---|---|---|
| v1 | ND_Idle | loop | 4.0 s | Idle | standing | breath idle, weight shift |
| v1 | ND_Walk | loop | 1 cycle | Movement | 6 studs/s | Alt walk |
| v1 | ND_Jog | loop | 1 cycle | Movement | 10 studs/s | default gait |
| v1 | ND_Sprint | loop | 1 cycle | Movement | 20 studs/s | arms pumping; weapon lowering is the weapon's own clip |
| v1 | ND_SprintStop | one-shot | 0.20 s | Action | 20 → 0 | planted stop |
| v1 | ND_CrouchIdle | loop | 3.0 s | Idle | crouched | head about 1.4 studs lower |
| v1 | ND_CrouchWalk | loop | 1 cycle | Movement | 4 studs/s | |
| v1 | ND_Jump | one-shot | 0.20 s | Action | | takeoff, 4-stud jump |
| v1 | ND_Fall | loop | 0.8 s | Movement | | |
| v1 | ND_LandSoft | one-shot | 0.15 s | Action | | dip 0.3 studs |
| v1 | ND_LandHard | one-shot | 0.45 s | Action | | from 10+ studs |
| v1 | ND_Vault | one-shot | 0.40 s | Action | | obstacle 3–4.5 studs; code moves HRP |
| v1 | ND_Mantle | one-shot | 0.70 s | Action | | ledge up to 4.5 studs; code moves HRP |
| v1 | ND_Slide | one-shot | 0.90 s | Action | | head to about 2.2 studs |
| v2 | ND_LeanL, ND_LeanR | hold | 0.3 s in | Action | | head travel 0.7 (built as a 25° spine bend, upper body only) |
| v2 | ND_CrouchLeanL, ND_CrouchLeanR | hold | 0.3 s in | Action | | same, crouched |
| v2 | ND_ProneIdle | loop | 4.0 s | Idle | | head about 0.8; readable outline: head and shoulders up, one knee bent |
| v2 | ND_ProneCrawl | loop | 1 cycle | Movement | 2 studs/s | |
| v2 | ND_ToProne, ND_FromProne | one-shot | 0.5 s / 0.8 s | Action | | |

Held, not in this order:
- **First-person clips (about 8).** These wait on the viewmodel decision, open question 2 in [[Idea - Movement and Hitbox Reconciled]]: a separate `Animator` with authored clips, or procedural arms.
- **Wounded locomotion and the treatment loops.** These wait on approval of [[Idea - Injury and Survival]].

## Pipeline

1. `animation/src/` (Python) generates every clip: foot paths solved with two-bone IK, then baked to joint rotations in Roblox axes.
2. Blender loads the same data onto an exact R15 rig, for review renders.
3. Lune writes the KeyframeSequences to `.rbxm`.
4. **Human step:** insert the rbxm in Studio, then right-click each KeyframeSequence → Save to Roblox to get its asset id. Before publishing, `KeyframeSequenceProvider:RegisterKeyframeSequence` gives a Studio-only id for playtests.

## Progress

- [x] v1 locomotion: Idle, Walk, Jog, Sprint
  - 2026-10-20: first export, 4 clips.
  - 2026-10-21: rebuilt on the real R15 meshes against human gait data (Winter walk, Novacheck run), with joint
    limits, balance and real-mesh floor contact gated. Walk 3.40 steps/s, step 1.76; Jog 3.00, step 3.33, bob 8.5 cm;
    Sprint 3.73, step 5.36.
- [x] v1 remainder: SprintStop, CrouchIdle, CrouchWalk, Jump, Fall, LandSoft, LandHard, Vault, Mantle, Slide
  - 2026-10-21: `out/ND_Movement_v1.rbxm` holds all 14 KeyframeSequences; Lune verify PASS (max error 7.7e-6);
    `out/manifest.txt` rewritten with the notes for code.
  - Every ACCEPT line met (seam 0.000°, stance slide 0.000, no sole below the floor, head pitch inside ±3°), plus the
    realism gates. Numbers, gates and design rulings: [[Realism Ledger#Movement animation set v1 (2026-10-21)]].
  - Studio: every clip plays on the stock R15 with all 15 joints driven and no warnings, on a Motor6D rig and on the
    AnimationConstraint rig that `CreateHumanoidModelFromDescription` builds in this place.
  - Left for a human: insert the rbxm and Save to Roblox for asset ids (Pipeline step 4).

![[nd_movement_v1_all_clips.jpg|900]]

- [x] v2: LeanL/R, CrouchLeanL/R, ProneIdle, ProneCrawl, ToProne, FromProne
  - 2026-10-23: built in `animation/src/ndanim/stance.py`; every gate PASS and the 14 v1 clips came out
    byte-identical. `out/ND_Movement_v1.rbxm` now holds 22 KeyframeSequences (Lune verify <= 7.7e-6).
  - Prone lies along -Z with the HRP standing over the hips: body flat, head centre 0.95 above the floor
    (a level R15 head on a flat torso can't sit at the note's 0.8), forearms ahead on the floor, the
    right knee drawn up and out for the outline. The crawl is a 1.0 s low crawl at 2 studs/s, the
    forearms planted in turn. ToProne (0.5 s) starts from CrouchIdle; FromProne (0.6 s) ends on it.
  - The leans are upper body only: a 25 deg spine bend gives 0.64-0.71 studs of head travel (the note's
    0.4 hip shift plus 12 deg, carried by the spine), and LowerTorso and the legs are exported at Pose
    Weight 0, so the loop underneath keeps the legs while leaning or shuffling.
  - Installed in the hub and played: see [[Realism Ledger#Prone and lean (2026-10-23)]].
  - Left for a human: Save to Roblox for the eight new ids (Pipeline step 4).
