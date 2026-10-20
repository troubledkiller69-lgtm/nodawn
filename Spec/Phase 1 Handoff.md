# PHASE 1 HANDOFF — ROBLOX SURVIVAL EXTRACTION SHOOTER

## ROLE AND OBJECTIVE

Act as a senior Roblox gameplay engineer, systems architect, technical designer, and UI/UX developer. Build Phase 1 of a serious, commercially viable first-person zombie survival extraction shooter using Roblox Studio and the available MCP tools.

The game draws inspiration from the grounded survival tension, scavenging, resource scarcity, atmosphere, and meaningful risk found in Project Zomboid, State of Decay, The Walking Dead, and Dying Light. The game must have its own original identity, systems, assets, terminology, and lore.

This is a persistent multiplayer survival game, not a generic zombie-wave simulator. Prioritize functional systems, clean architecture, security, data integrity, maintainability, and a polished player experience over superficial visual complexity.

**Phase 1 objective:** establish the playable foundation on which all later systems will depend. Build the fortified prison hub, survivor creation, persistent profiles, basic economy, vendors, and inventory/storage architecture. Do not attempt to build the entire game in this phase.

## 1. MANDATORY FIRST STEP: AUDIT THE EXISTING PROJECT

Before editing anything, inspect the currently open Roblox Studio project using available MCP tools.

Inspect:
- Workspace hierarchy, terrain, existing maps, spawn points, models, and assets.
- ServerScriptService, ReplicatedStorage, StarterPlayer, StarterGui, ServerStorage, and other relevant services.
- Existing Scripts, LocalScripts, ModuleScripts, RemoteEvents, RemoteFunctions, BindableEvents, and their dependencies.
- Existing UI, input handling, character systems, inventory, economy, persistence, and data schemas.
- Current DataStore access, saving/loading logic, session handling, and failure recovery.
- Existing naming conventions, architectural patterns, broken scripts, duplicate systems, and unused assets.
- Current camera behavior and any first-person restrictions already implemented.

Do not assume a feature works merely because a script or UI element exists. Verify behavior where practical and distinguish verified findings from assumptions.

Do not delete, overwrite, or replace existing systems blindly. Preserve working functionality and identify conflicts before changing anything.

Produce a concise audit and milestone plan, then begin implementation if the user has already authorized implementation. If this handoff is being used for the initial audit-only pass, stop after the audit and wait for approval.

## 2. TECHNICAL ARCHITECTURE

Use Roblox-native Luau and Roblox services. Do not introduce external frameworks or dependencies without a concrete reason.

Organize the project into clearly separated responsibilities:

- Shared configuration, constants, item definitions, and type definitions.
- Server-owned gameplay services for profiles, inventory, economy, character creation, and safe-zone validation.
- Client controllers for UI, menus, input, camera behavior, and presentation.
- Reusable UI components and shared utilities where appropriate.
- A centralized, documented remote interface with server-side validation.

Use modular ModuleScripts instead of one giant script. Keep server-authoritative state separate from client presentation. Clients may request actions; only the server validates and commits authoritative changes.

Use strict Luau typing where practical. Establish consistent naming conventions, clear module ownership, and explicit interfaces between systems.

Do not scatter arbitrary constants throughout scripts. Centralize configurable values such as item definitions, occupation data, attribute limits, vendor inventories, starting equipment, grid dimensions, and weight limits.

Never create multiple competing systems for the same responsibility.

## 3. GAME IDENTITY AND CORE CONSTRAINTS

The game is a first-person, multiplayer zombie survival extraction shooter for PC and console.

Visual direction:
- Grounded, oppressive survival-horror atmosphere.
- Blocky Roblox avatar proportions with carefully designed clothing, equipment, environments, and animations.
- Believable materials, restrained lighting, practical architecture, readable silhouettes, and purposeful environmental storytelling.
- Original designs rather than direct copies of other games.

Gameplay principles:
- Survival resources must matter.
- Gear and loot should have meaningful value.
- Player progression must persist.
- Shared multiplayer systems must be resistant to exploits and duplication.
- Essential mechanics must remain understandable and accessible.
- Do not introduce pay-to-win advantages.
- Support keyboard/mouse and gamepad navigation without making the interface dependent on mouse hover.

Maintain first-person gameplay as the intended experience. The prison hub is a protected social and preparation area, not a raid map.

## 4. THE PRISON HUB

The primary safe hub is a fortified prison settlement. Players spawn in its central prison yard.

First build a complete, navigable greybox using simple geometry. Establish correct spatial relationships and player flow before investing in detailed decoration.

Required areas:

1. **Central yard:** primary spawn, social space, orientation point, and main circulation area.
2. **Cell blocks:** recognizable prison architecture connected to the yard.
3. **Armory and supply vendors:** basic weapons, ammunition, backpacks, clothing, and essential supplies.
4. **Medical wing:** reserved for later medical systems, with appropriate environmental layout.
5. **Workshop:** reserved for later repairs and crafting.
6. **Administration and survivor services:** space for future profile-related, faction, and service interfaces.
7. **Personal vault access:** a clear, protected point for individual persistent storage.
8. **Guarded deployment gate:** a visually obvious route toward future raid deployment.
9. **Supporting circulation:** corridors, doors, barriers, checkpoints, signage, and logical connections between areas.

The hub should feel defensible and functional. Use sightlines, chokepoints, fencing, reinforced doors, guard positions, and environmental details to communicate security.

Do not build elaborate decorative interiors for every room immediately.

**Greybox and polish order:**
- Complete the overall navigable layout.
- Make the central yard readable and visually convincing.
- Polish the main spawn and one vendor area.
- Verify navigation, collision, spawn placement, and performance.
- Expand environmental polish after the layout works.

The hub must be a safe zone. Ordinary weapon damage, melee damage, and hostile player interactions must not function there. Enforce protection on the server, not merely by hiding combat controls in the client.

Prevent players from exploiting safe-zone boundaries to gain unintended combat advantages. Future raid entry and exit transitions must be designed so protection cannot be abused.

## 5. SURVIVOR CHARACTER CREATION

Build a functional survivor creation interface inspired by the depth and organization of Project Zomboid's character creator, adapted to Roblox avatars.

This is a mix-and-match customization system, not simply a selection of preset character outfits.

Required capabilities:
- Preview the survivor in a dedicated, controlled preview area.
- Rotate or otherwise inspect the preview.
- Select supported skin tones, faces, hair, and compatible appearance options.
- Select and combine clothing pieces from curated, original options.
- Display occupation information, advantages, drawbacks, and starting equipment.
- Allocate available attribute points within server-defined limits.
- Display the final attribute values and occupation effects before confirmation.
- Review starting equipment before creating the survivor.
- Confirm creation and enter the prison hub.

Use Roblox-compatible assets and avatar systems. Validate asset compatibility and ensure appearance selections cannot inject arbitrary asset IDs or unauthorized items.

The UI must communicate which selections are cosmetic and which affect gameplay.

Character creation should feel deliberate, grounded, and polished. Avoid cluttered menus, oversized animations, excessive particles, and generic AI-generated interface decoration.

### Occupation system

Players select a pre-apocalypse occupation. An occupation provides:
- Themed expertise and modest starting advantages.
- Relevant drawbacks where appropriate.
- A small amount of occupation-themed starting equipment.
- A job-dependent pool of free attribute points.
- A clear explanation of its gameplay identity.

More specialized or powerful occupations should generally receive fewer free attribute points than less specialized occupations. Balance must be explicit in configuration rather than scattered through code.

Potential occupation candidates:
- Paramedic.
- Police officer.
- Firefighter.
- Mechanic.
- Construction worker.
- Electrician.
- Warehouse worker.
- Truck driver.
- Hunter.
- Farmer.
- Nurse.
- Cook.
- Teacher.
- Office worker.
- Unemployed.
- Veteran.
- The Sadist.

Treat this as a candidate pool, not a requirement to ship every occupation immediately. Implement a coherent, balanced launch selection with a data-driven structure that allows more jobs to be added later.

Starting equipment must be modest and occupation-appropriate. Do not grant powerful free firearms, body armor, large ammunition supplies, or other equipment that undermines early survival and the economy.

Occupations must not gate access to essential core mechanics. A character who did not choose a mechanic occupation must still be able to interact with future repair systems, even if their efficiency differs.

The Sadist is a concealed-background occupation, not a publicly displayed identity or a generic combat class. Other players should not automatically learn that selection. Its eventual gameplay design is defined in the broader project specification; do not implement its advanced stress and infected-killing effects in Phase 1. Store the occupation in a way that permits future server-authoritative behavior without exposing secret information through replicated client data.

## 6. SIX-ATTRIBUTE SYSTEM

Implement these six attributes:

1. **Strength:** carrying capacity and heavy melee handling.
2. **Endurance:** stamina capacity, fatigue, and recovery.
3. **Agility:** movement, vaulting, and weapon-ready speed.
4. **Perception:** environmental clues and threat detection.
5. **Intelligence:** crafting, repairs, and technical learning.
6. **Composure:** aim stability under pressure and resistance to flinching.

For Phase 1:
- Define valid minimums, maximums, starting values, and point-allocation rules.
- Calculate occupation bonuses and starting attribute-point pools consistently.
- Clearly display the effects of each attribute.
- Validate all submitted values on the server.
- Prevent negative points, excessive allocations, invalid attributes, duplicate creation, and client-side manipulation.
- Save the final validated values to the persistent survivor profile.

Attributes will eventually use modest direct bonuses alongside practical gameplay thresholds. They must not lock essential survival mechanics behind low stats.

Do not implement the complete combat, stamina, injury, or crafting effects yet. Establish the data model and validation logic so later systems can consume the attributes consistently.

## 7. PLAYER PROFILES AND PERSISTENCE

Implement robust persistent profiles using Roblox DataStores and a documented, versioned schema.

The profile should accommodate:
- Profile schema version.
- Survivor identity and creation state.
- Appearance selections.
- Occupation and concealed occupation metadata where applicable.
- Six attributes.
- Currency balance.
- Equipped equipment.
- Carried inventory.
- Personal vault inventory.
- Secure-container contents.
- Future progression, skill, faction, and statistics fields through controlled schema evolution.

Do not implement all future progression systems now. Provide a clean extensible schema without pretending those systems already exist.

### Persistence requirements

- Validate and sanitize all data when loading.
- Apply explicit schema migrations.
- Use protected calls and bounded retry/backoff for transient DataStore failures.
- Never replace a failed or unavailable profile load with a blank default and then save over the player's existing data.
- Do not permit gameplay that could overwrite an unknown existing profile after a failed load.
- Use session-locking or an equivalent robust ownership strategy to prevent simultaneous sessions from independently mutating the same profile.
- Handle player departure, server shutdown, and saving failures safely.
- Keep server-owned in-memory profile state authoritative during the session.
- Prevent duplicate creation, duplicate rewards, duplicate transactions, and double application of inventory changes.
- Avoid saving the entire profile excessively after every small UI action. Use controlled save points and appropriate autosaving.
- Track dirty state and distinguish successful persistence from a merely attempted save.
- Ensure migrations are repeatable or otherwise safe against partial failure.
- Keep a recovery-friendly design and document any limitations of the chosen locking strategy.

If profile loading fails, prevent the player from entering state-changing gameplay until their profile is safely loaded or provide a clear recoverable failure path. Never silently treat a failed load as a brand-new player.

DataStore behavior must be tested in a published test experience with API access configured appropriately. Do not claim live persistence has been verified if only local Studio behavior was tested.

## 8. ITEM DEFINITIONS AND INVENTORY FOUNDATION

Create a centralized item catalog with stable unique IDs and explicit item definitions.

Support data fields appropriate to each item, such as:
- Display name and description.
- Item category.
- Stack limit.
- Grid dimensions.
- Weight.
- Vendor price and sell value.
- Equipment slot or usable-item metadata where applicable.
- Item-specific properties needed by future systems.

Avoid storing redundant copies of item definitions inside player inventories. Inventory entries should reference validated item IDs and store only necessary instance-specific state, such as quantity, condition, or unique instance data.

### Separate storage categories

Establish four distinct categories:

1. **Equipped inventory:** worn clothing, backpack, weapons, and other equipped gear.
2. **Carried inventory:** items physically carried during a future raid.
3. **Personal vault:** protected persistent storage accessed at the hub.
4. **Secure container:** a limited-capacity container intended to protect eligible items during a future raid death.

These categories must have explicit ownership and transfer rules.

### Hybrid grid-and-weight model

The intended inventory system combines:
- Grid-based placement, where items occupy defined dimensions.
- Weight and carrying capacity.
- Backpack capacity and tradeoffs.
- Encumbrance effects in later gameplay.

For Phase 1, implement the data structures and core validation necessary to represent grids, stacks, item sizes, weight, and capacity. Build a functional inventory UI or vertical slice sufficient to demonstrate legitimate item movement and storage transfers.

The server must validate:
- Item ownership.
- Item ID validity.
- Quantity and stack limits.
- Grid dimensions and placement.
- Overlapping item prevention.
- Container capacity and weight.
- Equipment compatibility.
- Valid source and destination.
- Transaction atomicity.
- Secure-container eligibility.

Do not allow clients to directly set their own inventory, currency, item condition, or ownership.

Inventory transfer failures must leave the original state intact. A transfer should either complete correctly or make no change. Prevent duplication and loss caused by concurrent requests, partial updates, or repeated requests.

Future encumbrance, stamina penalties, and full equipment gameplay can be integrated later. Do not build an elaborate physical inventory simulation before the core rules work.

## 9. BASIC ECONOMY AND VENDORS

Implement a simple, persistent currency balance and functional hub vendors.

Required behavior:
- Browse vendor inventory.
- Inspect item names, descriptions, prices, and relevant properties.
- Purchase items when eligible and sufficiently funded.
- Sell eligible owned items.
- Display currency before and after transactions.
- Update the UI only after server confirmation.
- Provide clear feedback for success and failure.
- Persist the resulting state safely.

Starting hub vendors should reliably provide basic recovery equipment. Players who lose a raid must have a practical way to rebuild without receiving a free high-tier loadout.

Basic supplies may include suitable clothing, a modest basic melee option, limited essential supplies, basic ammunition, and entry-level equipment as appropriate to the balanced item catalog. Exact prices and starting inventory should be centralized and configurable.

Do not grant a free firearm, body armor, large ammunition bundle, backpack, or expensive gear merely for respawning.

### Transaction security

All transactions must be validated and committed by the server.

Validate:
- Vendor stock and item availability.
- Current authoritative balance.
- Item ownership and sell eligibility.
- Stack, capacity, and weight limits.
- Correct prices and quantities.
- Repeated or simultaneous requests.
- Currency and inventory consistency.

Use atomic transaction logic so a failed purchase cannot charge currency without delivering the item, and a failed sale cannot remove an item without issuing the correct payment. Apply rate limits and appropriate request deduplication.

For Phase 1, vendors may use static stock. Rotating trader inventories, barter systems, rare raid traders, and advanced economy mechanics are out of scope.

## 10. PERSONAL VAULT AND SECURE CONTAINER

Build the foundations for protected personal storage.

The personal vault:
- Belongs exclusively to the individual player.
- Persists between sessions.
- Is accessible through the prison hub.
- Cannot be accessed by faction leaders, other players, or client-side manipulation.
- Supports validated transfers between eligible inventory categories.

The secure container:
- Has a configurable limited capacity.
- Stores only eligible items.
- Is kept separate from ordinary carried loot.
- Persists appropriately between sessions and follows the established player-data rules.

The future raid death rules are already defined:
- Players lose equipped gear, backpack contents, and unsecured raid loot on death.
- Other players may loot eligible dropped equipment and loot.
- Personal vault contents, eligible secure-container contents, blueprints, permanent unlocks, skills, and faction progression are protected according to their respective systems.

Phase 1 must establish the storage separation and server validation needed for those rules. Do not build full death drops or raid loot spawning yet.

## 11. UI/UX AND INPUT

The UI should feel like a grounded survival game's interface rather than a generic Roblox simulator.

Use:
- Clear hierarchy and readable typography.
- Restrained, practical visual effects.
- Consistent spacing and alignment.
- Informative item cards and attribute descriptions.
- Clear confirmation and error states.
- Consistent navigation between menus.
- Controller-friendly focus, selection, and back navigation.
- Mouse and keyboard support without relying on hover as the only way to reveal important information.

Build functional interfaces for:
- Survivor creation.
- Occupation selection.
- Attribute allocation.
- Character preview.
- Hub/vendor interaction.
- Basic inventory and storage interaction.
- Currency display.
- Loading, saving, and failure states where appropriate.

Do not build elaborate menus for systems that do not exist yet. Do not use decorative controls that imply nonexistent functionality.

Use the available Roblox GUI tools and services correctly. Verify text scaling, screen bounds, focus behavior, and readability on typical PC and console layouts.

## 12. SERVER AUTHORITY AND EXPLOIT PREVENTION

Treat every client request as untrusted.

The server must own:
- Player profiles.
- Currency.
- Inventory ownership and quantities.
- Purchase and sale calculations.
- Occupation and attribute validation.
- Item transfers.
- Equipment eligibility.
- Safe-zone enforcement.
- Creation and transaction state.

Use server-side validation, rate limits, reasonable payload limits, and clear remote interfaces. Do not trust client-supplied prices, weights, item definitions, attribute totals, permissions, or ownership claims.

Prevent:
- Currency injection.
- Item duplication.
- Invalid inventory transfers.
- Duplicate survivor creation.
- Unauthorized item IDs or asset IDs.
- Remote spam.
- Cross-player data access.
- Repeated transaction exploitation.
- Data overwrites after failed loading.
- Simultaneous session corruption.
- Safe-zone combat exploits.

Do not create excessive remotes or expose secret occupation metadata unnecessarily. Keep sensitive state on the server.

## 13. PERFORMANCE AND MAINTAINABILITY

The experience is intended for PC and console, with multiplayer systems that must scale responsibly.

For Phase 1:
- Keep the prison hub geometry reasonably efficient.
- Avoid unnecessary per-frame loops and uncontrolled event connections.
- Avoid excessive part counts and redundant physics.
- Reuse assets and modular code appropriately.
- Avoid large replicated data payloads.
- Keep server profile and inventory operations bounded.
- Separate UI presentation from authoritative game logic.
- Make configuration changes possible without rewriting core systems.

Do not prematurely optimize every detail, but do not establish avoidable architectural problems that will undermine later raids, infected AI, or multiplayer combat.

## 14. IMPLEMENTATION MILESTONES

Work in small, testable milestones. After each milestone, explain what changed and run the available tests before moving on.

**Milestone A — Project audit and architecture**
- Inspect existing project.
- Identify reusable systems and conflicts.
- Establish or refine module boundaries.
- Define profile, item, occupation, and attribute schemas.
- Define remote interfaces and validation responsibilities.
- Document implementation order and risks.

**Milestone B — Prison hub greybox**
- Build the navigable prison layout.
- Establish the central yard and spawn.
- Add major functional area placeholders.
- Polish the spawn and one vendor area.
- Verify collision, circulation, and safe-zone boundaries.

**Milestone C — Persistent survivor profile**
- Implement versioned profile loading and saving.
- Implement validation, migrations, session ownership, failure recovery, and safe shutdown handling.
- Verify rejoining restores the correct player profile.
- Ensure failed loads cannot overwrite existing data.

**Milestone D — Survivor creation**
- Implement appearance selection and preview.
- Implement occupations and the six attributes.
- Validate selections and point allocation on the server.
- Save the survivor and prevent duplicate creation.
- Verify rejoining restores the same survivor.

**Milestone E — Item and inventory foundation**
- Implement the item catalog and storage schemas.
- Implement grid, stack, weight, and capacity validation.
- Build functional inventory and vault transfer interactions.
- Verify transfers cannot duplicate or lose items.

**Milestone F — Currency and vendors**
- Implement vendor browsing and static stock.
- Implement secure purchases and sales.
- Integrate transactions with profile and inventory state.
- Verify balances, item quantities, and persistence.

**Milestone G — Integration and acceptance testing**
- Test hub navigation and safe-zone restrictions.
- Test creation, persistence, inventory, vault, secure container, vendors, and controller navigation.
- Fix integration defects.
- Report remaining limitations and readiness for the next phase.

Reorder a milestone only when the project audit demonstrates a concrete dependency or risk. Explain the reason.

## 15. ACCEPTANCE TESTS

Phase 1 is not complete merely because the scripts compile or the UI looks finished.

Verify these scenarios:

1. A new player can create a survivor using valid selections.
2. Invalid occupation IDs, attribute values, appearance selections, and point totals are rejected by the server.
3. A returning player loads their existing survivor rather than creating a duplicate.
4. Appearance, occupation, attributes, currency, and inventory restore correctly after rejoining.
5. A failed profile load never silently creates and saves a blank replacement.
6. Concurrent sessions cannot independently overwrite the same profile.
7. A valid purchase deducts the correct amount and grants exactly the intended item quantity.
8. An invalid or repeated purchase cannot create currency or items.
9. A valid sale removes the eligible item and grants the correct amount exactly once.
10. An invalid transaction leaves the original balance and inventory unchanged.
11. Grid placement rejects overlapping or out-of-bounds items.
12. Stack, weight, and capacity restrictions are enforced by the server.
13. Vault transfers work correctly and preserve item ownership.
14. Secure-container eligibility and capacity are validated.
15. Players cannot access or modify another player's personal vault or profile.
16. The prison hub blocks ordinary combat interactions on the server.
17. Keyboard/mouse and gamepad users can navigate the implemented interfaces.
18. Saving failures and shutdown conditions are handled without falsely claiming success.
19. The project contains no conflicting duplicate systems introduced by the implementation.
20. The experience can be published and its DataStore behavior verified in a properly configured test environment.

For each test, report whether it was passed, failed, partially verified, or not run. Distinguish local Studio tests from tests requiring a published experience or multiple clients.

## 16. STRICTLY OUT OF SCOPE

Do not implement these during Phase 1:
- Full raid maps or extraction gameplay.
- Infected AI, hordes, special infected, or dynamic threat simulation.
- Full weapon combat, recoil, ballistics, attachments, or weapon malfunctions.
- Full injury, bite infection, Antizin, stress, or boredom simulations.
- Player death drops and loot recovery in raids.
- Persistent faction bases, faction progression, or territory control.
- Player-to-player trading or a public marketplace.
- Advanced crafting, repair, and blueprint systems.
- Wandering raid traders or dynamic supply drops.
- Complete skill progression and advanced character perks.
- Large-scale dynamic events.
- Monetization or pay-to-win features.

Prepare clean interfaces for future integration without creating fake or unfinished implementations of these features.

## 17. CODE QUALITY AND DELIVERY REQUIREMENTS

- Use readable, maintainable Luau with clear module responsibilities.
- Avoid giant monolithic scripts and excessive abstraction.
- Use comments to explain non-obvious logic and important security decisions, not to narrate every line.
- Keep data schemas, configuration, and server logic consistent.
- Avoid duplicate definitions and magic values.
- Clean up temporary debugging code and unused objects.
- Do not silently remove existing functionality.
- Do not claim an API, service, or test works unless it has actually been verified.
- Do not report success based solely on code generation or the absence of visible errors.
- If an MCP action fails, report the failure and use a supported alternative rather than pretending the change happened.

After each milestone, provide:
1. Files, modules, and Roblox objects created or changed.
2. Functionality implemented.
3. Tests actually performed and their results.
4. Known defects, assumptions, and unverified behavior.
5. Remaining work and the next milestone.

## DEFINITION OF DONE

Phase 1 is complete when the prison hub is navigable, the survivor creator works, player profiles persist safely, occupations and attributes are validated, basic vendors and currency function correctly, the inventory/vault/secure-container foundations prevent duplication, the hub is server-enforced as a safe zone, and the implemented UI supports keyboard/mouse and gamepad.

The result must be a stable, extensible foundation for Phase 2—not a visually impressive prototype with unreliable data or fake functionality.

Start by auditing the existing project and following the milestone process. Preserve working systems, prioritize data integrity and server authority, and test every feature before declaring it complete.