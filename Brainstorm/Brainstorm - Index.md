---
tags: [nodawn, brainstorm]
updated: 2026-10-20
---

# Brainstorm - Index

Back to [[No Dawn - Home]] · Guardrails: [[Phase 1 Handoff]] §16, [[Design Rules]], [[Phase 1 Scope Map]]

Working space for everything after Phase 1. Nothing here is a commitment.

## How this space works

- One idea per note, listed under a thread below. Tag `#idea`.
- Frontmatter carries `status: raw | shaping | decided | dead`.
- When an idea is chosen it moves into [[Open Items]] or its own spec note, and the idea note gets `status: decided`.
- Dead ideas stay. The reason they died is worth more than the idea.

Template for a new idea:

```
---
tags: [idea]
status: raw
updated: YYYY-MM-DD
---
# Idea - <name>

**The pitch:**
**Why it fits No Dawn:**
**What it costs:**
**What it breaks:**
**Open questions:**
```

## Threads

### Raids
- [[Idea - Raid Frame]] — 24 design ceiling, 16 at launch, open-ended sessions, no boss
- [[Idea - Detroit Map]] — Eastern Market and the Dequindre Cut at 1:1. The density correction, the 0.40 km² frame, the rope lines
- [[Idea - Raid Map]] — superseded by the Detroit map. The 800 × 800 highway exit, kept for the small-map reasoning
- What a run costs to enter, and what makes extraction tense rather than long.
- The profile-lock hand-off on teleport.

### Combat
- [[Idea - Combat Frame]] — server-owned state, hitscan plus hit zones, noise as a system. The plan to approve before modelling a pistol
- Weapon instance state in the inventory, and whether the schema needs a v3 migration first.

### Movement
- [[Idea - Movement Frame]] — stances, slide, lean, mantle, and a grounded version of the DL2 grapple
- [[Idea - Running Feel]] — speeds, acceleration, stride, stamina, surface and breathing. The realism detail layer
- [[Idea - Hitbox Model]] — damage reads the animated body, not the capsule. The lean fix and the stance state machine
- [[Idea - Movement and Hitbox Reconciled]] — four research passes folded into one design. Supersedes the open questions in the three notes above
- [[Idea - Grapple]] — a swing, not a zip. The DL1/DL2 correction, the pendulum, and what the map owes it
- Lean and prone force the `HitboxService` decision; the grapple forces a map with rooftops worth reaching.

### Infected
- Density against server cost. Sound as the primary threat cue.
- Special types that change how you move, not just how much health they have.

### Death and loss
- What drops, what survives, how the secure container behaves in practice.
- How a player rebuilds without being handed a loadout.

### Economy
- Chits in versus chits out once raids pay. What stops inflation.
- Static vendor stock versus rotation.

### The hub as a place
- NPC routines, events, the "alive" feeling without adding more props.
- Declutter and focal hierarchy.

### Visual reference
- [[Reference - Detroit Overgrowth]] — 14 references, species and placement, staging by age, palette and fog, Roblox translation

### Progression
- Skills, blueprints, permanent unlocks. What is protected on death.
- Whether attributes grow or stay fixed at creation.

### The Sadist
- Concealed occupation with no defined effect. Stress, infected killing, what other players can observe.
- Must never leak through replication. Nothing about it goes in a client-readable field.

### Identity and UI
- The No Dawn name has no UI yet: typography, palette, diegetic framing.
- First-person HUD language for raids.

## Questions to settle before any of it

- Does Phase 2 ship one raid map or several?
- What is the session loop length — how long in the hub versus in a raid?
- Is solo extraction viable, or is the game group-first?
