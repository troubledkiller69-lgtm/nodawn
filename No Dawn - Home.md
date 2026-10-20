---
tags: [nodawn, home]
updated: 2026-10-20
---

# No Dawn — Home

**No Dawn** is a Roblox survival extraction shooter. The hub is a fortified prison modelled on the West Georgia prison from *The Walking Dead*, permanently foggy and rainy. Phase 1 (done) is the hub and everything a player keeps between raids: a persistent profile, survivor creation, a grid inventory, a personal vault, Chits and two vendors. Phase 2 adds raids, run as separate places.

- Place: `placeholder`, PlaceId **119649394550657**, UniverseId **10771286228**
- Scale: 1 stud ≈ 0.28 m
- Source of truth: the place itself. There is no git repo. This vault is the record.

## Status at a glance (2026-10-18)

| Area | State |
|---|---|
| Phase 1 systems (A–G) | Done. Unit tests 137/137. Acceptance 15 passed, 5 partial, 0 failed |
| Hub | 10,693 parts, 109 lights, 0 text, 59 NPCs. Realism v2, v3 and Stage 4 vehicles done |
| Stage 4 | Bus, pickup and wreck built at real scale; gates and paths verified. Captures outstanding |
| Blocking | The place is not saved or published — the latest work lives only in the open Studio session |
| Next | Stage 4 captures, Stage 5 verify, then interiors, declutter, the No Dawn UI |

## Start here

**The contract**
- [[Phase 1 Handoff]] — the original spec, verbatim
- [[Master Project Context]] — the standing brief for Phase 2: raids, infected, factions, combat philosophy
- [[Addendum - Infected and Firearms]] — special infected directions and the firearm realism rules
- [[Weapon - M1911A1 Build Spec]] — the first firearm, as an implementation task
- [[Phase 1 Scope Map]] — where each section of it landed, and where we knowingly deviated
- [[Design Rules]] — standing rules for how the hub must look
- [[Working Agreement]] — code and UI stay with Nyx; meshes, textures, animation clips and front-end visuals go to Claude through a terse work-order handoff

**The build**
- [[Project Overview]] — what exists, where it lives in the place, how to rebuild the hub
- [[Hub - How It Is Built]] — HubBuilder, Kit, the generated-prop pipeline, gates, backups
- [[Hub - Build History]] — every pass in order
- [[Phase 1 - Game Systems]] — milestones A–G, tests, acceptance results

**What's next**
- [[Open Items]] — the live list, and nothing else
- [[Brainstorm - Index]] — Phase 2 and everything after

**Reference and history**
- [[Lessons]] — Studio and MCP gotchas. Read before touching the place
- [[Realism Ledger]] — decisions, gate results, build numbers
- [[Tracker]] — frozen Phase 1 checklist
- [[Phase 1 Final Report]], [[Phase 1 Architecture]], [[Phase 1 Acceptance Tests]], [[Stage 4 Handover]], [[Living Hub Design]]

## Rules that never bend

- No text anywhere: no signs, labels, licence plates, destination boards. All 71 were removed on 2026-10-17
- No painted-on depth: gravel, rubble, bricks, cracks, grass and debris are geometry. Flat textures only for flat things
- Real scale. A bus is ~36 studs, a pickup ~17.6
- Always night. Foggy, rainy, day-for-night. No cycling
- Never hand-edit `Workspace.Hub`. Change HubBuilder and rebuild
- Saving and publishing are human actions. Scripts can't do either

## On the numbers

Older notes disagree with the current status table — acceptance tallies, unit-test counts, part budgets. The historical documents now carry a "superseded" banner. Where a live note and an old one conflict, the live note wins: this page, [[Open Items]], [[Hub - Build History]] and [[Realism Ledger]].
