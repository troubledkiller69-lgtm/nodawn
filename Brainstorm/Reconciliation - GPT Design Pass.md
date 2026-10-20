---
tags: [reconciliation, design]
status: shaping
updated: 2026-10-20
---

# Reconciliation - GPT Design Pass

Four fresh notes were pulled from GPT on 2026-10-20: `nodawnDNL`, `nodawnINFECTED`, `nodawnENL`, `nodawnCAC`. They are a second pass, not a first. They contain real numbers the vault did not have, and four decisions that fight standing rulings. This note records what survives and what needs His call.

## 1. The four files

| File | What is new and worth keeping | What fights the vault | Verdict |
|---|---|---|---|
| **DNL — Death and Loss** | 2×2 secure container; server-side enforcement; atomic death transaction; reanimation with no player control; disconnect-as-death | Body persists **20 min** vs the Raid Frame's **5 min**; reanimated body's loot location is undefined | Keep the container and transaction rules; reject 20 min; define the death cache |
| **INFECTED** | Lighting in the vision check; attack wind-up/recovery; local horde only; 30-local cap; AI update budget; server-validated bite/damage | **120 active / 30 local** vs the Detroit Map's **80 total / ~20 active** | Keep senses, telegraphs and horde logic; use 80/20 at launch, 120 only as a measured stress valve |
| **ENL — Economy and Loot** | 25% vendor buyback; launch price table; no starting Chits; anti-inflation metrics; finite vendor restock | **Basic armour 180** and **weapon repair 40** vs the Combat Frame's **no armour, no malfunctions in v1** | Keep the metrics and most prices; cut armour; defer repairs; tune the 25% |
| **CAC — Character Creation** | All attributes 5; no free allocation; one positive/one negative trait; Sadist server-side rules | **No free points** vs the Master Context's **5-point pool**; **skills improve through use** vs the no-power-growth model | Adopt the Sadist almost verbatim; no-allocation is a real change to approve; skills need a definition or a cut |

## 2. The four fights

### 2.1 Body persistence — 5 or 20 minutes?

The Raid Frame says five minutes. GPT says 20, or until the server closes. In a 50-minute raid, 20 minutes turns a corpse into a free cache and removes the race. Five minutes creates the decision: camp the body, or leave before the noise brings company.

**Recommendation: 5 minutes, or 8 if five feels too tight.** The 20-minute version belongs to a game with longer raids and fewer players.

### 2.2 Infected — 80/20 or 120/30?

The Detroit Map is built on 80 infected, ~20 active within 200 studs. GPT proposes 120 active with a 30-local cap. 120 active humanoids with pathfinding is a server-cost risk, and 120 on 0.40 km² is 300/km², denser than the map's density argument.

**Recommendation: 80 authored, 20 active baseline, 30 local, 120 global cap only after measurement.** The cap is a safety valve for hordes, not the population. If the server holds 120, raise the authored count deliberately; do not let a cap become the design.

### 2.3 Skills — the third rail

The Master Context §8 wants skills that improve through use, persist through death, and have diminishing returns. GPT repeats it: "Skills improve through use." The no-headstart model says a level 50 and a level 1 with the same gear are a 50/50 fight. Those cannot both be true if skills grant reload speed, aim stability, stamina or carry weight.

**Recommendation: cut stat skills at launch.** Make skills a **knowledge codex** — weapon manuals, locations, vendor contacts — with cosmetic completion rewards. If He wants use-based growth, cap it as sidegrades (faster reload vs. quieter steps) and define the list. Do not leave "skills" undefined and let it become a second attribute bar.

### 2.4 Attributes — five free points or no allocation?

The Master Context §8 says occupations provide free attribute points, with a base pool of five. GPT says all attributes start at 5 and there is no free allocation; occupations and traits modify within 1–10. The GPT version prevents min-maxed builds and is simpler for launch. It also removes a chunk of build identity.

**Recommendation: no free allocation at launch, as a deliberate simplification.** Occupations and traits carry the build. If the creator feels flat, add the five points in a later pass, not before the first raid is stable.

## 3. The economy fights

- **25% buyback.** It is a strong sink and prevents buy-low/sell-high loops. It may also make looting feel bad: a 300-Chit firearm against common loot at 5–15 Chits is a long early game. **Test 35–40% first**, or keep 25% and add a first contract that pays 150–200. The anti-inflation metrics will decide.
- **Armour 180.** Cut. The Combat Frame says no armour in v1; penetration arrives with it. A price for something that does not exist is a promise the code cannot keep.
- **Repair 40.** Defer until condition degrades and malfunctions exist. At launch condition is value, not function. A repair service with nothing to repair is a UI stub.
- **Starting Chits.** GPT says none. The fallback knife run then needs a path to the first gun. **Recommendation: no starting Chits, but a first contract worth 150–200 and one free bandage on creation.** The no-free-gun rule survives; the new player does not starve.

## 4. The Sadist — adopt

The CAC note is the best of the four. It gets the important thing right: the Sadist is a **server-side hidden occupation** whose identifier, eligibility flag, modifier and payload never replicate. Killing a standard infected grants a small stress reduction with a per-raid cap and diminishing returns; no damage, accuracy, health, stamina, loot or other combat advantage; player kills do not trigger it; no UI, unique kit or leaderboard field; disabled behind a server flag until the assignment rules exist.

Two additions:

- The Master Context says stress **and boredom**. Add boredom relief, or state that boredom is cut.
- Decide the assignment: developer-controlled test occupation until an unlock condition is designed. Do not invent a client-visible eligibility check.

## 5. The death cache

The DNL note says reanimated bodies do not retain player control, but it does not say where the loot is. If the corpse gets up and walks, a player's gear can travel across the map.

**Recommendation:** on death, the loot drops into a static **death cache** at the death position. The reanimated body is a separate NPC and carries no player loot. The player returns to the hub. The cache persists for five minutes and is lootable by anyone.

This also solves the disconnect case: the body drops a cache at the last valid server position, then the player is removed.

## 6. What survives

From DNL: the 2×2 container, server-side enforcement, atomic death transaction, reconnect-as-death, reanimation without player control.
From INFECTED: lighting in the vision check, attack telegraphs, local hordes only, AI budget, server-validated bites.
From ENL: the price table minus armour and repairs, the 25% rule as a tuning knob, the metrics, finite restock, no starting Chits plus a first contract.
From CAC: the Sadist, one positive/one negative trait, occupation kits that never include a firearm, fixed attributes at launch.

## 7. Open questions for He

1. Body persistence: 5 or 20 minutes?
2. Infected: 80/20 or 120/30?
3. Skills: cut, codex, or capped sidegrades?
4. Attributes: no allocation or the five-point pool?
5. Starting economy: first contract, creation stipend, or neither?
6. Does the secure container hold quest items, or only loot?
