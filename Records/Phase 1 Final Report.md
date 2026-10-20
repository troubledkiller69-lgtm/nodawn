# Phase 1 Final Report — Roblox Survival Extraction Shooter

> **Superseded snapshot, 2026-10-17.** Its acceptance tally (14 pass / 6 partial) was later revised to **15 pass / 5 partial / 0 failed**; see [[Open Items]] and [[No Dawn - Home]]. Its hub stats (7,377 parts, 81 lights, 10 NPCs) predate the detail, realism and Stage 4 passes. Kept as the record of what was believed on that date.

Place: `placeholder` (PlaceId 119649394550657) · Report date: 2026-10-17

## Where the results come from

| Label | Meaning |
|---|---|
| **Re-run today** | Run on 2026-10-17 for this report. Either the unit suite (`ServerStorage.UnitTest`, against the in-memory DataStore double with fault injection) or a live check in a Studio playtest against the **real Studio DataStore** (`storeKind=datastore`). |
| **Recorded 10-16** | Verified live in the 2026-10-16 build session and recorded in its transcript. Not re-run today. |
| **Not run** | No test exists in any record. |

All results are **local Studio**. Nothing was tested on a published server or with more than one client.

## Bottom line

- **Unit suite: 125 run, 125 passed, 0 failed** (10 case modules, 21.7 s, re-run today).
- **Acceptance tests: 14 passed, 6 partially verified, 0 failed, 0 not run at all.**
- Most of the partial ones need things a single local Studio session can't provide: a published place, a second client, or a physical gamepad. Two are live vendor trades that only have service-level tests.

## Unit suite (re-run today)

| Case module | Tests | Result |
|---|---:|---|
| Schema_Test | 6 | 6/6 pass |
| RequestGuard_Test | 6 | 6/6 pass |
| Migrations_Test | 9 | 9/9 pass |
| ProfileSchema_Test | 8 | 8/8 pass |
| ProfileRepository_Test | 12 | 12/12 pass |
| ProfileSessions_Test | 20 | 20/20 pass |
| InventoryRules_Test | 13 | 13/13 pass |
| InventoryTx_Test | 16 | 16/16 pass (includes the random-sequence no-dup/no-loss property test) |
| SurvivorCreation_Test | 13 | 13/13 pass |
| Economy_Test | 22 | 22/22 pass |
| **Total** | **125** | **125/125** |

The trackers last recorded 103/103. The difference is mainly the 22 Economy tests.

## Live checks run today (Studio playtest, real Studio DataStore)

| Check | Result |
|---|---|
| Profile load | `ready`, schema v2, 0 sanitize issues; returning Paramedic loaded straight into the hub with no creator shown |
| Leave → rejoin (stop play, which runs the shutdown save and lock release, then start play), then a full diff against the pre-leave snapshot | **Identical.** Survivor, attributes, allocation, appearance, 106 Chits, and every item uid and grid position. The lock was released cleanly (reload was immediate). |
| Damage through `DamageService.apply` (environment → player and player → player) | Refused, `VictimProtected`; health unchanged |
| Raw `Humanoid:TakeDamage(10)` | Blocked by the server ForceField; health unchanged |
| Zone bounds | Yard is safe; points outside the compound are not |
| Player collision | `Survivors` group doesn't collide with itself (no shoving or flinging) |
| Vendor remote, 57 studs from the counter | Buy → `TooFar`; Sell → `TooFar` |
| Vendor remote, forged payloads | Client-sent price, qty 0, qty 1000, qty 1.5 and an unknown vendor → `BadPayload`; unknown action → `UnknownAction` |
| 14-request burst | 8 `TooFar`, then 6 `RateLimited` |
| Profile after all 23 refused requests | Unchanged: 106 Chits, 4 stacks / 11 units, `dirtyVersion` 0 |
| HUD vs. server | HUD shows 106 Chits, matching the server profile |
| Duplicate-systems scan | 1 enabled server Script (`Main`), 1 client bootstrap, no duplicate module names, and only the 5 designed remotes plus the Studio-only `DevProbe` |

## The 20 acceptance tests

| # | Test | Status | Evidence |
|---|---|---|---|
| 1 | New player creates a survivor with valid selections | **Passed** | Unit (re-run today): valid creation commits the survivor, Chits and kit together. Recorded 10-16: full creator flow driven by real mouse clicks. |
| 2 | Invalid occupation, attribute, appearance and point totals rejected | **Passed** | Unit (re-run today): invalid submissions rejected with no changes; pool and creation cap enforced; undeclared keys rejected. Recorded 10-16: live remote validation. |
| 3 | Returning player loads their existing survivor, not a duplicate | **Passed** | Live (re-run today): rejoin loads the existing Paramedic with no creator shown. Unit: duplicate creation refused without a second grant. |
| 4 | Appearance, occupation, attributes, currency and inventory restore after rejoin | **Passed** | Live (re-run today): the post-rejoin profile is identical to the pre-leave snapshot. |
| 5 | A failed load never creates and saves a blank profile | **Passed** (unit) | Unit (re-run today): failed, corrupt, future-schema and malformed-survivor loads expose no data and write nothing; retry restores the stored profile. Failures are injected into the DataStore double; the real store was not forced to fail. |
| 6 | Concurrent sessions can't overwrite the same profile | **Partial** | Unit (re-run today): a second server is refused without writing; a lost lock freezes the session. **Not run:** two real servers (needs a published place and two clients). |
| 7 | A valid purchase deducts the exact price and grants the exact quantity | **Partial** | Unit (re-run today): exact deduction and delivery, an exact-balance buy leaving 0, and stacks past stackMax. **Not run:** no live buy through the remote and UI is recorded. |
| 8 | Invalid or repeated purchases can't create currency or items | **Passed** | Unit (re-run today): insufficient funds, rapid repeats, buy-then-sell-back never creates Chits, request-id replay. Live (re-run today): forged price, bad quantities, unknown vendor, rate limiting; profile unchanged. |
| 9 | A valid sale removes the item and credits the exact amount once | **Partial** | Unit (re-run today): exact sell value, exact removal, worn/unowned/over-quantity refused. **Not run:** no live sale is recorded. |
| 10 | An invalid transaction leaves balance and inventory unchanged | **Passed** | Unit (re-run today): full rollback, partial fits refused whole. Live (re-run today): 23 refused requests and the profile is unchanged. |
| 11 | Grid placement rejects overlap and out-of-bounds | **Passed** | Unit (re-run today): bounds, overlap, rotation. Recorded 10-16: locker placement in the UI. |
| 12 | Stack, weight and capacity limits enforced by the server | **Passed** | Unit (re-run today): stackMax, Strength-based carry capacity, overweight refusals. |
| 13 | Vault transfers work and preserve ownership | **Passed** | Unit (re-run today): terminal access required; random-operation property test. Recorded 10-16: live locker move, `TooFar` away from the terminal, rid dedup conserving totals. |
| 14 | Secure-container eligibility and capacity validated | **Passed** | Unit (re-run today). Recorded 10-16: live move to the secure container. |
| 15 | Players can't access or modify another player's vault or profile | **Partial** | Unit (re-run today): an unknown uid returns `NotOwned`. Recorded 10-16: a forged uid returns `NotOwned` live. Requests only resolve against the caller's own session, and state pushes go to the owner only. **Not run:** a two-player test. |
| 16 | The hub blocks ordinary combat on the server | **Passed** | Live (re-run today): gateway refusal, ForceField, zone bounds, collision group. |
| 17 | Keyboard/mouse and gamepad users can navigate the interfaces | **Partial** | Recorded 10-16: creator and inventory driven by mouse and keyboard. **Not run:** the gamepad was never driven (focus wiring exists in `ScreenManager`), and no test of the Vendor UI is recorded. |
| 18 | Save failures and shutdown handled without falsely claiming success | **Passed** | Unit (re-run today): a failed save or failed release is reported as failed; 3 shutdown tests. Live (re-run today): stop play saved and released, and the reload was clean. |
| 19 | No conflicting duplicate systems | **Passed** (project code) | Live scan (re-run today). Two third-party Studio plugins (cloud ids 4771536187, 7352299243) inject their own GUI scripts into playtests and fill the Output with errors. They aren't part of the place, but they should be reviewed or removed. |
| 20 | Publish and verify DataStore behaviour in a configured environment | **Partial** | Studio API access is on, and the real Studio DataStore round-trip passed today. **Not run:** on a published server. I couldn't confirm that the current build is published. |

## Milestones

| Milestone | State |
|---|---|
| A: Audit and architecture | Done |
| B: Prison hub | Done, plus the detail and realism passes: 7,377 parts, 81 lights, 10 catalog-dressed NPCs. Navigation re-checked after the detail pass. |
| C: Persistent profiles | Done; real-DataStore rejoin verified |
| E1: Item catalog and transaction engine | Done |
| D: Survivor creation | Done. Mouse flow verified; gamepad not verified. |
| E2: Inventory and vault UI | Built; mouse and keyboard verified on 10-16 |
| F: Currency and vendors | Built (`EconomyService`, `Config.Vendors`, `VendorScreen`, `Economy_Test`); all 22 economy tests pass. Neither recorded session shows this code being written, so nothing beyond these tests verifies it. Live trades and the Vendor UI are unverified. |
| G: Integration and acceptance | This report |

## Known gaps and pending actions

**What you need to do:**
- Set `Lighting.Technology = Future`. The tools can't set it.
- Save and publish the place.
- Review or remove the two plugins that inject scripts.

**Still to verify** (closes the 6 partial tests):
- one live buy and one live sell at a counter, through the Vendor UI;
- a gamepad pass over the creator, inventory, vault and vendor screens;
- on the published place with two clients:
  - a two-server lock contention test;
  - a cross-player access test.

**Not done:**
- The living-hub pass: NPC routines, ambient sound, wildlife, day/night.
- Section props not yet placed: hospital beds, workbench, generator, pallet stacks, dumpster.

**Housekeeping:** the old `tasks/todo.md` trackers are stale. They still list C–G as unchecked and E2/F as not started.
