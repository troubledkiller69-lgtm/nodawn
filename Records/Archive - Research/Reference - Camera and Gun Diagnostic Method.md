# Reference - Camera and Gun Diagnostic Method

> **Superseded, 2026-10-28.** Consolidated into [[Reference - First-Person and Gun Research]]. Historical research retained verbatim below; this copy is not maintained.

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]] · Existing work: [[Open Items]]

This is a proposed investigation protocol, not a second live checklist and not a claim that these checks were run. Its purpose is to reduce repeated usage by ensuring each future edit tests one explanation.

## 1. Describe one visible failure

Use a sentence such as “during right lean, the rear sight moves left of the front sight after ADS settles,” or “looking down while walking reveals a gap between the replacement upper arm and torso.” “First person is broken” combines too many subsystems.

Record weapon, action, stance, camera pitch, FOV, resolution/aspect ratio, avatar scale/outfit, frame rate, environment and whether this is local or remote observation. Keep the same short input sequence for before/after comparison.

## 2. Build the smallest reproducer around the current setup

**Proposal:** a clean floor, wall, doorway, low cover and stationary target are enough. Use the actual character and existing weapon. Establish the unmodified baseline; disable one presentation layer at a time through temporary developer switches. Avoid replacing controllers as a diagnostic experiment.

| Observation | Hypotheses to distinguish | Smallest useful experiment |
|---|---|---|
| Weapon jitters when turning | Camera/weapon update ordering; differing pose sampling times | Freeze recoil/sway; record camera and rig transforms per frame |
| Hands remain attached but pose looks wrong | Grip frame, shoulder origin, limb proportions, FOV | Show side view and first-person view of the same static pose |
| ADS works level but fails looking down | Mixed world/camera axes; camera-body feedback loop | Sweep pitch with animation frozen |
| Aiming hits through a wall | Camera-only firing; incorrect muzzle origin/filter | Stand at doorway with muzzle blocked and target visible |
| Body rises or moves when manipulating arms | Physical assembly involvement | Compare assembly/root motion with visual arms enabled/disabled |
| Reload numbers and magazine disagree | Conflicting phase ownership; duplicate event; lost cancel | Log action ID and commit phase across cancel/re-equip |
| Local looks correct, remote wrong | Local-only pose/state; stale replication | Same action, two-client recording |
| Prone is hit above the visible body | Damage query still uses standing geometry | Visualize authoritative target volumes independently |

These are possible causes, not diagnoses.

## 3. Instrument only what resolves the question

**Proposal:** sample a bounded capture, then stop. Include frame timestamp; callback/writer name; camera and weapon-root transforms; sight/grip reference; action ID and phase; muzzle and accepted ray; authoritative ammo and mechanical state.

Useful measures:
- Sight error projected in screen pixels, alongside angular error.
- Frame-to-frame camera-to-weapon variation when motion is supposed to be steady.
- Root displacement caused by presentation alone.
- Difference between requested and accepted muzzle origin.
- Number of ammo commits and effects per accepted action.
- Pose discontinuity at transition boundaries.
- Camera/weapon solve time and memory growth during repeated equip cycles.

The October 26 hand error near 10^-6 studs shows grip closure in those checks. It cannot establish eye relief, screen composition, frame stability, anatomical plausibility or perceived weight.

Roblox's MicroProfiler supports identifying costly tasks and saving frame captures. Use it when the symptom is stutter or growing frame cost; average FPS alone can hide spikes. [MicroProfiler walkthrough](https://create.roblox.com/docs/performance-optimization/microprofiler/use-microprofiler)

## 4. Use a staged acceptance sequence

**Proposal:**
1. **Static:** ready and settled ADS, pistol first; look level/up/down; inspect shoulders and clipping.
2. **Transitions:** equip, aim/un-aim, fire/recover, reload/cancel, mag check, holster and menu return.
3. **Movement:** walk, crouch, both leans, prone entry/exit, sprint and traversal restrictions.
4. **Environment:** wall, doorway, low cover, uneven ground and camera already near geometry.
5. **Network:** second player, late observation, delayed connection and repeated action requests.
6. **Lifecycle:** death, respawn, disconnect during action and re-equip.

Run 16:9 plus a narrow/wide viewport, and representative 30/60/high-frame-rate conditions where available. The exact target matrix is a proposal, not a requirement to run every combination after every small edit. Start with the failing case, then check adjacent cases affected by the change.

Roblox supports multi-client testing and device simulation. Use the client view and another player's view, not merely a server Explorer pose. [Studio testing modes](https://create.roblox.com/docs/studio/testing-modes)

Roblox's Network Simulator can independently add inbound/outbound latency, jitter and loss. Added one-way delays contribute to round-trip ping; report the applied values. Compare repeatable baseline and impaired runs. [Network Simulator](https://create.roblox.com/docs/studio/network-simulator)

## 5. Stop an unproductive fix loop

**Proposed working rule:** before an edit, write the symptom, evidence, hypothesis, smallest change and pass criterion in a short temporary investigation record. After it, capture the same reproduction. If it fails, revert or explain what new evidence justifies retaining it.

Do not expand the weapon roster, rewrite camera architecture and retune animations in the same experiment. A successful fix should explain the failure and survive the affected transitions, not merely improve one still image.

Close with an evidence package: short before/after clip, exact reproduction, changed owner/transform/phase, relevant measurements, regression results and unresolved subjective concerns. A visual defect needs visual acceptance as well as arithmetic.

## 6. Questions research cannot answer without the actual place

Which callback currently wins the camera? Does the live script still match the October 26 record? Is the problem shoulder shape, motion, obstruction, shot origin or all four? Are remote poses reconstructed? Does the current pump identity match any manufacturer reference?

These are the next targeted observations. None is a reason to guess a root cause or start another broad rewrite.
