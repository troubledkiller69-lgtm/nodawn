---
tags: [nodawn, workorder, animation]
status: in-progress
updated: 2026-10-20
---

# Work Order - Movement Animation Set

Back to [[Open Items]] · Source design: [[Idea - Movement and Hitbox Reconciled]], [[Idea - Movement Frame]], [[Idea - Running Feel]], [[Idea - Grapple]] · Process: [[Working Agreement]]

```
TASK: Third-person body clip set for No Dawn movement, stages v1 to v3.
TARGET: Roblox Studio, stock blocky R15 (content/avatar/characterR15.rbxm). Authored in Blender 5.2.
DELIVERABLE: animation/out/ND_Movement_<stage>.rbxm (KeyframeSequences), animation/NoDawn_Movement.blend,
             animation/out/manifest.txt. Folder sits next to the vault, outside its git repo.
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
| v2 | ND_LeanL, ND_LeanR | hold | 0.3 s in | Action | | Root 0.4 lateral, spine 12°, head travel 0.7 |
| v2 | ND_CrouchLeanL, ND_CrouchLeanR | hold | 0.3 s in | Action | | same, crouched |
| v2 | ND_ProneIdle | loop | 4.0 s | Idle | | head about 0.8; readable outline: head and shoulders up, one knee bent |
| v2 | ND_ProneCrawl | loop | 1 cycle | Movement | 2 studs/s | |
| v2 | ND_ToProne, ND_FromProne | one-shot | 0.5 s / 0.8 s | Action | | |
| v3 | ND_GrappleAttach, ND_GrappleSwing, ND_GrappleRelease | 1 loop + 2 one-shots | 0.2 / 1.0 / 0.15 s | Action | | left arm up, legs trailing |

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
  - 2026-10-20: `out/ND_Movement_v1.rbxm` (4 KeyframeSequences) and `out/manifest.txt` written; Lune export verify PASS.
  - Loop seam 0.000°, stance slide ≤0.032 studs, sole below floor 0.000, head pitch within ±3°, 0 IK clamps.
  - Review scene: `NoDawn_Movement.blend`; static part-CFrame preview `blender/static_preview.py`; `out/jog_cycle.gif`.
- [ ] v1 remainder: SprintStop, CrouchIdle, CrouchWalk, Jump, Fall, LandSoft, LandHard, Vault, Mantle, Slide
- [ ] v2
- [ ] v3
