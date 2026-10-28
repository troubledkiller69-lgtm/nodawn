# Reference - Video Review - First Person and Guns

> **Superseded, 2026-10-28.** Consolidated into [[Reference - First-Person and Gun Research]]. Historical research retained verbatim below; this copy is not maintained.

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]] · Sources: [[Reference - Combat Research Sources and Videos]]

Reviewed 2026-10-28 after the user requested direct video review.

## Scope and evidence

The in-app browser could play the videos even though the web text fetcher could not. I inspected selected timestamped frames, on-screen code, diagrams and short live-caption excerpts. This is **a sampled visual review, not a complete uninterrupted viewing or full transcript review**. The transcript panels remained on a loading spinner. Computer audio was not available as an input to this review; the players were muted. Sound quality and sonic differences remain unassessed.

Times below are observed playback positions or navigation anchors. A nearby link helps locate the material; it does not claim that every second between samples was reviewed. No game code was changed.

## 1. Destiny first-person animation — David Helsby / GDC

[Video](https://www.youtube.com/watch?v=CXFLu8cityA)

**Observed:** At [15:25](https://www.youtube.com/watch?v=CXFLu8cityA&t=925s), the framing diagram reserves peripheral areas and a clear central aim corridor. At [26:20](https://www.youtube.com/watch?v=CXFLu8cityA&t=1580s), a diagram illustrates eye compensation during head rotation. At [29:30](https://www.youtube.com/watch?v=CXFLu8cityA&t=1770s), camera translation is illustrated as especially affecting nearby objects. At [32:40](https://www.youtube.com/watch?v=CXFLu8cityA&t=1960s), a slow-motion rig demonstration shows a distinct camera element beside the animated arms. The description warns that part of the original talk is absent.

**No Dawn proposal:** build an on-screen framing overlay for development only. Compare ready, ADS and reload poses against target visibility, shoulder continuity and sight readability. Test camera translation and rotation independently. Do not infer from the rig screenshot that Destiny's production code can be reproduced with one camera attachment.

Additional inspected samples: 14:37 title; 16:40 attention illustration; 18:10 outlined pose; 24:20/25:00 camera section; 27:50 balance illustration; 30:50/31:40 boxing reference; 37:30 weapon-animation-set demonstration.

## 2. Roblox FPS Part 1 — Ampro

[Video](https://www.youtube.com/watch?v=kFpvVTD9-jg)

**Observed:** The [12:21](https://www.youtube.com/watch?v=kFpvVTD9-jg&t=741s) code uses spring-driven mouse/movement offsets and a camera-relative viewmodel pivot. Around [17:40](https://www.youtube.com/watch?v=kFpvVTD9-jg&t=1060s), ADS interpolates toward an aim-part relationship to the viewmodel root. The displayed interpolation uses a constant per-frame fraction. The 18:40 sample shows the first-person weapon presentation. This is a simple tutorial demonstration; sampled frames do not establish support for No Dawn's full body, clothing, prone or lean.

**No Dawn proposal:** compare the existing rig's sight reference, root and composition order with a frozen-pose baseline. Calculate the ADS transform from explicit references rather than accumulating guessed offsets. If a fixed per-frame interpolation is present in our code, compare transition duration at 30, 60 and high frame rates; use elapsed-time-based smoothing if the duration differs.

Additional inspected samples: 4:15 setup code, 8:50 first-person example, 12:15 bobbing code, 15:30 input handling, 17:30 aiming code.

## 3. Roblox FPS Part 2 — Ampro

[Video](https://www.youtube.com/watch?v=lLxCUNRdiDw)

**Observed:** At [5:16](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=316s), separate gun/recoil springs and firing parameters are visible. At [11:14](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=674s), camera recoil and viewmodel motion are composed separately. At [16:00](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=960s), bullet/flash/impact rendering is a distinct function. At [21:50](https://www.youtube.com/watch?v=lLxCUNRdiDw&t=1310s), the displayed server code counts suspicious origin/hit checks and accepts a count below two: one failed check can pass. This critiques the shown revision, not every version of the author's framework.

**No Dawn proposal:** borrow the separation of presentation responsibilities, then independently specify authority. Required checks must each pass; a “suspicion score” is not a substitute for a valid shot contract. Investigate the current No Dawn service before assuming it shares the tutorial's issue. Audit shot identity, equipped weapon, ammo, action state, fire cadence, origin, obstruction and target validation together.

The displayed code also uses older filtering terminology. Use current official APIs when implementing a principle. The final sample at about 24:14 shows the tutorial closing in Studio, not evidence of a completed production acceptance pass.

## 4. DayZ official weapon audio comparison

[Video](https://www.youtube.com/watch?v=EAJLaW5KDc0)

**Observed visually:** Side-by-side 1.28/1.29 comparisons show the FX-45 outdoors at [1:30](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=90s), AUR AX outdoors at [5:00](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=300s), and VIKHR inside a hangar at [8:55](https://www.youtube.com/watch?v=EAJLaW5KDc0&t=535s). This establishes comparison framing and environments. I did **not** hear the audio and cannot judge timbre, loudness, reverberation or distant recognition.

**No Dawn proposal:** reproduce the comparison method with our own licensed audio: same gun, same action, same listener location and an indoor/outdoor pair. Keep recording levels and environmental conditions fixed. Evaluate shot onset, mechanical timing, tail, obstruction and whether distant identity survives the mix. Actual listening is still required.

## What changes in the research recommendation

1. **Add visual acceptance to mathematical checks.** Measure occupied screen space and compare the same short motion sequence, not only grip closure.
2. **Audit transform composition before retuning values.** Freeze recoil, sway and animation separately, then re-enable them one at a time.
3. **Make camera comfort an explicit design choice.** Head animation does not automatically define a comfortable viewer rotation.
4. **Check smoothing across frame rates.** A visually pleasing single-machine tutorial does not establish consistent transition timing.
5. **Evaluate tutorial validation as code.** A successful-looking demonstration is insufficient evidence for multiplayer correctness.
6. **Treat audio review as unfinished.** The DayZ comparison is a useful controlled-reference format; sound conclusions still depend on listening.

These are proposed experiments for the existing architecture, not a new implementation checklist or a conclusion that any one issue causes No Dawn's current defect.
