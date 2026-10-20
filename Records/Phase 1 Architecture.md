# Phase 1 — Audit and Architecture (Milestone A)

> **Milestone A snapshot, written before implementation.** The "Status: draft for approval. No project changes have been made yet" line is historical — everything here has since been built. It also calls the place user-owned and published, which was never confirmed. Current state: [[No Dawn - Home]].

Status: draft for approval. No project changes have been made yet.

## 1. Audit of the open project (verified 2026-10-16)

Place `placeholder`, PlaceId 119649394550657, UniverseId 10771286228. It's user-owned and published.

| Area | Finding | How verified |
|---|---|---|
| Workspace | Stock Baseplate template: a 2048×16×2048 Baseplate, one neutral SpawnLocation at the origin (ForceField duration 0), and the Camera. Terrain is empty (0 cells). | Descendant census, `Terrain:CountCells()` |
| Scripts | None. There is no Script, LocalScript or ModuleScript outside Roblox core. | Scanned the full DataModel for `LuaSourceContainer` |
| Remotes / Bindables | None outside `RobloxReplicatedStorage`, which is engine-internal. | Full scan |
| ReplicatedStorage, ReplicatedFirst, ServerScriptService, ServerStorage, StarterGui, StarterPack | Empty | Census |
| StarterPlayer | Default containers only, so the engine's default PlayerModule and camera apply. `CameraMode=Classic`, `CameraMaxZoomDistance=128` (third person allowed), `LoadCharacterAppearance=true` (players spawn in their own Roblox avatar), WalkSpeed 16, JumpHeight 7.2 | Property reads |
| Players | `CharacterAutoLoads=true`, RespawnTime 3, MaxPlayers 60 | Property reads |
| Workspace settings | `StreamingEnabled=true`, Gravity 196.2, FallenPartsDestroyHeight −500 | Property reads |
| Lighting | Template defaults: Sky, Atmosphere, Bloom, DepthOfField and SunRays; ClockTime 14.5; Brightness 3 | Census |
| Persistence | No DataStore code. **Studio access to API Services is disabled.** A read-only `GetAsync` probe returned `502 … Studio access to APIs is not allowed`. | Read probe |
| HTTP | `HttpEnabled=false`. Phase 1 doesn't need it. | Property read |
| Avatar settings | The `AvatarSettings` service is present but can't be read from the MCP plugin context. The rig type will be confirmed at the first playtest. | Read attempt failed |

**Conclusions**
- This is a greenfield project. There are no existing systems to preserve, no conflicts, no duplicates and no broken scripts.
- The template Baseplate and SpawnLocation have no dependents. Milestone B replaces them with the hub ground and the yard spawn.
- Three settings conflict with the spec and will change:
  - `CameraMode` becomes first person.
  - `LoadCharacterAppearance` becomes false, because survivors use their profile appearance.
  - `CharacterAutoLoads` becomes false, so a character spawns only once its profile has loaded and a survivor exists.
- **Blocker for verifying Milestone C:** turn on *Game Settings → Security → Enable Studio Access to API Services*. Until then, persistence can only be tested against the in-memory DataStore double.

## 2. Key architectural decisions

1. **The hub and the raids are separate places.** They belong to the same experience. The hub place contains no system that can deal damage; raids (Phase 2+) are reached through TeleportService from the deployment gate. This is the strongest safe-zone guarantee available, and it keeps hub servers light. Inside the hub, the server also enforces protection itself (§8).
2. **Every authoritative mutation runs without yielding** and works on a staged copy, which is committed by a single swap. Luau runs one thread at a time, so these transactions are atomic by construction. DataStore calls, which do yield, never happen in the middle of a transaction.
3. **Each player's whole profile is one DataStore key.** Every save is therefore a consistent snapshot. If a server crashes, currency and items roll back together: nothing can be duplicated, and the worst case is losing up to one autosave interval.
4. **The session lock lives inside the DataStore record**, as a fencing token checked in `UpdateAsync`. A MemoryStore lock wouldn't work here: a server that has lost ownership must be physically unable to write, and only an in-record token guarantees that.
5. **The persistence layer is our own code, not ProfileStore.** Using our own avoids an external dependency and keeps the locking rules explicit and documented. ProfileStore is a sound, battle-tested alternative if you prefer it.
6. **Cosmetic appearance is separate from equipment.** Appearance is chosen at creation and can never be lost. Equipment (jackets, packs, weapons) consists of inventory items, which future raids can loot.
7. **The client never sends asset IDs.** Appearance assets are curated instances stored under `ReplicatedStorage.AppearanceAssets` and keyed by option ID. The client sends option IDs only.
8. **The character preview is built on the client.** A rig inside a `ViewportFrame` + `WorldModel` is assembled from those same curated instances, so it updates instantly with no server round trip.
9. **The UI is built in code** from a small native component library with one Theme module. It doesn't use Roact, React or Fusion.
10. **The client and server share the catalog and vendor config.** Clients read the modules for display; the server prices from the same modules. Clients never send prices, weights or definitions.

## 3. Module layout

```
ReplicatedStorage
  Shared/
    Config/   GameConfig, Attributes, Occupations, Items, Vendors, Appearance, Inventory, Economy
    Rules/    AttributeRules, GridRules, InventoryRules   -- pure functions: client previews, server enforces
    Util/     TableUtil, Schema (payload validator)
    Types     exported Luau types
    Net       remote names + client request helper
  AppearanceAssets/   curated Accessory/Shirt/Pants/Decal instances, keyed by option ID
  Remotes/            created by the server at boot
ServerScriptService
  Main (Script)       bootstrap: require services → init() → start()
  Services/
    DataStoreAdapter   real DataStore + in-memory double with fault injection
    ProfileRepository  load / lock / save / release, retry with backoff, migrations
    Migrations         ordered pure functions vN → vN+1
    PlayerDataService  lifecycle, autosave, dirty tracking, BindToClose, owner-only replication
    RequestGuard       rate limits, request-id dedup, payload schema validation
    RemoteRouter       binds remotes to handlers; the single entry point for client requests
    SurvivorService    creation validation, starting kit, appearance application, spawn gating
    InventoryService   move / split / merge / equip transactions
    EconomyService     vendor buy / sell
    InteractionService server-side proximity checks for vendors and the vault terminal
    SafeZoneService    zone volumes, per-character zone state, ForceField defense in depth
    DamageService      the only sanctioned damage path; refuses in safe zones
ServerStorage
  Tests/               unit + service tests (run against the DataStore double)
StarterPlayer/StarterPlayerScripts
  Main (LocalScript)   bootstrap controllers
  Controllers/  ClientState, Camera, Input, Screens (modal stack + Back), Creation, Inventory, Vendor, Interaction, HUD
  UI/           Theme, Components/ (Button, Panel, Tabs, ItemCard, GridView, AttributeRow, Dialog, Toast)
```

**Conventions**
- Modules are PascalCase: services are `XService` and controllers are `XController`.
- Config keys are camelCase.
- Item, occupation and appearance IDs are stable `snake_case` strings. An ID is never renamed; an item that's no longer wanted is retired instead.
- `--!strict` is used wherever it's practical.

## 4. Profile schema v1

```lua
-- DataStore "Profiles" (Studio uses "Profiles_Studio" so test data never touches live data), key "p_<UserId>"
Record = {
  format = 1,                                    -- record envelope format
  data = Profile,
  session = { job: string, token: string, beat: number }?,   -- lock / fencing token
  meta = { saves: number, lastSave: number },
}

Profile = {
  schemaVersion = 1,
  createdAt: number,
  survivor = nil | {
    createdAt: number,
    occupationId: string,          -- replicated to its owner only; never put on Player/Character attributes
    allocation: { [AttrId]: number },   -- free points spent (kept for audits and future respecs)
    attributes: { [AttrId]: number },   -- final validated values
    appearance: { skinTone, face, hairStyle, hairColor, shirt, pants: string },  -- option IDs
  },
  wallet = { chits: number },      -- integer; never negative
  equipped = { head?, body?, back?, melee?, sidearm?, primary?: ItemInstance },
  containers = {
    pockets:  { ItemInstance },    -- fixed grid
    backpack: { ItemInstance },    -- grid size comes from the equipped `back` item; must be empty to unequip
    vault:    { ItemInstance },
    secure:   { ItemInstance },
  },
  quarantine = { { item, reason, at } },   -- entries that failed validation on load are kept here, never deleted
  stats = {}, progression = {},            -- reserved; stays empty until those systems exist
}

ItemInstance = { uid: string, id: string, qty: number, x: number?, y: number?, r: 0|1? }
-- uid is generated by the server. Definitions live only in Config/Items; inventories store references.
```

## 5. Persistence design

- **Load and lock:** a single `UpdateAsync` call does this. If `session` is empty, belongs to this server, or is stale (no heartbeat for over 300 s), the server writes its own `{job, token, beat}`. Otherwise it returns `nil`, which cancels the write, and the result is "locked".
- **When the lock is held:** retry 5 times with increasing waits (about 45 s in total), which covers a fast rejoin while the old server is still saving. If the lock is still held, show the player a recoverable "profile in use" state with a Retry button. A blank profile is never created.
- **Transient failures:** each call is wrapped in `pcall` with 4 attempts and exponential backoff plus jitter. The server checks `GetRequestBudgetForRequestType` before writing.
- **Failed load:** no profile object exists, so nothing can be saved for that player. No character spawns, and every gameplay request returns `NotReady`. The player sees a "Retry" screen or can rejoin. Only a successful read that finds *no record* counts as a new player.
- **Newer schema:** if `schemaVersion` is greater than the server's version, the load counts as a failure, so an older server never overwrites data a newer server wrote.
- **Migrations:** pure functions applied to a deep copy at load time. The stored data stays untouched until the next successful save, so a failure partway through simply re-runs from the stored version.
- **Sanitizing on load:** unknown item IDs, invalid quantities, overlapping or out-of-bounds placements and duplicate uids are moved to `quarantine` with a reason, never silently deleted. Invalid currency is clamped, and the event is logged.
- **Saves:**
  - Each save is an `UpdateAsync` that writes only if `session.token` is still ours. If it isn't, ownership was lost: the session is frozen, the player is kicked with a clear message, and nothing is written.
  - Each mutation increments `dirtyVersion`. A save records the version it wrote into `savedVersion`, so "attempted" and "persisted" are tracked separately.
  - Autosave runs every 90 s if dirty, and also refreshes the heartbeat.
  - A critical save runs right after survivor creation.
  - A final save on leave also releases the lock.
  - `BindToClose` saves and releases every profile in parallel within the 30 s budget.
- **Duplicate rewards:** the starting kit and starting money are granted in the same transaction that sets `survivor`, and creation requires `survivor == nil`. If a crash happens before the save, the whole creation rolls back.
- **Known limitation:** after a server *hard*-crashes, its lock blocks that player for up to 300 s until it goes stale. The fencing token keeps the lock safe even if a lock is taken over early: the old server can no longer write.

## 6. Items and inventory

- **Item definition:**
  - `id`, `name`, `description`, `category`
  - `stackMax`, `size {w,h}`, `rotatable`, `weight` (kg)
  - `price`, `sellValue`, `sellable`
  - `equipSlot?`, `grid {w,h}?` (backpacks only)
  - `secureEligible`, `props` (data reserved for future systems)
- **Containers:**
  - pockets: 4×1 grid
  - secure: 2×2 grid, basic tier
  - vault: 10×16 grid
  - backpacks: Drawstring Bag 3×3, School Backpack 4×4, Hiking Pack 5×6
- **Weight:** carried weight covers everything equipped plus pockets, backpack and secure. Capacity is 20 kg + 2.5 kg per Strength point, so Strength has a real effect in Phase 1. The vault has a grid limit only. Encumbrance tiers come later.
- **Actions:**
  - `Move` handles equip and unequip too, as moves to and from `equipped:<slot>`.
  - `Split` and `Merge` handle stacks.
  - Purchases and sales go through EconomyService, which uses the same transaction engine.
- **The server checks every request for:**
  - ownership (the uid exists in the claimed source container of *this* player's profile)
  - a valid item ID, quantity and stack limit
  - grid bounds and overlap (the moving item itself is excluded)
  - rotation
  - weight
  - slot compatibility
  - backpack-must-be-empty-to-unequip
  - secure eligibility
  - vault access (any move that touches the vault requires being near the vault terminal)
- **Auto-placement** for purchases fills existing partial stacks first, then takes the first free cell, rotated if needed. If nothing fits, the whole transaction is rejected and no money is charged.
- **Nested containers aren't supported in Phase 1.** Making backpacks hold their contents only while equipped avoids nested-container complexity for now.

## 7. Occupations and attributes

**Attributes:** Strength, Endurance, Agility, Perception, Intelligence, Composure.
- Every attribute starts at a base of 3.
- Final value = base + occupation modifier + allocated points.
- Allocated points per attribute are 0 or more, and their total can't exceed the occupation's pool. Unspent points are lost, and the UI warns about this.
- Final values at creation must be between 1 and 8. The long-term cap is 10.
- Each attribute card states plainly whether it **applies now** (Strength → carry capacity) or **in raids (Phase 2)**.

**Launch occupations (initial numbers, tunable in `Config/Occupations`).** Total budget = free points + modifiers, and it falls as specialization rises.

| Occupation | Free pts | Modifiers | Starting kit (modest; no firearms, armor or ammo) |
|---|---|---|---|
| Unemployed | 12 | — | Water Bottle, Canned Beans |
| Office Worker | 11 | INT +1, STR −1 | Painkillers, Energy Bar |
| Warehouse Worker | 9 | STR +1, END +1, INT −1 | Box Cutter, Water Bottle |
| Cook | 9 | END +1, COMP +1 | Kitchen Knife, Canned Beans ×2 |
| Mechanic | 7 | INT +2 | Pipe Wrench, Duct Tape |
| Paramedic | 7 | INT +1, COMP +2 | Bandage ×2, Painkillers |
| Firefighter | 6 | STR +1, END +2 | Hatchet |
| Police Officer | 6 | PER +1, AGI +1, COMP +1 | Baton, Flashlight |
| Hunter | 6 | PER +2, AGI +1 | Hunting Knife, Matches |
| Veteran | 4 | COMP +2, PER +1, END +1 | Combat Knife, Field Dressing |
| The Sadist *(concealed)* | 8 | COMP +1 | Water Bottle, Canned Beans (the same kit as Unemployed, so the gear can't reveal it) |

Occupations never lock a player out of a mechanic. Future systems read the attributes and occupation through server APIs and only change *efficiency*.

**The Sadist:** `concealed = true`. Only its owner's own view includes it. It never appears on replicated instances. Its advanced effects aren't implemented.

## 8. Safe zone

- Zone volumes are invisible, anchored, non-colliding parts tagged `SafeZone`. In the hub, one volume covers the whole compound.
- `SafeZoneService` checks each character's server-side `HumanoidRootPart` position at 4 Hz. It doesn't rely on Touched events.
- While a character is inside a zone, the server parents an invisible `ForceField` to it as defense in depth: `TakeDamage` respects ForceFields.
- `DamageService.apply(attacker, victim, amount, kind)` is the only sanctioned way to deal damage. It refuses whenever **either** the attacker or the victim is inside a zone, which also blocks attacking out of a zone or into one. Phase 2 adds a hook for a combat tag that delays protection after recent combat.
- Character-to-character collisions are disabled through a collision group, so players can't shove or fling each other.
- If a character ends up outside the compound bounds (a glitch or exploit), the server returns it to the yard.

## 9. Remote interface

```
Remotes/
  Session    RemoteFunction   GetState | RetryLoad
  Creation   RemoteFunction   Submit {occupationId, allocation, appearance}
  Inventory  RemoteFunction   Move | Split | Merge
  Vendor     RemoteFunction   Buy {vendorId, offerId, qty, dest} | Sell {vendorId, uid, qty}
  State      RemoteEvent      server → owner only: load status, self-view snapshots, notices
```

- **Request envelope:** `{ rid: string(≤40), action: string, args: table }`.
- **Response:** `{ ok: boolean, code: string?, delta: SelfViewDelta? }`.
- **Every request passes these checks in order:**
  1. rate-limit token bucket (per player and per remote)
  2. envelope shape and size
  3. action whitelist
  4. argument schema (types, ranges, string lengths; unknown keys are rejected)
  5. `rid` dedup: the last 64 per player are kept, and a repeated rid returns the cached result without running again
  6. profile ready
  7. service rules
  8. commit
- **Error codes** are an enum, for example: `RateLimited`, `BadPayload`, `NotReady`, `Duplicate`, `NotOwned`, `NoSpace`, `Overweight`, `Insufficient`, `NotEligible`, `TooFar`, `InvalidSelection`.
- The UI changes state only when the server's response arrives.
- In Phase 1, the only profile data other players can see is the rendered appearance.

## 10. Interaction and UI

- **Vendors and the vault terminal** use ProximityPrompts, which support keyboard and gamepad natively. The server re-checks distance (≤ 12 studs from the anchor, using the server-observed position) on *every* vendor and vault request, not just when the UI opens.
- **First-person camera:** `CameraMode=LockFirstPerson`. Each open menu contains a `Modal` button, which frees the mouse.
- **Gamepad:**
  - The `Screens` controller manages a modal stack, so **B/Esc** goes back.
  - Focus uses `GuiService.SelectedObject` with explicit `NextSelection*` links and `SelectionGroup`.
  - No information is available only on hover: item details appear on select or focus, in a fixed detail panel.

## 11. Risks and open items

1. **Studio API access is disabled.** It blocks real DataStore testing and requires your action (§1).
2. **Original clothing art.** Classic shirts and pants need image assets uploaded to your Roblox account, where they're moderated.
   - The plan is to start with BodyColors skin tones, a small set of free Roblox-made faces and hair, and original shirt and pants templates that I generate and upload.
   - I'll ask before uploading anything to your account.
3. **Multi-server tests** (concurrent sessions, acceptance test 6) need a published experience and two real clients.
   - Locally I'll simulate two servers against the DataStore double.
   - Once API access is on, I'll also test two lock holders against the real store.
4. **No version control.** The scripts live inside the place file. I recommend publishing or saving after each milestone (Roblox keeps version history), and optionally moving to Rojo + git later.
5. **Custom survivor names are deferred**, because they need TextService filtering. The player's Roblox DisplayName is used for now.
6. **Gamepad verification will be partly manual.** The MCP tools can't simulate a physical controller; I'll verify focus navigation programmatically.
7. **Team Create status is unknown.** It only matters if script drafts are enabled; I'll check at the first script write.

## 12. Milestone order (one justified change)

The order is **B → C → E1 → D → E2 → F → G**.
- Milestone E is split. **E1** (item catalog, container model, transaction engine, with no UI) moves ahead of **D**. The reason is that survivor creation grants a starting kit and starting money, and both must go through the catalog and the transaction engine. Otherwise the creation path would need its own temporary inventory code.
- **E2** (inventory and vault UI) follows D.
- The hub (B) has no dependencies, so it goes first. That also leaves you time to turn on API access before C.
