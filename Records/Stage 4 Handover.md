# Stage 4 — Vehicles at real scale (side session, 2026-10-18)

> **Superseded, 2026-10-18.** The Stage 4 work described here is complete and gated. Most of the captures it lists as outstanding have since been taken — the wreck and a wide field shot are still missing. Live list: [[Open Items]].

Status: built, gated and rebuilt in the open place; screenshots NOT taken yet (Studio's viewport renders blank
white since the usage-limit break — window minimized or screen locked). Full record: tasks/realism-v2-ledger.md
"Stage 4"; tracker: tasks/todo.md Stage 4; lessons added (long generations, prompt limit, white captures).

What changed (live): HubBuilder.Kit (+ isWheel / leanOnto / flatTyre, Kit.busAsset, Kit.pickupAsset,
Kit.sedanAsset opts.settle, Kit.bus fallback sign -> blank board), Sections.FrontField, Sections.Yard.
Backups: DevTools.SectionsBackup_20261018, KitBackup_20261018 (rollback = copy back + rebuild).
Drafts left for reference: DevTools.KitDraft, DevTools.SectionsDraft (identical to live; safe to delete).

- Bus: HubAssets.Props.PrisonBus (generated flat-fronted coach) scaled 1.195 and stretched 2.02 -> 36 x 8.7 x 10,
  welded plates over the lower sides, bars over the windows, razor wire on the roof, flat front-right tyre.
  Moved to (84, 102.6) east-west (nose west), in line with the east jersey barrier.
- Pickup: HubAssets.Props.PickupGreen, 17.6 x 7.2 x 6.4, cargo in the bed, at (47, 36) nose to the field gate.
- Wreck: SedanBlue via Kit.sedanAsset (hood up, front-right wheel off and lying flat, settled on the disc) at
  (15.5, 89.5).
- Gates: Yard / FrontField "shared rng: unchanged", gameplay unchanged; WestWing PASS. Hub 10,693 parts, 109 lights,
  0 text, tags unchanged. Clearance probe 0; paths OK.

To finish: with Studio in front, capture the bus (road side + the flat tyre), the pickup from the plaza, the wreck,
and a wide field shot; check the stretched bus sides and the plates read right, then send them to the user.
