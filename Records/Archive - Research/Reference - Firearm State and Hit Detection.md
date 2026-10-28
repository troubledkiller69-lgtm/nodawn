# Reference - Firearm State and Hit Detection

> **Superseded, 2026-10-28.** Consolidated into [[Reference - First-Person and Gun Research]]. Historical research retained verbatim below; this copy is not maintained.

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]] · Contract: [[Addendum - Infected and Firearms]], [[Weapon - M1911A1 Build Spec]]

## 1. The system is more than a weapon model

**Proposed responsibility chain:** input intent → allowed action → authoritative weapon state → inventory/ammunition transaction → shot/hit resolution → animation/audio presentation → reconciliation.

First person can immediately display a predicted response while the server decides whether the action is valid. A rejected request must restore consistent state and avoid permanent fake ammunition, duplicate effects or stuck actions.

Roblox requires validation of client-originated data: context/permission, structure, value ranges and request rate. For a gun, validate alive/equipped status, weapon identity, action compatibility, ammo, timing, finite vectors, plausible origin and legal targets. Client-reported hit success is insufficient evidence for damage. [Client-server boundary](https://create.roblox.com/docs/scripting/security/client-server-boundary)

## 2. Model independent state dimensions

**Proposal:** avoid one ever-growing state enum that combines everything:

- Action: ready, equipping, firing, cycling, reloading, checking, holstering.
- Mechanical: chamber contents, magazine/tube contents, slide/bolt/action position.
- Presentation: hip/ADS blend, recoil, sway, lowered fraction.
- Character restrictions: sprint, traversal, prone transition, menu, death.

The server owns consequential state. Clients render it. Use weapon instance identity and action/shot sequence IDs so delayed messages cannot complete an action on a newly equipped gun.

For the existing pistol baseline, seven in the magazine and a separate chamber are already specified by the vault. Colt's modern Classic page also describes a seven-round .45 configuration and five-inch barrel. Its modern variant is corroboration only: it does not prove every external feature of a military M1911A1. Do not copy its safety or sight details indiscriminately. [Colt Classic](https://www.colt.com/detail-page/classic/)

## 3. Ammunition must survive interrupted presentation

**Proposal:** keep logical commit points independent of whether a client animation marker fires.

| Event in the game's action | Authoritative consequence | Visual consequence |
|---|---|---|
| Fire accepted | Spend a valid live round exactly once | Shot, recoil, muzzle effect, mechanism cycle |
| Magazine removed | Apply the chosen retention/ownership policy | Magazine leaves the grip |
| Replacement seated | Commit the appropriate inventory transfer once | New magazine appears seated |
| Action completed | Mechanical state becomes ready if appropriate | Hands return and sights settle |
| Cancel, disconnect or death | Resolve already committed phases consistently | Stop action and rebuild visible state |

A client-side marker is useful for synchronization, but can be missing, delayed or repeated. Define server-side phase timing and transaction identity. Reconnection/late observation should reconstruct the current condition rather than needing the entire past animation.

An important design distinction: **physical magazine inventory** and **loose rounds refilling a magazine** are different abstractions. The vault currently describes an inventory transaction involving reserve rounds; do not silently add persistent individual magazines to “fix realism.” If proposed, cost the schema, UI, death loot and recovery changes.

## 4. Pistol and pump need different families

**Vault requirement:** pistol slide reciprocation, last-round lock, preserved chamber on tactical reload and visibly removed/reinserted magazine.

**Proposal for the pump's eventual audit:** represent tube, chamber and cycling separately; make each accepted shell insertion a discrete commit; determine whether a reload interrupt leaves the firearm ready or still needing a cycle. The support hand must follow the fore-end during its action. It should not use a pistol magazine animation with altered duration.

The exact current pump model/configuration is not identified by the combat record. Mossberg's manufacturer manual illustrates that pump capacities depend on model/configuration and shell length. It is a comparison source, not proof that No Dawn's pump is a Mossberg or has that capacity. Confirm the actual asset identity before applying specifications. [Mossberg pump-action manual](https://resources.mossberg.com/hubfs/manuals/103855-Pump-Action-Firearms-002.pdf)

## 5. Resolve the sight/shot contract explicitly

There is a documented conflict: [[Idea - Combat Frame]] favors camera-axis hit detection; the M1911 spec requires an actual aiming solution agreeing with the sights. This is a design question, not proof of today's bug.

**Proposal for evaluation:**
1. Derive an intended aim point from the view or sight reference.
2. Resolve the valid muzzle origin from the gameplay representation.
3. Check the path from that origin toward the intended point.
4. Stop at cover if obstructed, even when the camera can see past it.
5. Draw impact/tracer consistently with the accepted shot.
6. Verify any recoil/sway which changes hit direction is represented in the shot policy.

Roblox's laser tutorial demonstrates a camera-derived target followed by a weapon-to-target ray and server validation. It is an example of separating target selection from obstruction, not a complete production firearm framework. [Hit detection with lasers](https://create.roblox.com/docs/tutorials/use-case-tutorials/scripting/intermediate-scripting/hit-detection-with-lasers)

Two choices still need a deliberate ruling: whether muzzle direction strictly constrains the shot, and whether near-distance sight/bore offset is preserved or compensated. A two-ray solution alone does not implement realistic bore alignment or sight zeroing.

## 6. Collision queries and network limits

Raycast direction magnitude defines range. Filtering and CanQuery control eligible hits; streaming can mean a client lacks distant geometry. Therefore choose a server hit-validation policy that does not assume the client has a complete world. [Raycasting](https://create.roblox.com/docs/workspace/raycasting)

**Proposal:** keep physical character collision, damage hitboxes, concealment and cover as separate rules. Prone visuals with a standing damage target will feel unfair. Leaf concealment should not accidentally become bulletproof cover.

For camera/weapon clearance, consider volume queries rather than one pencil-thin ray. Shapecasts sweep shapes; the original announcement records overlap/margin limitations. Recheck current API behavior and test starting inside geometry before relying on it. [Roblox shapecasts](https://devforum.roblox.com/t/introducing-shapecasts/2320655)

Start with the existing hit model. Rewind and physical bullet trajectories add independent complexity and should not be bundled into a first-person visual fix.

## 7. Audio is both feedback and information

**Proposal:** trigger mechanical sounds from the same semantic phases that move components. Deduplicate local predicted and replicated shots by shot identity. Use a server noise event for infected and a separately mixed sound for listeners; perceived audio volume must not be the authoritative AI hearing calculation.

Reserve malfunctions for after the basic state machine is reliable. An unexplained random refusal to fire makes a broken gun system harder to diagnose and a scarce-ammunition game harder to read.
