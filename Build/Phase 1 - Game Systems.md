---
tags: [nodawn, phase1]
updated: 2026-10-21
---
# Phase 1 — Game Systems

Back to [[No Dawn - Home]] · Full detail: [[Records/Phase 1 Final Report]], [[Records/Phase 1 Architecture]],
[[Records/Phase 1 Acceptance Tests]], [[Tracker]]

Order built: A → B → C → E1 → D → E2 → F → G (E1 was moved ahead of D).

| Milestone | What | State |
|---|---|---|
| A. Audit and architecture | Module layout, profile schema, item/occupation/attribute models, remote interface, risks | Done |
| B. Prison hub greybox | Walls, towers, yard, cell blocks, supply, medical, workshop, services, vault room, sally port; SafeZone, vendor and vault anchors | Done, then rebuilt by the detail and realism passes ([[Hub - Build History]]) |
| C. Persistent profile | DataStore adapter (real store + a double with fault injection), profile repository, migrations, session locking, autosave, shutdown save | Done; save → rejoin → restore verified on the real Studio DataStore |
| E1. Items and transactions | Item catalog, grid/inventory rules, atomic transactions (move, split, merge, equip), RequestGuard + RemoteRouter (rate limits, schema, request-id dedup) | Done |
| D. Survivor creation | Occupations, attributes, appearance (curated catalog clothing on the R15 body), creation UI with a rotating preview, kit + starting Chits in one transaction | Done; mouse flow verified, gamepad wiring present but not driven |
| E2. Inventory and vault UI | Inventory (equipped, pockets, backpack, secure) and the locker terminal screen; drag and gamepad select-then-place | Done; mouse and keyboard verified |
| F. Currency and vendors | `Config.Vendors` (quartermaster + armory), `EconomyService` buy/sell over the transaction engine, `TradeRules`, `VendorScreen` (buy/sell tabs, quantities, delivery to carried or vault) | Done; 22 economy tests and live trades through the real remote (TooFar, replayed request ids, 10 concurrent buys, forged prices) |
| G. Integration and acceptance | 20 acceptance tests, duplicate-system audit, perf check, Phase 2 readiness | Done |

## Tests
- **Unit suite: 150/150 passing** (latest run 2026-10-21 with the Dollars change; 125 at the Phase 1 report, then
  WorldClock, HitZones and Money tests).
- **Acceptance: 15 passed, 5 partial, 0 failed.** Partial: #5 failed-load handling (unit only), #12 overweight
  (unit only), #15 cross-player access (one client only), #17 gamepad (never driven with a real controller),
  #20 DataStore on a published server.
- Perf: server 60 Hz. Client fps in a Studio playtest isn't representative; check on a published client
  (shadow-casting floodlights, about 5,900 shadow-casting parts).

## Ready for Phase 2
- Seams in place: DamageService (single damage gateway, zone-aware), InventoryTx (atomic transfers for loot),
  EconomyService / TradeRules, reserved profile fields (`stats`, `progression`), hub and raid as separate places.
- Teleporting to raids will need a profile-lock hand-off (hub releases, raid acquires), teleport data, and the raid's
  own DamageService rules.
