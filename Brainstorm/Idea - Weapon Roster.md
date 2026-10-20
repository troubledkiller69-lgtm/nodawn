---
tags: [idea, combat, weapons]
status: shaping
updated: 2026-10-20
---

# Idea - Weapon Roster

**The pitch:** eight firearms at launch, chosen by job, not by model count. The M1911A1 is the baseline because it is the first gun built; the roster grows outward from what the map asks for: tight basements, open market streets, roofs, and the Cut.

**Scope discipline:** [[Idea - Combat Frame]] §12 still rules. v1 is one M1911A1, one ammo type, one melee. This note is the queue behind it, not permission to build eight guns now.

## 1. The levers

A gun in No Dawn is defined by:

- **Round** — damage, scarcity, noise.
- **Action** — semi, pump, bolt, full auto.
- **Shots to down** — 2 is the baseline; 1 is a specialist exception; 3 is volume.
- **Magazine and reload** — box, tube, stripper clip, cylinder.
- **Recoil and recovery** — vertical/horizontal, time to re-settle.
- **Sight picture and ADS** — irons, red dot, scope; how much the gun occludes.
- **Weight** — stamina drain, sprint speed, weapon sway.
- **Noise radius** — the map hears it.
- **Effective range** — where the round still does its job.
- **Attachments** — muzzle, optic, light.

Not levers: rarity colour, +damage tiers, weapon levels, proficiency grind. A gun's identity is its action, its round, and its noise.

## 2. The damage spine

Player 100 HP, standard infected 80. Head 3.0×, torso 1.0×, limbs 0.7×.

| Round | Damage | Torso shots | Head shots | Limb shots | Job |
|---|---:|---:|---:|---:|---|
| .22 LR | 22 | 5 | 2 | 7 | quiet utility, infected |
| 9×19 | 35 | 3 | 1 | 5 | volume, close control |
| .38 Special | 40 | 3 | 1 | 4 | cheap revolver |
| .45 ACP | 50 | 2 | 1 | 3 | baseline |
| 5.56×45 | 55 | 2 | 1 | 3 | precise, low recoil |
| 7.62×39 | 52 | 2 | 1 | 3 | punchy, heavy recoil |
| 7.62×54R | 90 | 2 | 1 | 2 | cheap bolt, roof |
| .308 Win | 105 | 1 | 1 | 2 | expensive bolt, one-shot |
| 12ga buckshot | 8 × 12 = 96 | 2 | 1 (3+ pellets) | — | contact, crippling |
| 12ga slug | 100 | 1 | 1 | 2 | single projectile, close |

Notes:

- **2-shot is the design centre.** A player who lands two torso hits with a workhorse rifle wins; misses are punished by the cycle.
- **1-shot is a specialist exception.** The .308 owns it. The Mosin does not; 90 damage is a 2-shot body, 1-shot head. This keeps the cheap surplus rifle from undercutting the expensive precision rifle.
- **Head is always lethal except .22.** 22 × 3 = 66; two headshots. That is the .22's identity: a quiet tool for infected and a desperation finisher, not a player-killer.
- **Limbs are a tempo tool.** 0.7× means three .45 limb hits (105) kill, but it takes longer, and the hitbox work already gives limp/recoil/bleed states. Leg shots are how you stop a runner, not how you win a duel.
- **Buckshot** is 8 pellets at 12. At contact all 8 hit for 96, a 2-shot; three pellets to the head (36 each) kill. At 8–10 studs, half the pellets miss and the second shot is the real decision. The sawn-off's two barrels are the one-shot.
- **Slug** is the ranged shotgun answer: one projectile, 100 at close range, falls off hard past 30 studs.

## 3. The launch roster

One primary, one sidearm, one melee, one throwable. The M1911A1 is a sidearm. The rifle, shotgun or SMG is the primary. You can carry a found gun in the inventory, but not equip a second primary in raid.

| Slot | Weapon | Round | Action | Capacity | Damage | Torso | Weight | Noise | Tier | Job |
|---|---|---|---:|---:|---:|---:|---|---:|---|---|
| Sidearm | **M1911A1** | .45 ACP | semi | 7+1 | 50 | 2 | medium | 220 | workhorse | baseline, 2-tap |
| Sidearm | **Glock 17** | 9×19 | semi | 17+1 | 35 | 3 | light | 200 | cheap | volume, fast follow-up |
| Primary | **Pump 12ga** | 12ga | pump | 6+1 | 96 | 2 | heavy | 260 | workhorse | rooms, the Cut |
| Primary | **SKS** | 7.62×39 | semi | 10 | 52 | 2 | medium | 240 | cheap | poor man's rifle |
| Primary | **AKM** | 7.62×39 | auto | 30 | 52 | 2 | heavy | 260 | professional | close/mid, loud |
| Primary | **MP5** | 9×19 | auto | 30 | 35 | 3 | medium | 200 | professional | control, interiors |
| Primary | **Mosin 91/30** | 7.62×54R | bolt | 5 | 90 | 2 | heavy | 280 | cheap specialist | roofs, ambush |
| Primary | **AR-15** | 5.56×45 | semi | 20 | 55 | 2 | medium | 230 | professional | precision, mid |

Why these eight:

- **M1911A1** — the first gun, the baseline 2-tap, the one every player learns.
- **Glock 17** — the volume pistol. 17 rounds, 3-shot, fast. The 1911 is the duellist's gun; the Glock is the panic gun.
- **Pump 12ga** — the interior gun. The Cut, basements, stairwells. It makes a corner a decision.
- **SKS** — the cheap rifle. 10 rounds, 2-shot, semi. The gun a scavenger can actually find and feed.
- **AKM** — the status gun. Full auto, 30 rounds, heavy recoil. It wins close fights and tells the block where you are.
- **MP5** — the control gun. 9mm, 30 rounds, low recoil, accurate. The professional's close-range answer.
- **Mosin** — the roof gun. 5 rounds, bolt, 90. One headshot kills; two body shots kill. Cheap, slow, punishing.
- **AR-15** — the precision semi. 5.56, 20 rounds, low recoil. **Semi-auto only** at launch; the select-fire M4 is a later, rarer gun.

The .38 revolver, .22, sawn-off, .308, Uzi and the heavy melee are the post-launch six. The AR-15 is the one that could swap with the .308 if the roof game needs a one-shot rifle more than a precision semi.

## 4. Post-launch

| Weapon | Round | Why it waits |
|---|---|---|
| .38 revolver | .38 | Flavour, but redundant with the Glock until the cheap-gun economy needs a third sidearm. |
| .22 pistol / rifle | .22 | The quiet infected tool. Needs the infected AI and a reason to choose quiet over lethal. |
| Sawn-off double | 12ga | The one-shot panic gun. Strong; wait until shotgun counterplay is measured. |
| .308 bolt | .308 | The one-shot torso. It should arrive when armour does, as the answer to it. |
| Uzi / Hi-Point | 9×19 | The junk spray. Cheap, inaccurate, fun; the starter SMG. |
| Hatchet / bat / sledge | melee | More melee roles once the stamina and limb systems are tuned. |

## 5. Melee

The Combat Frame's melee math is off: 30 blunt against 80 infected is three swings, not two; against 100 player it is four, not three. Fix it now, before the animations are built.

| Weapon | Damage | Infected | Player | Notes |
|---|---:|---:|---:|---|
| Pipe wrench | 40 | 2 | 3 | starter, blunt |
| Machete | 45 | 2 | 3 | blade, bleed |
| Crowbar | 38 | 3 | 3 | pry, durable |
| Baseball bat | 42 | 2 | 3 | reach, slow |
| Hatchet | 45 | 2 | 3 | blade, utility |
| Sledgehammer | 70 | 2 | 2 | slow, 20 stamina, loud |

Launch four: wrench, machete, crowbar, bat. The sledge is a post-launch "boss" melee; 70 damage two-shots a player, so it needs a 1.2 s swing and a hard stamina cost.

## 6. Throwables

| Item | Effect | Role |
|---|---|---|
| Brick / bottle | noise event, no damage | pull infected, bait a player |
| Smoke | line-of-sight break | cross the Cut, disengage |
| Molotov | fire area, damage over time | deny a stairwell, force a move |
| Frag | lethal fragmentation | military, rare, post-launch |

Throwables are the third leg of the fight beside guns and melee. They are found and bought, not crafted. A brick is free noise; a molotov is a commitment.

## 7. Attachments

- **Muzzle:** suppressor (noise ×0.35, weight +, ADS −), compensator (recoil −20%, noise +10%), none.
- **Optic:** irons default; red dot on rail-equipped guns (Glock, MP5, AR-15, AKM side rail); 4× scope on Mosin and AR-15. The M1911A1 has no rail and no optic.
- **Light:** weapon-mounted light. It helps in the Cut and basements and tells everyone where you are. A real trade.
- **No lasers.** They collapse the aim game.
- **Extended mags:** post-launch, rare loot, +capacity, +weight, changes the silhouette. Earned, never sold.

Attachments are inventory items with their own `uid`, lootable and lost on death ([[Idea - Combat Frame]] §8).

## 8. Acquisition and the ladder

- **Vendors:** rotating stock, Chits, limited quantity. The workhorse tier is always available; the professional tier is a few units per restock.
- **Raid loot by provenance:** residential (.22, .38, pump, 1911), police (Glock, MP5, pump), gun store (SKS, 1911, hunting rifles), military (AKM, AR-15, .308).
- **Player corpses:** everything, including the loaded magazine.
- **No level gates.** A level 1 can find an AKM. That is the extraction dream and the anti-headstart rule. Levels unlock vendor variety, not exclusivity ([[Idea - Progression]]).
- **Condition:** worn / serviceable / pristine. At launch it affects value and cosmetic wear, not damage or reliability. Malfunctions are a later system; do not promise them now.

## 9. What the map asks for

- **The Cut (below grade, dark, tight):** pump, MP5, pistol, weapon light, melee. Sound carries; a gunshot is a map event.
- **Market streets (20–40 studs):** SKS, AKM, AR-15. The workhorse range band.
- **Roofs (long):** Mosin, AR-15 with a scope. The grapple makes a roof a route; a bolt-action makes it a threat.
- **Basements and sheds:** shotgun, pistol, melee, light. The places where a rifle is too long.
- **The counter to a roof:** the Cut, interiors, smoke, and the fact that a bolt-action is a liability at five studs. Do not add a second sniper rifle until the roof game is measured.

## 10. TTK philosophy

- **2-shot baseline.** A workhorse rifle or the 1911 kills in two torso hits. Miss and the cycle punishes you.
- **1-shot specialist.** .308 body, any headshot except .22. Rare, expensive, slow.
- **3-shot volume.** 9mm. More rounds, faster follow-up, more noise.
- **Melee 2–3 swings.** A fallback, not a primary. The wrench is what a dead player has.
- **No armour in v1.** When it arrives, penetration, not flat reduction.
- **No malfunctions in v1.** Condition is value, not function.
- **No weapon levels, no +damage attachments.** The gun you find is the gun.

## 11. Open questions

- **AR-15 at launch, or .308?** Recommend AR-15 at launch, semi-auto; .308 with armour.
- **Mosin 90 or 105?** Recommend 90 (2-shot body). The .308 owns the 1-shot.
- **Pump one-shot at contact?** Recommend no; the sawn-off is the one-shot.
- **Melee fix to 40/45?** Recommend yes; the current 30 contradicts the HP table.
- **One primary?** Recommend yes; it makes the sidearm a real choice and keeps the roster readable.
- **7.62×39 common or scarce?** Recommend common. The SKS and AKM are the workhorse family; 5.56 and .308 are the scarce ones.
- **Do we need a second 9mm?** Not at launch. The Glock and MP5 cover different jobs.
