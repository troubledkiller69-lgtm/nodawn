---
tags: [nodawn, spec, infected, firearms]
updated: 2026-10-20
---

# ADDENDUM — SPECIAL INFECTED AND FIREARM REALISM

Back to [[No Dawn - Home]] · Contract: [[Master Project Context]], [[Weapon - M1911A1 Build Spec]], [[Idea - Combat Frame]]

### A. Special Infected — No Dawn

No Dawn is a grounded survival-horror extraction shooter. Special infected must be genuinely threatening, visually distinctive, and mechanically meaningful. They should feel like dangerous biological threats within the world, not colorful enemies with inflated health bars.

Our inspirations include the nighttime threat of Dying Light, the fast and lethal infected encounters of State of Decay, and the grounded survival atmosphere of Project Zomboid. These are references for gameplay roles, not designs to copy.

The intended infected directions discussed so far include:

**1. Standard infected**
- The baseline threat.
- Dangerous in groups, particularly when players make noise or become surrounded.
- Individual infected should not need excessive health to remain threatening.
- Their danger comes from numbers, positioning, injuries, and pressure.

**2. Fast pursuers / feral-type infected**
- Fast enough to pressure players who rely on sprinting or poor positioning.
- Dangerous when encountered unexpectedly or in confined areas.
- Must have readable movement, attack telegraphs, and counterplay.
- Inspired by the gameplay role of ferals in survival horror, but with original names, anatomy, animations, and behavior.

**3. Heavy infected**
- Larger, physically threatening infected capable of pressuring defensive positions and damaging or breaking barricades where appropriate.
- Slow enough to have distinct counterplay.
- Their threat should come from force and space control, not simply enormous health pools.

**4. Contamination-oriented infected**
- Potentially use spores, contaminated fluids, or area denial.
- Must create a tactical problem that changes player positioning.
- Their mechanics, appearance, and biological explanation need further design rather than being assumed finalized.

**5. Rare nighttime apex predators**
- The most dangerous infected category under consideration.
- Inspired by the tension created by Volatiles in Dying Light, not intended as a direct copy.
- Night should fundamentally change player decisions: visibility, movement, noise discipline, routes, and extraction timing.
- These creatures should be rare enough that encounters remain memorable.
- They need believable detection and pursuit rules, clear sensory cues, and opportunities for escape.
- Do not make them omniscient, arbitrarily fast, or impossible to evade.

The exact distinction between the rare nighttime apex predator and any separate Nightstalker-type category has not been conclusively settled. Do not silently create two overlapping apex classes without first explaining how their roles differ.

### Infected ecology and threat

Threat should depend on location, time, player activity, and world conditions.

- Valuable locations attract more and potentially more dangerous infected.
- Gunfire and other disturbances can trigger investigation and escalation.
- Infected should investigate sounds, search likely locations, pursue visible or otherwise detected targets, and lose targets when appropriate.
- Night should generally be more dangerous than daytime, but players should retain meaningful choices.
- The world should feel like an ecosystem rather than a sequence of scripted enemy spawns.

Any death may lead to reanimation unless the brain is destroyed. This is a core world rule. Bite infection progresses during a raid; Antizin temporarily suppresses progression rather than curing it. Successful extraction clears the bite infection.

All creature names, designs, animations, sounds, and lore must be original to No Dawn.

### B. Firearm Realism — A Core Design Requirement

We want **high visual and mechanical realism within Roblox performance and usability constraints**. Do not reduce this to realistic-looking meshes with arcade behavior.

Use real firearm names and authentic specifications where appropriate. Research real-world references rather than inventing dimensions, magazine capacities, operating behavior, or attachment compatibility.

Realism priorities:

1. Correct silhouette, proportions, controls, and recognizable components.
2. Authentic weapon-specific firing and reload behavior.
3. Correct ammunition and magazine logic.
4. Functional iron sights and properly aligned aiming.
5. Moving mechanical parts that correspond to actual weapon states.
6. Plausible recoil, handling, weapon weight, and aim transitions.
7. Physically plausible attachment interfaces and configurations.
8. Appropriate sound, including differences between suppressed and unsuppressed firing.
9. Meaningful weapon differences without arbitrary stat inflation.
10. Server-authoritative combat and ammunition logic.

### Mechanical behavior

Weapon animations must correspond to real gameplay state.

- Slides, bolts, hammers, magazines, and other visible moving components should move when appropriate.
- Reload animations must physically represent magazine removal and insertion.
- Ammunition state must agree with the visible weapon state.
- Empty-magazine behavior, chambered rounds, slide locks, and release actions must be implemented according to the specific firearm.
- Iron sights must be usable for actual aiming, not merely decorative.
- Attachment configurations must obey real compatibility constraints.
- Sounds should occur at the appropriate points in the firing and handling cycles.

Do not apply one generic animation to every weapon. Different firearm families require different mechanical behavior.

### Realism versus gameplay

Realism must improve immersion without making the game needlessly tedious.

- Ammunition scarcity should create meaningful decisions, not make firearms unusable.
- Recoil should be distinctive and controllable, not random camera abuse.
- Suppressors should reduce noise without making firearms silent.
- Weapon condition and malfunctions, if implemented, should be understandable and appropriately limited.
- Melee and stealth must remain viable alternatives to firing a weapon.
- Attachments should involve trade-offs where meaningful, not provide universal upgrades.
- Avoid arbitrary damage multipliers and exaggerated RPG-style weapon statistics.

### First firearm prototype

The initial weapon task is a classic **M1911A1 .45 ACP pistol**. It must have a detailed original model, usable iron sights, working first-person animations, visible slide reciprocation after firing, a slide-lock state when empty, correct reload behavior, plausible attachment configurations, and audio integrated from the provided local sound collection where possible.

The initial attachment plan includes a traditional iron-sight configuration, a compatible suppressor setup, and optional light or optic configurations only when the frame, slide, barrel, and mounting interfaces support them. Do not add a rail or optic to the classic configuration if the reference firearm does not have one.

Complete and test this one firearm before expanding the arsenal.

### Development rule

Do not confuse a design direction with an implemented feature. Research, document, and implement these systems in stages. Report which details are confirmed, which remain design proposals, and which are actually working in Roblox Studio.