# Reference - First-Person Architecture

> **Superseded, 2026-10-28.** Consolidated into [[Reference - First-Person and Gun Research]]. Historical research retained verbatim below; this copy is not maintained.

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]]

## 1. Separate the representations

The useful distinction is responsibility, not whether an approach is called “true first person.”

| Approach | How it operates | Advantage | Cost for No Dawn |
|---|---|---|---|
| Full animated body | Camera lives near the character's eyes; native body and held weapon supply the view | Visible self and consistent world pose | Head animation, shoulders, camera clearance and aiming must all agree |
| Dedicated arms and weapon | Local visual rig is positioned relative to the camera | Direct control over weapon framing and sight picture | Requires separate world representation; no lower body by itself |
| Hybrid | Native lower body plus separate local weapon/arm presentation | Supports looking down and carefully authored gun handling | Shoulder seams, duplicate arms, shadows and stance transitions need deliberate handling |

This comparison is engineering synthesis, not a claim that any named commercial game uses one exact implementation. No Dawn's October 26 record describes a hybrid already. Preserve that as the baseline until a measured defect requires changing it.

Bungie's first-person animation discussion treats arms, weapon motion and camera feedback as a coordinated part of player experience. This supports evaluating the whole action on screen, rather than only checking hand attachment error. It does not establish Bungie's solution as a drop-in Roblox architecture. [Bungie, first-person animation discussion](https://www.bungie.net/7/en/News/article/12707/undefined)

## 2. Give each transform an owner

**Proposal:** create an ownership map before changing offsets:

| Output | Sole owner | Inputs it consumes |
|---|---|---|
| View orientation and final camera transform | Camera controller | Input, stance, bounded head displacement, lean, recoil policy |
| Native locomotion pose | Character animator | Movement state, clip time, grounded state |
| Local weapon/arm rig | Weapon presentation | Final camera transform, weapon state, animation pose, grip/sight anchors |
| Visible slide, magazine and fore-end | Weapon presentation | Authoritative mechanical state plus predicted action timing |
| Damage and ammunition | Server weapon service | Validated request, inventory transaction, obstruction/hit policy |
| Remote player's held weapon | Remote presentation | Replicated semantic state and aim pose |

A body animator and a gun solver should not both write the same arm pose without a defined composition step. A camera should not derive its transform from a gun that is itself derived from that final camera. Such a dependency loop can amplify movement or create drift.

## 3. Understand coordinate spaces

Use named spaces: world, character root, camera, viewmodel root, weapon root and attachment-local.

**Derived example, not copied tutorial code:** let S be a sight reference relative to weapon root, and D the desired world-space sight reference. The root transform W must satisfy W × S = D, therefore W = D × inverse(S). Choose D using the camera, deliberate eye relief and the sight-axis convention.

This only works if:
- S is measured relative to the same root/pivot being moved.
- Attachment forward direction agrees with the chosen sight axis.
- Animation offsets are included at the intended stage.
- Eye relief is defined at the sight reference rather than accidentally at the gun origin.
- The reference follows the intended sight geometry when moving parts animate.

Transform multiplication order matters. Camera × recoil × aimOffset and camera × aimOffset × recoil rotate around different frames. Keep a small written transform chain beside the authoring convention.

**Practical consequence:** do not compensate for the wrong pivot by accumulating per-weapon offsets. A root, muzzle, rear sight, front sight and hand-grip reference should have explicit purposes.

## 4. Understand timing before treating jitter as bad math

Roblox's render bindings run by numeric priority; equal priorities have unspecified ordering. Default camera control is priority 200. A local weapon visual can be updated after the camera's final writer, using that frame's camera transform. Keep render callbacks short. [RunService](https://create.roblox.com/docs/reference/engine/classes/RunService)

Motor6D animation transforms are batched after PreSimulation. Animator usually writes them after PreAnimation and before PreSimulation; manual writes earlier can be overwritten. Transform is not replicated. This is relevant to native animated joints, not a reason to move every anchored visual part into the same callback. [Roblox Motor6D documentation source](https://raw.githubusercontent.com/Roblox/creator-docs/main/content/en-us/reference/engine/classes/Motor6D.yaml)

**Proposal:** audit the actual callbacks used by FirstPersonView, WeaponController, WeaponRig, WeaponAnimationPlayer and CharacterAnimator. Record the writer, phase, priority, source pose time and final consumer. Do not assume a blanket “use RenderStepped” edit fixes conflicts.

## 5. Camera comfort and body contact

**Proposal:** keep input rotation responsive; use only bounded, intentional animation displacement for the eyes. Test head bob, roll and landing motion independently. Existing vault rulings defer bob/roll/FOV effects until settings can control them.

Avoid copying every head rotation from a third-person clip into the camera: it may be acceptable for the body and unpleasant for the viewer. Define how crouch, lean and prone change eye position without letting the camera cross the chest, floor or wall.

Camera near-plane clipping is separate from physical obstruction. NearPlaneZ is read-only in Roblox; a plan that depends on freely adjusting it needs another solution. [Camera API](https://create.roblox.com/docs/reference/engine/classes/Camera)

When looking down, inspect shoulder joins and clothing as well as hands. The local rig must not become another physical character assembly. Hide duplicated native arms only where the replacement is actually active; verify restoration after menus, death, respawn and holster.

## 6. Hands, animation and IK

Roblox supports procedural IK on compatible rigs. IKControl requires an Animator under a Humanoid or AnimationController and valid chain/target settings. That is a rig requirement, not an automatic fix for detached visual parts. [IKControl](https://create.roblox.com/docs/reference/engine/classes/IKControl)

**Proposal:** choose which object leads each phase. A ready weapon can lead the right hand with support-hand IK; a reload can use authored hand motion while the removed magazine follows its holding hand. Blend support-hand influence away during removal and back after reseating. Do not solve the same hand simultaneously to an authored pose and a conflicting fixed grip.

Start with authored equip, ready, ADS, fire, tactical reload and empty reload. Add subtle procedural sway only after these contacts work. Test the existing anchored arm solution before assuming a rig conversion is necessary.
