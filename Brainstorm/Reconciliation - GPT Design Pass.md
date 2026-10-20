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
| **DNL — Death and Loss** | 2×2 secure container; server-side enforcement; atomic death transaction; reanimation with no player control; disconnect-as-death | Body persists **20 min** vs the Raid Frame's **5 min**; loot location was undefined | Keep the container and transaction rules; reject 20 min; **loot stays on the reanimated body** ([[Idea - Death and Loss]]) |
| **INFECTED** | Lighting in the vision check; attack wind-up/recovery; local horde only; 30-local cap; AI update budget; server-validated bite/damage | **120 active / 30 local** vs the Detroit Map's **80 total / ~20 active** | Keep senses, telegraphs and horde logic; scale 4–5 per player, cap 100, 20–24 local; 120 only as a measured stress valve |
| **ENL — Economy and Loot** | 25% vendor buyback; launch price table; no starting Dollars; anti-inflation metrics; finite vendor restock | **Basic armour 180** and **weapon repair 40** vs the Combat Frame's **no armour, no malfunctions in v1** | Keep the metrics and most prices; cut armour; defer repairs; tune the 25% |
| **CAC — Character Creation** | All attributes 5; no free allocation; one positive/one negative trait; Sadist server-side rules | **No free points** vs the Master Context's **5-point pool**; **skills improve through use** vs the no-power-growth model | Adopt the Sadist almost verbatim; no-allocation is a real change to approve; skills need a definition or a cut |

## 2. The four fights

### 2.1 Body persistence — 5 or 20 minutes?

The Raid Frame says five minutes. GPT says 20, or until the server closes. In an open server that refills its containers, 20 minutes makes a static cache and removes the race. Five minutes creates the decision: camp the body, or leave before the noise brings company.

**Recommendation: 5 minutes, or 8 if five feels too tight.** A corpse is a performance cost and a static cache; the reanimated body is the interesting timer. The five-minute clock is for a corpse that stays down; a reanimated body is an NPC and runs on its own timer — see §5.

### 2.2 Infected — how many?

The Detroit Map is authored on 80 infected and ~20 active. GPT proposes 120 active with a 30-local cap. Open servers make both fixed numbers wrong: the population moves between 8 and 32, so the infected count has to move with it.

**Recommendation: 4–5 active per player, cap 100, 20–24 local (within 200 studs of any player).** At 24 players that is ~100 active, near GPT's number; at 8 players it is ~40, and the map is not overrun. 120 stays the measured stress valve, not the design. The authored 80 remains the 16–20-average budget.

### 2.3 Skills — the third rail

The Master Context §8 wants skills that improve through use, persist through death, and have diminishing returns. GPT repeats it: "Skills improve through use." The no-headstart model says a level 50 and a level 1 with the same gear are a 50/50 fight. Those cannot both be true if skills grant reload speed, aim stability, stamina or carry weight.

**Recommendation: cut stat skills at launch.** Make skills a **knowledge codex** — weapon manuals, locations, vendor contacts — with cosmetic completion rewards. If He wants use-based growth, cap it as sidegrades (faster reload vs. quieter steps) and define the list. Do not leave "skills" undefined and let it become a second attribute bar.

### 2.4 Attributes — five free points or no allocation?

The Master Context §8 says occupations provide free attribute points, with a base pool of five. GPT says all attributes start at 5 and there is no free allocation; occupations and traits modify within 1–10. The GPT version prevents min-maxed builds and is simpler for launch. It also removes a chunk of build identity.

**Recommendation: no free allocation at launch, as a deliberate simplification.** Occupations and traits carry the build. If the creator feels flat, add the five points in a later pass, not before the first raid is stable.

## 3. The economy fights

- **25% buyback.** It is a strong sink and prevents buy-low/sell-high loops. It may also make looting feel bad: a 300-Dollar firearm against common loot at 5–15 Dollars is a long early game. **Test 35–40% first**, or keep 25% and add a first contract that pays 150–200. The anti-inflation metrics will decide.
- **Armour 180.** Cut. The Combat Frame says no armour in v1; penetration arrives with it. A price for something that does not exist is a promise the code cannot keep.
- **Repair 40.** Defer until condition degrades and malfunctions exist. At launch condition is value, not function. A repair service with nothing to repair is a UI stub.
- **Starting Dollars.** GPT says none. The fallback knife run then needs a path to the first gun. **Recommendation: no starting Dollars, but a first contract worth 150–200 and one free bandage on creation.** The no-free-gun rule survives; the new player does not starve.

## 4. The Sadist — adopt

The CAC note is the best of the four. It gets the important thing right: the Sadist is a **server-side hidden occupation** whose identifier, eligibility flag, modifier and payload never replicate. Killing a standard infected grants a small stress reduction with a per-raid cap and diminishing returns; no damage, accuracy, health, stamina, loot or other combat advantage; player kills do not trigger it; no UI, unique kit or leaderboard field; disabled behind a server flag until the assignment rules exist.

Two additions:

- The Master Context says stress **and boredom**. Add boredom relief, or state that boredom is cut.
- Decide the assignment: developer-controlled test occupation until an unlock condition is designed. Do not invent a client-visible eligibility check.

## 5. Loot on the reanimated body

His call, and it is better than the cache: the loot stays **on the body**. If the brain is intact, the body gets up carrying the gear. It is a loot container on legs — you kill it to open it.

The rules are in [[Idea - Death and Loss]] §4–6. The short version: 10-second reanimation delay, headshot kill or melee finisher destroys the brain, 80-stud leash, 10-minute despawn. Late join is normal, so the victim can return to the same server if it has space and the body timer is live; the squad can recover the body too.

The old static-cache idea is dead.

## 6. What survives

From DNL: the 2×2 container, server-side enforcement, atomic death transaction, reconnect-as-death, reanimation without player control, and loot on the body.
From INFECTED: lighting in the vision check, attack telegraphs, local hordes only, AI budget, server-validated bites.
From ENL: the price table minus armour and repairs, the 25% rule as a tuning knob, the metrics, finite restock, no starting Dollars plus a first contract.
From CAC: the Sadist, one positive/one negative trait, occupation kits that never include a firearm, fixed attributes at launch.

## 7. Open questions for He

1. Corpse persistence: 5 or 8 minutes? 20 is rejected; a reanimated body is an NPC.
2. Infected scaling: 4–5 per player, cap 100, 20–24 local? **Recommend yes.**
3. Skills: cut, codex, or capped sidegrades?
4. Attributes: no allocation or the five-point pool?
5. Starting economy: first contract, creation stipend, or neither?
6. Does the secure container hold quest items, or only loot?
7. Can the victim re-enter the same server to recover their body? **Yes, if it has space and the body timer is live.**
