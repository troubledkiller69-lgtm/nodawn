---
tags: [ nodawn, brainstorm ]
updated: 2026-10-28
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

## Design passes

- [[Reconciliation - GPT Design Pass]] — four fresh GPT notes compared to the vault: what is new, what fights standing rulings, and what survives.

## Threads

### Raids
- [[Idea - Raid Frame]] — the server is the world, the raid is your deployment. No timer, late join, loot respawns, extraction-only banking
- [[Idea - Detroit Map]] — Eastern Market and the Dequindre Cut at 1:1. The density correction, the 0.40 km² frame, the rope lines
- [[Reference - The Walking Dead Online]] — the open-server precedent: join/leave, loot respawns, late join, 50-player caps
- [[Idea - Raid Map]] — superseded by the Detroit map. The 800 × 800 highway exit, kept for the small-map reasoning
- What a run costs to enter, and what makes extraction tense rather than long.
- The profile-lock hand-off on teleport.

### Combat
- [[Reference - First-Person and Gun Research]] — web research pack: camera/body/viewmodels, firearm state, hit detection, bounded diagnosis, realism candidates and a source/video register (2026-10-28)
- [[Idea - Combat Frame]] — server-owned state, hitscan plus hit zones, noise as a system. The plan to approve before modelling a pistol
- [[Idea - Weapon Roster]] — eight firearms at launch, chosen by job. The damage spine, the melee math fix, and the post-launch six
- Weapon instance state in the inventory, and whether the schema needs a v3 migration first.

### Movement
- [[Idea - Movement Frame]] — stances, slide, lean, mantle
- [[Idea - Running Feel]] — speeds, acceleration, stride, stamina, surface and breathing. The realism detail layer
- [[Idea - Hitbox Model]] — damage reads the animated body, not the capsule. The lean fix and the stance state machine
- [[Idea - Movement and Hitbox Reconciled]] — four research passes folded into one design. Supersedes the open questions in the three notes above
- Lean and prone force the `HitboxService` decision.

### Infected
- [[Reconciliation - GPT Design Pass]] — dynamic infected scaling (4–5 per player, cap 100), lighting in the vision check, attack telegraphs.
- Density against server cost. Sound as the primary threat cue.
- Special types that change how you move, not just how much health they have.

### Survival
- [[Idea - Injury and Survival]] — procedural wounds by cause and body part, treatment, pain, fractures, hunger and thirst.
- The personal clock that replaces the server timer; the body as the story.

### Death and loss
- [[Idea - Death and Loss]] — what drops, the 2×2 secure container, and loot on the reanimated body.
- What drops, what survives, how the secure container behaves in practice.
- How a player rebuilds without being handed a loadout.

### Economy
- [[Idea - Monetisation]] — sell identity, never capability. The Commissary, the cosmetic contract, and the Dollars/Robux firewall
- [[Reconciliation - GPT Design Pass]] — 25% buyback, the price table, and the armour/repair cuts.
- Dollars in versus dollars out once raids pay. What stops inflation.
- Static vendor stock versus rotation.

### The hub as a place
- NPC routines, events, the "alive" feeling without adding more props.
- Declutter and focal hierarchy.

### Visual reference
- [[Reference - Detroit Overgrowth]] — 14 references, species and placement, staging by age, palette and fog, Roblox translation

### Progression
- [[Idea - Progression]] — account renown, not survivor power. The level 1 vs level 50 test, contracts, and the three models
- [[Reconciliation - GPT Design Pass]] — the skills fight: cut, codex, or capped sidegrades.
- Skills, blueprints, permanent unlocks. What is protected on death.
- Whether attributes grow or stay fixed at creation.

### The Sadist
- [[Reconciliation - GPT Design Pass]] — the server-side rules are adoptable almost verbatim.
- Concealed occupation with no defined effect. Stress, infected killing, what other players can observe.
- Must never leak through replication. Nothing about it goes in a client-readable field.

### Identity and UI
- The No Dawn name has no UI yet: typography, palette, diegetic framing.
- First-person HUD language for raids.

## Questions to settle before any of it

- Does Phase 2 ship one raid map or several?
- What is the session loop length — how long in the hub versus in a raid?
- Is solo extraction viable, or is the game group-first?
