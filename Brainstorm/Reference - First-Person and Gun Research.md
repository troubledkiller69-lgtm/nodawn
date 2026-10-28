---
tags:
  - reference
  - nodawn
  - combat
  - research
updated: 2026-10-28
---
# Reference - First-Person and Gun Research

Back to [[No Dawn - Home]] · Related: [[Reference - Grounded Survival Improvements]], [[Combat - Weapon and Walker Pass]]

Research compiled and consolidated 2026-10-28. This is the maintained research note for cameras, weapon presentation and firearm behavior. Findings and proposed experiments are not approved specs or implemented features.

## Which note owns what

| Question | Owner |
|---|---|
| What still needs doing? | [[Open Items]] — the only live task list |
| What was built and tested? | [[Realism Ledger]] and the dated combat record linked above |
| What must the firearm deliver? | [[Addendum - Infected and Firearms]] and [[Weapon - M1911A1 Build Spec]] |
| Why might the camera or gun fail, and what should we investigate? | This note |
| What other realism ideas are worth exploring? | Grounded Survival Improvements, linked above |
| What movement design supersedes the older drafts? | [[Idea - Movement and Hitbox Reconciled]]; later implementation rulings remain in the ledger |

The October 26 combat record describes native body/legs plus six isolated clothing-matched visual arm parts, weapon-animation fallback and calibrated pistol/pump holds. It reports tiny grip/sight errors and 204/205 tests, with an existing eyewear regression. Those historical checks are not a fresh inspection of today's place. Visual framing, comfort, transitions, remote observation and muzzle obstruction require their own evidence.

## First-person architecture

| Representation | Benefit | Main cost |
|---|---|---|
| Full animated body near the eyes | Consistent visible self/world pose | Head motion, shoulders and aiming must agree |
| Camera-relative arms and weapon | Direct control of framing and handling | Separate world representation; lower body absent |
| Native lower body plus local arms | Visible self and controlled gun presentation | Shoulder seams, duplicate limbs, shadows and stance transitions |

No Dawn already has the hybrid approach according to the combat record. Investigate it before choosing a replacement. No commercial game's exact architecture is inferred from its appearance.

**Proposed ownership:** one controller produces the final camera; locomotion owns the native body pose; weapon presentation consumes that camera and owns local arms/mechanical visuals; the server owns ammunition and damage; remote presentation reconstructs visible actions from replicated state. Two systems must not write the same output without a defined composition step.

Keep world, root, camera, weapon and attachment-local spaces explicit. If S is the sight transform relative to the weapon root and D is its desired world transform, the derived relationship is W × S = D, so W = D × inverse(S). Root/pivot convention, attachment axis, animated offsets and eye relief must agree. Changing multiplication order changes the space in which an offset acts.

Render bindings run by numeric priority; equal priorities have unspecified order and default camera control uses 200 [R1]. Motor6D transforms are batched after PreSimulation, Animator can overwrite earlier writes, and Transform is not replicated [R2]. Audit actual writers and phases in FirstPersonView, WeaponController, WeaponRig, WeaponAnimationPlayer and CharacterAnimator. Anchored visual parts and native animated joints need not share the same update method.

**Proposals:** keep input rotation responsive; use bounded eye displacement rather than copying every third-person head rotation; inspect shoulders/clothing when looking down; restore native arms on holster/menu/death/respawn; ensure visuals cannot move the physical assembly. NearPlaneZ is read-only [R3]. Compatible IK rigs need an Animator and valid chain/target configuration [R4–R5]; this is not an automatic repair for detached parts.

Choose a leader for each contact: weapon-to-grip during ready, authored hand-to-magazine during removal, then blended return. Do not constrain a hand to incompatible authored and procedural targets simultaneously. Respect the existing accessibility/settings rulings before adding bob, roll or FOV effects.

## Firearm state and transactions

The proposed chain is input → allowed action → authoritative state/transaction → shot resolution → visual/audio response → reconciliation. Immediate local prediction must recover if the server rejects the action.

Keep separate state dimensions:
- **Action:** ready, equipping, firing, cycling, reloading, checking, holstering.
- **Mechanical:** chamber, magazine/tube and slide/bolt/action position.
- **Presentation:** hip/ADS, recoil, sway and lowered fraction.
- **Character restrictions:** sprint, traversal, transitions, menus and death.

Use weapon instance identity and action/shot IDs. The server validates alive/equipped status, action, ammo, rate, payload structure, finite vectors, plausible origin and legal targets [R6]. Client-reported hits alone do not justify damage.

| Event | Consequence to specify | What presentation must show |
|---|---|---|
| Accepted fire | Spend one valid round once | Shot, recoil and mechanism cycle |
| Magazine removal | Apply explicit retention/ownership policy | Magazine leaves the grip |
| Replacement seated | Commit inventory transfer once | Magazine seated |
| Action completed | Ready only when mechanics allow | Hands and sights return |
| Cancel/death/disconnect | Resolve committed phases consistently | Reconstruct current state |

Animation markers synchronize presentation; missing or repeated client markers must not duplicate transactions or leave actions stuck. Late observers should reconstruct current state without replaying the entire history.

The existing pistol spec uses a seven-round magazine plus separate chamber, visible cycling, empty slide lock and distinct tactical/empty reloads. Colt's modern Classic page corroborates a seven-round .45 configuration, not every detail of a military M1911A1 [D7].

For the pump, investigate tube/chamber/cycle state, per-shell commits, interruption readiness and support-hand contact with the moving fore-end. Its exact model remains unidentified in the combat record. Mossberg is a family comparison, not proof of our asset's identity or capacity [D8].

Physical individual-magazine inventory would change schema, UI and loot; do not silently substitute it for the existing reserve-round abstraction. Defer malfunctions until ordinary state transitions are dependable.

## Aim, muzzle obstruction and hit detection

**Unresolved documentation conflict:** [[Idea - Combat Frame]] favors camera-axis hit detection; the M1911 spec requires an actual aiming solution agreeing with sights. This note does not decide the conflict.

**Proposed evaluation:** choose intended aim point → resolve gameplay muzzle origin → check muzzle-to-point path → stop at cover → display the accepted result. Roblox's laser example separates camera target selection from weapon-origin raycasting and validation [R8]. That alone does not implement realistic bore constraints or sight zeroing. Decide explicitly whether bore direction constrains the shot and whether close-range sight/bore offset is preserved or compensated.

Ray direction magnitude sets range; filters/CanQuery determine eligible hits, and streamed clients may lack distant geometry [R7]. Keep physical collision, damage volumes, concealment and cover distinct. Camera/weapon clearance may need volume queries; test starting overlaps and current shapecast semantics [R9]. Rewind and physical ballistics are separate projects, not prerequisites for a visual repair.

Mechanical audio should follow the same semantic phases as animation. Deduplicate predicted/replicated effects by shot identity. AI noise remains a server gameplay event, independent of client sound mix or quality settings.

## Diagnostic method

This is a reusable method, not another backlog.

1. Describe one visible failure: weapon, action, stance, pitch, FOV, aspect ratio, outfit, frame rate and local/remote viewpoint.
2. Reproduce it with the existing character/weapon on a simple floor, wall, doorway and target.
3. State evidence, hypothesis, smallest change and pass criterion before editing.
4. Compare the same short input sequence before/after; revert or justify failed experiments.
5. Extend verification only to affected transitions and adjacent cases.

| Symptom | Distinguishing observation |
|---|---|
| Jitter while turning | Freeze recoil/sway; compare camera/rig timing |
| Hands attached but pose wrong | Static first-person and side view; grip frame, shoulder and proportions |
| ADS fails only looking down | Pitch sweep with animation frozen; detect mixed spaces or feedback loop |
| Shot crosses cover | Camera sees target while muzzle is blocked |
| Body moves during arm solving | Root/assembly motion with visuals enabled/disabled |
| Reload numbers disagree with visuals | Action ID and transaction phase across cancel/re-equip |
| Remote pose differs | Two-client recording of identical action |
| Prone target differs from visible body | Visualize authoritative hit volumes |

Capture only the variables that resolve the question: frame/writer/phase, camera/weapon/sight transforms, action ID, muzzle/ray, ammo and mechanical state. Measures include screen-pixel sight error, frame-to-frame drift, root displacement, transaction/effect count, transition discontinuity and solve time.

Verification progresses through static pose → action transitions → movement/stance → walls/terrain → network → death/respawn. Use narrow/wide screens and representative 30/60/high frame rates when relevant. Studio supports multiple clients and device simulation [R10]; network simulation can vary each traffic direction [R11]. Use MicroProfiler when stutter or growing frame cost is the symptom [R12].

A useful close-out is a before/after clip, exact reproduction, causal change, relevant measurements and remaining limitations. Tiny grip error alone is not visual acceptance.

## Video evidence

Selected frames, on-screen code, diagrams and live-caption excerpts were reviewed in the in-app browser on 2026-10-28. This was **sampled visual review, not uninterrupted viewing or a full transcript review**. Transcript panels did not load. Computer audio was unavailable; sound judgments remain unassessed. Linked times locate observed samples, not continuous reviewed intervals.

### Destiny first-person animation — David Helsby / GDC

[Video](https://www.youtube.com/watch?v=CXFLu8cityA)

**Observed:** At [15:25](https://www.youtube.com/watch?v=CXFLu8cityA&t=925s), the framing diagram reserves peripheral areas and a clear central aim corridor. At [26:20](https://www.youtube.com/watch?v=CXFLu8cityA&t=1580s), a diagram illustrates eye compensation during head rotation. At [29:30](https://www.youtube.com/watch?v=CXFLu8cityA&t=1770s), camera translation is illustrated as especially affecting nearby objects. At [32:40](https://www.youtube.com/watch?v=CXFLu8cityA&t=1960s), a slow-motion rig demonstration shows a distinct camera element beside the animated arms. The description warns that part of the original talk is absent.

### Roblox FPS Part 1 — Ampro

[Video](https://www.youtube.com/watch?v=kFpvVTD9-jg)

**Observed:** The [12:21](https://www.youtube.com/watch?v=kFpvVTD9-jg&t=741s) code uses spring-driven mouse/movement offsets and a camera-relative viewmodel pivot. Around [17:40](https://www.youtube.com/watch?v=kFpvVTD9-jg&t=1060s), ADS interpolates toward an aim-part relationship to the viewmodel root. The displayed interpolation uses a constant per-frame fraction. The 18:40 sample shows the first-person weapon presentation. This is a simple tutorial demonstration; sampled frames do not establish support for No Dawn's full body, clothing, prone or lean.

### Roblox FPS Part 2 — Ampro

[Video](https://www.youtube.com/watch?v=lLxCUNRdiDw)

**Observed:** At [5:16](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=316s), separate gun/recoil springs and firing parameters are visible. At [11:14](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=674s), camera recoil and viewmodel motion are composed separately. At [16:00](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=960s), bullet/flash/impact rendering is a distinct function. At [21:50](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=1310s), the displayed server code counts suspicious origin/hit checks and accepts a count below two: one failed check can pass. This critiques the shown revision, not every version of the author's framework.

### DayZ official weapon audio comparison

[Video](https://www.youtube.com/watch?v=EAJLaW5KDc0)

**Observed visually:** Side-by-side 1.28/1.29 comparisons show the FX-45 outdoors at [1:30](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=90s), AUR AX outdoors at [5:00](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=300s), and VIKHR inside a hangar at [8:55](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=535s). This establishes comparison framing and environments. I did **not** hear the audio and cannot judge timbre, loudness, reverberation or distant recognition.

**Proposed applications:** reserve clear target space when judging poses; test camera translation separately from rotation; audit root-relative ADS and smoothing at several frame rates; require every mandatory shot check to pass; use controlled indoor/outdoor comparisons with our owned audio and actual listening. None establishes the cause of No Dawn's current issue.

Full sample-position logs and original wording remain in Records/Archive - Research.

## Sources and access limits

Source IDs below retain the original register. “Read” refers to relevant available text. Publication-specific examples are references, not implementation proof for No Dawn. Platform details should be rechecked when implementation begins.

### Roblox technical sources

| ID | Source | Evidence reviewed and use |
|---|---|---|
| R1 | [RunService API](https://create.roblox.com/docs/reference/engine/classes/RunService) | Read relevant render binding and animation/simulation sections; ordering reference |
| R2 | [Motor6D official documentation source](https://raw.githubusercontent.com/Roblox/creator-docs/main/content/en-us/reference/engine/classes/Motor6D.yaml) | Read Transform timing and replication tags; especially useful because rendered API page exposed less explanatory text |
| R3 | [Camera API](https://create.roblox.com/docs/reference/engine/classes/Camera) | Read API properties; near-plane and camera coordinate reference |
| R4 | [IKControl API](https://create.roblox.com/docs/reference/engine/classes/IKControl) | Retrieved chain/target setup requirements |
| R5 | [Inverse kinematics overview](https://create.roblox.com/docs/animation/inverse-kinematics) | Retrieved overview; procedural pose context |
| R6 | [Client-server boundary](https://create.roblox.com/docs/scripting/security/client-server-boundary) | Read validation guidance; authority reference |
| R7 | [Raycasting](https://create.roblox.com/docs/workspace/raycasting) | Read direction/range, filtering and streaming caveat |
| R8 | [Hit detection with lasers](https://create.roblox.com/docs/tutorials/use-case-tutorials/scripting/intermediate-scripting/hit-detection-with-lasers) | Read camera target, weapon ray and server validation example |
| R9 | [Introducing shapecasts](https://devforum.roblox.com/t/introducing-shapecasts/2320655) | Roblox announcement retrieved; historical behavior needs current API verification |
| R10 | [Studio testing modes](https://create.roblox.com/docs/studio/testing-modes) | Read multi-client/device testing guidance |
| R11 | [Network Simulator](https://create.roblox.com/docs/studio/network-simulator) | Read independent directional impairments and repeatable comparison workflow; feature availability may vary with Studio version/beta settings |
| R12 | [MicroProfiler walkthrough](https://create.roblox.com/docs/performance-optimization/microprofiler/use-microprofiler) | Read diagnostic walkthrough; investigate frame spikes rather than guessing |
| R13 | [Acoustic simulation release](https://devforum.roblox.com/t/full-release-acoustic-simulation-emit-audio-with-presence/4307121) | Read announcement with September 22, 2026 update and quality caveat |
| R14 | [AudioListener API](https://create.roblox.com/docs/reference/engine/classes/AudioListener) | Retrieved acoustic simulation property requirements |

### Developer and manufacturer sources

| ID | Source | Evidence reviewed and use |
|---|---|---|
| D1 | [Bungie weekly update, March 26, 2015](https://www.bungie.net/7/en/News/article/12707/undefined) | Search-indexed developer text retrieved; direct page fetch timed out. Limited first-person animation framing only |
| D2 | [GDC: The Art of First Person Animation for Destiny](https://www.gdcvault.com/play/1022298/The-Art-of-First-Person) | Session abstract and speaker identity reviewed; full talk not watched |
| D3 | [Valve: The AI Systems of Left 4 Dead](https://steamcdn-a.akamaihd.net/apps/valve/2009/ai_systems_of_l4d_mike_booth.pdf) | Official slide text retrieved; pacing section inspected, especially slides 77–91 |
| D4 | [DayZ update 1.29](https://dayz.com/article/game-update/dayz-update-129) | Official article retrieved; distant identification and audio consistency |
| D5 | [Project Zomboid B42 feature overview](https://projectzomboid.com/blog/upcoming-features-b42/) | Retrieved adaptive-music discussion; historical feature page, not current build-status authority |
| D6 | [Project Zomboid: Reinforcing the Barricades](https://projectzomboid.com/blog/news/2026/05/reinforcing-the-barricades/) | Official post retrieved; magazine/item/condition synchronization bugs |
| D7 | [Colt 1911 Classic](https://www.colt.com/detail-page/classic/) | Product page retrieved; accessible image description supplies 7-round .45 and 5-inch barrel reference. Modern variant, not a complete military-pattern reference |
| D8 | [Mossberg pump-action manual](https://resources.mossberg.com/hubfs/manuals/103855-Pump-Action-Firearms-002.pdf) | Manufacturer manual excerpt retrieved on model/shell-dependent capacity. Does not identify No Dawn's existing pump |

The Colt page's linked manual returned 404 during this pass. Do not cite that inaccessible manual as reviewed. The Watch_Dogs “In Your Hands” slides were discovered but the PDF exceeded the browser fetch limit; no detailed implementation claims are based on them. Several third-party guide/wikis appeared in searches; they were excluded from technical authority.



## Archive and maintenance

Consolidated from the first-person architecture, firearm state, diagnostic, source-register and video-review notes. Their full bodies remain as superseded records in Records/Archive - Research, with the same basenames so old links resolve. Maintain new findings here instead of reviving those copies.

Update Grounded Survival Improvements for other feature candidates. Keep tasks in Open Items, requirements in the existing specs, and actual implementation evidence in the existing records. Do not promote a research proposal into a design ruling during note cleanup.
