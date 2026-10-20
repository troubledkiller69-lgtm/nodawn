# NO DAWN — MASTER PROJECT CONTEXT & HANDOFF

> **Context snapshot, written before Phase 2.** §11's Phase 1 status is stale: it records 125/125 tests, 14/6 acceptance and 7,377 parts, all superseded by the later passes (137/137, 15/5/0, 10,693 parts, 59 NPCs — see [[No Dawn - Home]]). Everything else — raid direction, infected, factions, combat philosophy — is current intent, not implemented state.

## 1. Project Overview

You are helping develop **No Dawn**, a serious first-person multiplayer zombie survival extraction shooter on Roblox.

The game's design draws inspiration from:
- Project Zomboid — survival simulation, encumbrance, scavenging, skills, and world danger.
- The Walking Dead — grounded post-apocalyptic atmosphere, human conflict, and the consequences of death.
- Dying Light — dangerous nighttime infected and intense pursuit.
- State of Decay — scavenging, settlements, resources, and long-term survival.

These are inspirations, not assets or designs to copy directly. No Dawn needs its own identity, lore, maps, infected designs, models, and audio.

The goal is an ambitious, polished survival game with meaningful risk, realistic weapon handling, persistent progression, and a rewarding extraction loop. It must not feel like a generic zombie-wave shooter.

Be direct and critical. Do not agree with ideas simply because I suggested them. Identify bad design decisions, unnecessary complexity, exploit risks, performance problems, and unrealistic implementation plans.

## 2. Technical Environment and Development Rules

The project is being developed in Roblox Studio, with Claude previously used for implementation and testing. The workflow now involves handing work to DeepSeek.

The existing project contains implemented systems and architectural decisions documented in the Phase 1 handoff and test documents. Those documents are your primary source of truth for the actual codebase.

Rules:
- Inspect the existing project before modifying it.
- Reuse existing services, modules, remotes, schemas, and conventions.
- Do not build duplicate systems because you did not locate the original ones.
- Do not rewrite unrelated working systems without a concrete reason.
- Do not assume a feature works just because code exists.
- Distinguish implementation, automated tests, Studio tests, multiplayer tests, and published-server verification.
- Preserve persistent data and existing player profiles.
- Keep server authority over important gameplay and inventory decisions.
- Use modular architecture where it provides real value, not unnecessary abstraction.
- Do not silently change established design decisions.
- Report blockers honestly and never claim tests passed unless they were actually run.

Use Roblox-compatible assets and original content. Do not substitute placeholders for finished assets without explicitly identifying them as placeholders.

## 3. Game Structure

### Main hub

The main safe hub is a fortified prison settlement. Players spawn in the central prison yard.

The hub should eventually contain:
- Character creation and survivor services.
- Personal vaults.
- Weapon, equipment, and supply vendors.
- Medical facilities.
- Workshops and crafting stations.
- Faction-related services.
- A guarded deployment gate leading to raids.
- Social spaces and environmental details that make the settlement feel inhabited.

The prison yard is the central gathering area. The hub should be built in stages: functional greyboxing first, then polish the spawn and key service areas before expanding decorative detail everywhere.

The Phase 1 implementation already contains a substantial hub. Do not rebuild it from scratch.

### Raids

Raids take place in separate maps. Players deploy from the safe hub, scavenge, complete objectives, encounter infected and potentially hostile players, and choose when and where to extract.

Raids should be open-ended rather than ending because of an arbitrary short countdown. Extraction locations can have different risks, requirements, and rewards.

The intended launch direction includes:
- A target of approximately 20–24 players per raid, subject to performance testing.
- Squads of up to four players.
- Solo players and incomplete squads.
- Matchmaking that prefers comparable squad sizes but relaxes when necessary.
- Shared PC and console play where platform restrictions allow it.
- Mixed PvP and PvE, with some lower-risk routes for players who want safer progression.
- Higher-risk areas with better potential rewards.

Potential raid activities include contracts, scavenging, rescue opportunities, supply drops, faction tasks, and high-risk locations. Prioritize a functional core loop before adding a large number of dynamic events.

## 4. Death, Loot, and Persistent Progression

Death must have meaningful consequences.

On death, players lose their equipped gear, carried equipment, and unsecured raid loot according to the inventory rules. Other players may be able to loot eligible items.

Protected progression includes:
- Personal vault contents.
- Eligible secure-container contents.
- Persistent skills and unlocks.
- Blueprints and permanent progression.
- Faction progression.

The secure container should be small enough that players must make real decisions about what they protect.

Players who die should have a recovery path, but not a free replacement loadout. The intended fallback is basic clothing and a knife, with essential recovery supplies available through basic vendors. Do not automatically give dead players a free gun, armor, backpack, and large ammunition supply.

Disconnecting during a raid must not be an easy method of avoiding death or losing equipment. The intended policy is that abandoning a raid counts as death, with any reconnect grace behavior designed to prevent exploitation.

Direct player trading takes place in the protected hub, requires mutual confirmation, and must be validated and committed atomically by the server. Changes to a trade should invalidate prior confirmations.

## 5. Infected and World Threat

The infected system should support a dynamic threat model inspired by survival simulations.

Threat increases around valuable locations such as:
- Police and military facilities.
- Hospitals and clinics.
- Warehouses and supply depots.
- Commercial areas.
- Quarantine or research locations.

The world should respond to noise and activity. Gunfire can attract nearby infected, trigger investigations, and contribute to larger horde movement. Suppressors reduce sound but do not eliminate it.

Daytime should generally provide better visibility and lower risk. Nighttime should be substantially more dangerous and offer reasons to take the risk, without forcing every player to participate in night raids.

Infected behavior should eventually include believable investigation, searching, pursuit, and loss of target rather than magical knowledge of player locations.

### Infected types

The game should have original infected archetypes inspired by the gameplay roles of other survival horror games:
- Standard infected.
- Fast pursuers.
- Heavy infected capable of threatening barricades.
- Contamination or area-denial variants.
- Rare, particularly dangerous nighttime predators.

These are design directions, not permission to copy another game's exact creature designs, names, animations, lore, or models. Final archetypes and mechanics should be deliberately designed and balanced.

### Reanimation and infection

The established design direction is that almost any death can result in reanimation unless the brain is destroyed.

Bites cause a progressive infection during a raid. Antizin is a rare suppressant that temporarily halts progression rather than curing it. When its effect expires, progression resumes. Successful extraction clears the bite infection.

These systems should be implemented only within their intended scope and should not be allowed to corrupt player persistence or create arbitrary offline penalties.

## 6. Combat and Weapon Design Philosophy

Combat should be grounded, tactical, and dangerous.

Firearms should have meaningful differences in:
- Handling and recoil.
- Rate of fire.
- Reloading behavior.
- Ammunition and magazine capacity.
- Effective range.
- Noise.
- Weight and encumbrance.
- Condition and reliability.
- Attachment compatibility.

Ammunition should be a meaningful resource. Firearms should be powerful but not the only viable survival strategy.

Melee should be useful for dealing with isolated infected quietly, but crowds should remain dangerous. Stamina, positioning, timing, and weapon type should matter.

Use-based weapon skills should provide modest, useful improvements without making high-level characters overwhelmingly stronger. Skill progression persists through death.

Weapon condition and malfunctions may exist, but failures should occur for understandable reasons and primarily at meaningful low-condition thresholds. Do not make weapons randomly unreliable merely to create frustration.

### Weapons and attachments

The intended approach is real-world firearm identity and technically plausible equipment where appropriate. Research actual firearms and attachment interfaces instead of inventing arbitrary combinations.

The weapon system must integrate with the existing inventory, item catalog, equipment, and persistence architecture. It must not create an independent competing inventory.

Server authority is mandatory for ammunition, firing limits, reload validity, hit validation, and damage decisions.

Weapon noise should expose a reusable event interface for the future infected AI. Do not tightly couple the firearm implementation to an unfinished infected system.

A separate weapon-specific implementation prompt will define the initial firearm and its detailed requirements. Do not expand into a full arsenal before one prototype is complete and tested.

## 7. Inventory, Loot, and Economy

The intended inventory combines a grid system with weight and encumbrance.

Items occupy grid space. Backpacks provide additional capacity but have weight and movement trade-offs. Excessive carried weight should affect movement and stamina.

Keep these concepts distinct:
- Equipped items.
- Carried raid inventory.
- Secure-container contents.
- Personal vault storage.
- Faction storage.

The economy uses ordinary currency for vendors, with barter items and faction resources providing separate progression opportunities. Better equipment should be discoverable or craftable without making basic recovery impossible.

The progression model should avoid pay-to-win advantages. Blueprints can unlock crafting, but the player should not be permanently locked out of basic viability because of unlucky loot.

## 8. Character Creation, Attributes, and Skills

Character creation is inspired by Project Zomboid's occupation and trait approach.

The six established attributes are:

- **Strength:** carrying capacity and heavy melee handling.
- **Endurance:** stamina, fatigue, and recovery.
- **Agility:** movement, vaulting, and weapon-ready speed.
- **Perception:** environmental clues and threat detection.
- **Intelligence:** crafting, repairs, and technical learning.
- **Composure:** aiming stability under pressure and flinch resistance.

Attributes should provide modest bonuses and practical thresholds. Essential mechanics should not become unusable because a character has low attributes.

Occupations represent pre-apocalypse jobs. They provide thematic expertise, pros and cons, small starting items, and occupation-dependent free attribute points. Stronger occupations receive fewer free points. The original design discussed a base pool of five free points.

Potential occupations include paramedic, police officer, firefighter, mechanic, construction worker, electrician, warehouse worker, truck driver, hunter, farmer, nurse, cook, teacher, office worker, unemployed, and veteran. The final launch list may be smaller.

Do not grant powerful starting weapons or armor through occupations. Skills should improve through use with anti-farming protections and diminishing returns. Skill gains persist through death.

Character appearance should use Roblox-compatible proportions with curated appearance and clothing customization, not just a few fixed preset outfits.

## 9. Factions and Long-Term Progression

Factions are persistent, player-created groups.

The intended direction includes:
- Recruitment and ranks.
- Permission-controlled shared resources.
- Shared progression.
- Private persistent faction bases first.
- Base upgrades, defenses, and crafting facilities.
- Shared-world territorial gameplay later.

Personal vaults and personal currency must remain protected from faction leaders. Faction storage should be separate, and contributions should be voluntary. Important shared-resource actions should be validated and logged.

Do not prioritize advanced territory warfare before the core raid, loot, extraction, and persistence loop is stable.

## 10. Survival, Injuries, and Psychological Systems

Injuries should have understandable consequences and treatments. Bleeding can be treated with bandages; serious injuries require better supplies. Temporary movement and stamina penalties are preferable to a sprawling permanent-injury simulation at launch.

Stress should respond to danger, injury, gunfire, death of teammates, and horde encounters. Effects should be modest and predictable, such as aim stability or stamina recovery, rather than random weapon firing or loss of control.

Boredom should develop slowly and have minor consequences. The game should not punish cautious players simply for surviving quietly.

A hidden occupation concept called The Sadist was discussed: killing infected can temporarily relieve stress and boredom, with diminishing returns during a raid and no direct combat bonus. This concept is not a reason to expand the current implementation scope unless specifically requested.

## 11. Phase 1 — Current Status

Phase 1 has been reported as completed by the previous development workflow. The actual project and its handoff/test documents remain authoritative.

The Phase 1 test report described the following results:

### Verified in the reported test environment
- Unit suite: 125/125 passing across ten modules.
- Acceptance suite: 14 passed, 6 partial, 0 failed, 0 not run.
- DataStore profile round-trip in Studio: schema v2 and survivor data persisted across a stop/rejoin, including appearance, attributes, currency, item UIDs, and grid positions.
- Session lock released after the tested session.
- Safe-zone damage protection and collision behavior passed their reported tests.
- Vendor distance checks rejected a request made 57 studs from the counter.
- Forged payloads were rejected and request bursts were rate-limited.
- The profile remained unchanged after the reported invalid requests.
- HUD and server balance matched in the reported test.
- A duplicate-system scan found one enabled server Script named Main, one client bootstrap, no duplicate module names, five designed remotes, and a Studio-only DevProbe.

### Not fully verified
- Forced real DataStore failure and protection against blank overwrites were covered by unit tests, not a forced real-store failure.
- Concurrent profile locking was unit-tested, but a real two-server contention test was not performed.
- Valid purchases and sales were not fully verified through the live UI.
- Cross-player vault/profile access needs a proper two-player test.
- Gamepad navigation was not tested.
- The published experience was not confirmed with the full multiplayer test suite.

### Remaining Phase 1 actions from the report
- Set `Lighting.Technology` to `Future` manually if still necessary.
- Save and publish the current place.
- Review two third-party Studio plugins reported as potentially injecting GUI scripts. Verify their purpose and provenance before deciding whether to remove them.
- Perform a live purchase and sale through the UI.
- Test gamepad navigation.
- Test the published place with two clients, including session-lock contention and cross-player access.

The hub was reported to contain approximately 7,377 parts, 81 lights, and ten catalog-dressed NPCs. A live vendor UI verification was still pending.

The report also identified future hub work such as NPC routines, ambient sound, wildlife, day/night, hospital beds, workbenches, generators, pallet stacks, and dumpster props. These are polish and world-development tasks, not proof that Phase 1's core systems are missing.

The previous todo file was reported to be stale. Do not trust it over the actual project state and current handoff documents.

## 12. Recommended Development Priorities

Before substantial expansion, verify the remaining critical Phase 1 gaps. Do not blindly repeat all 125 unit tests if they are already passing, but do inspect the test suite and rerun relevant checks after changes.

After the core persistence and economy risks are understood, the intended high-level progression is:

1. Complete outstanding critical persistence, vendor, and multiplayer verification.
2. Greybox a raid map.
3. Implement deployment and raid lifecycle.
4. Implement scavenging and loot generation.
5. Implement extraction points and extraction validation.
6. Implement death, loot loss, and recovery behavior.
7. Develop infected AI and the integrated combat loop.
8. Expand world systems, content, and polish once the core loop works.

This is a suggested order, not permission to ignore a specific task I assign next. If I request a particular system, inspect its dependencies and explain any blockers before changing priorities.

## 13. Development and Reporting Expectations

Work in small, verifiable milestones. Prefer finishing one complete vertical slice over creating many incomplete systems.

For each milestone, report:
- What was inspected.
- What was implemented or changed.
- Which existing files and services were modified.
- What tests were run and their actual results.
- What was tested in Studio versus only in automated tests.
- What remains unverified.
- Any manual actions I must perform.
- Any known bugs or architectural risks.

For visual assets, show genuine screenshots of the actual work at intermediate stages. Do not present concept art as a finished in-game model or claim a feature works because the code exists.

Do not ask me to repeat decisions already documented here or in the Phase 1 documents. When a requirement is genuinely unresolved, ask one focused question rather than sending a long questionnaire.

## 14. First Actions for the Incoming Developer

1. Read the Phase 1 handoff document and Phase 1 test document in full.
2. Inspect the actual Roblox Studio project and relevant scripts.
3. Reconcile the documents with the current project state; identify any discrepancies.
4. Produce a concise status report covering architecture, confirmed functionality, outstanding verification, and risks.
5. Do not rebuild Phase 1.
6. Wait for or follow the separately provided task specification for the next implementation assignment.

**Final principle:** No Dawn should prioritize a coherent, persistent, exploit-resistant survival game over feature count. Build from the existing codebase, preserve established decisions, test real behavior, and be honest about what is actually finished.