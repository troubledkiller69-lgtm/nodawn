---
tags:
  - reference
  - nodawn
  - combat
  - research
updated: 2026-10-28
---
# Reference - First-Person and Gun Research

Back to [[No Dawn - Home]] · Related: [[Brainstorm - Index]], [[Combat - Weapon and Walker Pass]], [[Open Items]]

Research compiled 2026-10-28 for No Dawn, the Roblox survival extraction shooter. This pack explains the systems, identifies questions to investigate, and proposes improvements. It does not change the game or approve a replacement architecture. Existing live status remains in Open Items and the Realism Ledger.

## Read in this order

1. [[Reference - First-Person Architecture]] — body, camera, arms, sights, animation ownership and frame timing.
2. [[Reference - Firearm State and Hit Detection]] — ammunition, animation events, authoritative shots and obstruction.
3. [[Reference - Camera and Gun Diagnostic Method]] — a bounded investigation that produces evidence before another fix.
4. [[Reference - Grounded Survival Improvements]] — ranked realism opportunities, costs, dependencies and acceptance examples.
5. [[Reference - Combat Research Sources and Videos]] — source register, YouTube study queue and access limitations.

## What the existing record actually establishes

The October 26 [[Combat - Weapon and Walker Pass]] reports six isolated clothing-matched visual arm parts, preserved native body and locomotion, data-based weapon animation fallback, calibrated pistol/pump holds and infected searching. Its 72 combinations had tiny measured grip/ADS errors; the suite was 204/205 with an existing eyewear migration failure.

Those are historical reported checks, not a fresh inspection of today's place. They do not establish that silhouettes, shoulder placement, camera comfort, wall behavior, transitions or remote players look convincing. The record itself leaves reference matching, second-client replication, sustained combat and terrain contact unverified. The user's current report establishes that first person and guns still need investigation.

## Main findings

- A visible body, a first-person weapon presentation, and the body seen by other players are distinct responsibilities. Sharing gameplay state does not require identical camera-relative geometry.
- A mathematically aligned sight can still be too close, too large, badly framed, unstable or visually detached from the shoulder.
- Multiple writers and incorrect update order are plausible causes of instability. They are hypotheses until measured in No Dawn.
- Ammunition changes belong to authoritative transitions. Animation displays those transitions; it must not be the only way a transaction finishes.
- Camera aim, bore direction and muzzle obstruction need an explicit policy. Older vault notes disagree on this; the research identifies the decision without silently changing it.
- More gun types would multiply unresolved authoring and state problems. First establish a repeatable pistol reference, then adapt the existing pump.
- Realism has high-value opportunities in sound propagation, believable sensory knowledge, animation contact, physical state continuity and readable consequences.

## Evidence labels

**Documented:** directly supported by a linked primary source. **Vault record:** existing project documentation, not current runtime verification. **Proposal:** a suggested No Dawn design or workflow. **Hypothesis:** an explanation needing observation. **Video queued:** title/description/link found, but footage and transcript not reviewed.

The browser could retrieve documentation and source text, but YouTube exposed metadata or fetch errors rather than reviewable transcripts. The video queue is useful for study; this pack does not pretend that the videos were watched. Source-specific limitations are in the register.

## Research boundary

Keep permanent night, real scale (1 stud = 0.28 m), textless world, original assets and existing design rulings. No grapple. No new live task list. Proposed features below are research candidates, not shipped work, promises or revised specifications.
