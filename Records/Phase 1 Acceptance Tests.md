# Phase 1 — Acceptance tests (from the user's Phase 1 spec, §15)

> **This is the test specification, not the results sheet.** It lists the 20 scenarios and the definition of done. Results live in [[Phase 1 Final Report]] and [[Tracker]]; the current tally is 15 pass, 5 partial, 0 failed.

Report each as passed / failed / partially verified / not run, and say whether it was a local Studio
test or needs a published experience / multiple clients.

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

## Definition of done (spec)
The hub is navigable, the survivor creator works, profiles persist safely, occupations and attributes
are validated, basic vendors and currency work, inventory/vault/secure foundations prevent
duplication, the hub is a server-enforced safe zone, and the UI supports keyboard/mouse and gamepad.
Also required: clean up temporary debugging code and unused objects; never claim something works
unless it was actually verified.
