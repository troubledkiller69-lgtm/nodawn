---
tags: [idea, combat, survival]
status: shaping
updated: 2026-10-20
---

# Idea - Injury and Survival

**The pitch:** the body is the story. A bullet does not just remove HP; it opens a wound on a body part, and that wound has a treatment path, a cost and a consequence. Hunger and thirst are the personal clock that makes a long server a survival game instead of a farm.

Project Zomboid does not call it "procedural injuries" — it is a **body-part wound system**. The procedure is cause → body part → wound family → treatment → status. That is the part worth taking.

## 1. What Project Zomboid does

| PZ system | How it works | What No Dawn takes |
|---|---|---|
| Health | One overall pool, not per-limb HP. 17 body parts hold **conditions**, not separate health bars. | One 100 HP pool; wound status on a part. |
| Wound families | Scratch, laceration, deep wound, bite, burn, fracture, lodged bullet, lodged glass. | Six families in v1; glass folds into deep. |
| Location | Neck bleeds ×1.5, head ×1.0, hands ×0.2. Pain and healing vary by part. | Compressed six-group multiplier table. |
| Treatment | Bandage → disinfect → stitch → splint; tweezers for bullets/glass; dirty bandages. | Same sequence, raid-length timers. |
| Pain | A moodle and a status penalty, not damage. | Pain 0–100; aim sway and stamina effects. |
| Fracture | Disables the limb; a splint restores function, not the bone. | Leg: no sprint/jump. Arm: recoil/reload penalty. |
| Infection | Bacterial wound infection is curable; the Knox virus is a separate, fatal timer. | Bacterial in v1; the virus waits for v4. |
| Hunger / thirst | Moodles; at zero, health drains. | Two meters, drained only in raid. |
| First Aid skill | Speeds treatment and improves outcomes. | **Cut.** No power growth; level 1 and 50 treat the same. |

PZ's timelines are days long. No Dawn is a deployment game, so the same states are compressed: a wound lasts the raid and is finished at the hub, not over a week.

## 2. The model in one line

**Global HP stays 100. A wound is a status record on a body part — type, bleed, pain, treatment state, infection, fracture — not a second health bar.**

This preserves the Combat Frame's ruling against per-limb health. There are no limb HP pools. The wound changes what the player can do, not how much HP the limb has.

## 3. The body map

Six groups, mapped from the R15 parts the hitbox already reads. The exact part is stored for the health panel; the group drives the penalty.

| Group | R15 parts | Bleed × | Pain × | Function |
|---|---|---|---|---|
| Head | Head | 1.2 | 1.0 | Concussion: blur, tinnitus, sway |
| Torso | UpperTorso, LowerTorso | 1.0 | 0.8 | Internal bleed; no tourniquet |
| Arms | UpperArm, LowerArm (L/R) | 0.5 | 0.7 | Aim, reload, melee |
| Hands | Hand (L/R) | 0.3 | 0.5 | Aim, reload, treatment speed |
| Legs | UpperLeg, LowerLeg (L/R) | 0.6 | 0.7 | Walk, sprint, jump, mantle |
| Feet | Foot (L/R) | 0.3 | 0.5 | Walk speed, footstep noise |

Neck and groin from PZ are folded into head and torso. 17 parts is a PZ UI tax; six groups fit a Roblox health panel and cover every consequence that matters.

## 4. The wound families

Bleed is HP per second before location multipliers. Pain is points. All numbers are placeholders until `Config` owns them.

| Wound | Cause | Bleed | Pain | Field treatment | Function | Hub cost |
|---|---|---|---|---|---|---|
| Scratch | Infected nails, glass graze, short fall | 0.3 | 8 | Bandage | −5% | Free |
| Laceration | Blade, low-damage bullet, infected | 0.8 | 15 | Bandage; disinfect | −10% | 15 Dollars |
| Deep wound | Blade ≥30, bullet 25–39, glass | 1.5 | 25 | Bandage; stitch | −25% | 40 Dollars |
| Lodged bullet | Bullet ≥40 | 1.8 | 30 | Tweezers; stitch; bandage | −30% | 80 Dollars |
| Fracture | Fall ≥25 studs, heavy blunt ≥50 to a limb (sledge) | 0 | 35 | Splint | Leg: no sprint/jump, walk ×0.6. Arm: recoil +50%, reload +50% | 100 Dollars |
| Burn | Fire, explosion | 0.5 | 30 | Water; bandage | −15% | 60 Dollars |
| Bite | Infected bite | 1.2 | 28 | Bandage; disinfect | −15% | 70 Dollars; virus flag is v4 |

A wound is a record, not a hit reaction. It survives the fight, the extraction and the loading screen; the hub is where it ends.

## 5. How a hit becomes a wound

1. `HitSelect` returns the part, the post-multiplier damage and the source.
2. If the hit kills, no wound record is written. Death and reanimation take over.
3. Otherwise `WoundRules.classify(source, part, damage)` picks the family:
   - **Infected scratch** → scratch. **Infected bite** → bite.
   - **Bullet:** <25 laceration, 25–39 deep, ≥40 lodged. A non-lethal head hit adds concussion.
   - **Blade:** <30 laceration, ≥30 deep.
   - **Blunt:** <50 contusion (scratch tier), ≥50 fracture on a limb. A bat is a contusion; the sledge breaks bone. Head/torso blunt adds concussion/internal.
   - **Fall:** <15 studs scratch, 15–25 laceration, ≥25 leg fracture.
   - **Fire/explosion:** burn.
4. If a limb hit carries to the torso, the wound goes on the torso. The final part owns the consequence.
5. No random crit. The hit location and the damage are the variance; the wound is the deterministic consequence.

Examples:

- .45 torso, 50 damage → lodged bullet, 1.8 HP/s. At 50 HP the player has ~28 s to bandage and stitch or bleed out.
- 9mm limb, 35 damage → deep wound, 1.5 HP/s. Three limb hits still kill, but the second one is now a race.
- Sledge to the arm, 70 → fracture. A bat at 42 is a contusion; it hurts, it does not break the arm.
- Fall 30 studs → leg fracture. The extract is a walk, not a sprint.
- Infected bite, 12 → bite wound. The virus is not in v1; the bleeding is.

## 6. Bleeding, pain, fracture

**Bleeding** is the sum of the wounds, capped at 3.0 HP/s. A bandage stops light bleeding and slows a deep wound; a deep or lodged wound needs a stitch to stop. A bandage goes dirty after 8 minutes and must be replaced. Light wounds cannot kill alone from full health; deep and lodged wounds can. This replaces the flat 1 HP/s in [[Idea - Combat Frame]] §5.

**Pain** is the sum of the wounds, capped at 100.

| Pain | Effect |
|---|---|
| 30 | Aim sway +20% |
| 60 | Aim sway +40%, stamina regen −30% |
| 80 | Tunnel vision, interaction speed −30% |

Painkillers subtract 30 for 5 minutes. Pain does not decay while a wound is untreated; a bandaged or stitched wound lets it fall by 1 per 5 s.

**Fracture** is a limb flag, not a wound with bleed.

- Leg: no sprint, no jump, no mantle; walk ×0.6. Grapple attach is allowed, the launch leap is not.
- Arm: recoil +50%, reload +50%, melee −40%. Two-handed weapons still fire; they just punish.
- Splint: walk ×0.8, no sprint/jump; arm penalties halve. The fracture remains until the hub.
- No torso or head fractures in v1. Blunt hits there are concussion/internal bleeding.

**Concussion** is a non-lethal head hit: 30 s of blur and tinnitus, pain 40, bleed 1.0. It does not stack; the second head hit usually kills.

## 7. Treatment

| Item | Time | Does | Notes |
|---|---|---|---|
| Bandage | 2.0 s | Stops light bleed; slows deep | Dirty after 8 min |
| Disinfectant | 2.0 s | Prevents bacterial infection for 15 min | Pain +5 |
| Suture needle | 4.0 s | Closes deep/lodged after extraction | Consumed |
| Needle and thread | 5.0 s | Same, +10 pain | Consumed |
| Splint | 4.0 s | Supports a fracture | Branch + cloth |
| Tweezers | 3.0 s | Removes a bullet or glass | Then stitch |
| Painkillers | 1.0 s | −30 pain for 5 min | No healing |
| Antibiotics | 30 s | Clears bacterial infection | Does not touch the virus |

- Self-treatment takes 1.5× the time; a squadmate takes 1.0×. Both players must be stationary. Damage interrupts and resets the action.
- Field treatment stops the immediate threat. The wound itself persists until the hub.
- **Hub medical ward:** instant treatment for Dollars at the prices in §4, plus a full HP restore. No real-time timer. The ward is a money sink, not a gate.
- The ward never lets a player bleed out in the hub: stopping the bleed is free; closing the wound and mending the bone cost. A broke player can always stop the bleed.
- No First Aid skill. Treatment speed comes from the item and the animation, not the account. A level 1 and a level 50 with the same bandage are identical.

## 8. Infection

**Bacterial wound infection** is the PZ system that fits v1.

- A wound with no bandage, or a dirty bandage, for 10 minutes becomes infected.
- Infected adds 10 pain and blocks field healing; it is not fatal.
- Disinfectant before bandaging prevents it for 15 minutes; antibiotics clear it in 30 s; the hub clears it for 20 Dollars.
- A dirty bandage is the tell: the health panel says `Dirty` and the wound row turns.

**Zombie infection is v4, not v1.** A bite is a severe wound now. The virus — the Knox equivalent, the Antizin timer, the guaranteed turn — waits for the infection and reanimation system to be stable. PZ's defaults are bite 100%, laceration 25%, scratch 7%, with death in 2–3 in-game days. That is a survival-game clock, not a 40-minute deployment clock. Why:

- A second invisible death timer fights the extraction loop. The player already has bleeding, pain, hunger and thirst; a fifth clock that only a rare loot item can pause is a different game.
- The reanimation rule already makes death physical. The bite virus should arrive with the system that lets a player manage it, not before.

If He wants it sooner: a 15-minute timer, Antizin as a rare medical item, death → reanimation. That is a switch, not a redesign.

## 9. Hunger and thirst

Two meters, 0–100, start at 100. They drain **only in raid** — not in the hub, not offline.

| Meter | Drain | Empty |
|---|---|---|
| Hunger | 1 per 48 s (80 min) | −0.1 HP/s |
| Thirst | 1 per 33 s (55 min) | −0.15 HP/s |

- Below 25: stamina regen −25% for each meter.
- At 0: health drains. Both at 0 is 15 HP/min; a player has ~6–7 minutes to find food and water or extract.
- A 40-minute deployment leaves thirst near 27 and hunger near 50. A 60-minute deployment is out of water. That is the clock.

| Item | Restores | Notes |
|---|---|---|
| Canned beans | 30 hunger | Common |
| MRE | 60 hunger | Rare, military |
| Granola bar | 20 hunger | Common |
| Dog food | 15 hunger | Common, low value |
| Water bottle | 40 thirst | Common |
| Soda | 25 thirst, 5 hunger | Common |
| Canteen | 60 thirst | Hub item, refillable |

- Hub canteen: water free, a basic meal 5 Dollars. The prison farm and the animal pen are the reason it is cheap. No player can be soft-locked out of food.
- Eating and drinking take 2 s; opening a can is noise 5. The item takes a backpack slot, so the secure container is a real choice between meds, food and quest items.
- Meters persist between deployments. The hub is where you top up; the raid is where you spend.
- HUD shows an icon only below 50. The health panel shows the numbers.

This is the sink the open server needed. [[Idea - Raid Frame]] removed the server timer; hunger and thirst are the personal timer that replaces it, without a countdown on screen.

## 10. UI and animation

- **Health panel (H):** a six-zone body silhouette, wound rows with part and state, treatment buttons, and bleed / pain / fracture / infection icons. Hunger and thirst bars sit at the bottom.
- **HUD:** the heart icon pulses when bleeding; a pain vignette at 60+; a limp at a leg wound; an arm hold at an arm wound. No numbers unless the panel is open.
- **Squad:** a medic can treat a teammate by proximity; the prompt names the worst wound. The target's full panel stays private.
- **No world text.** The panel and the HUD are UI, which is allowed.
- **Animation handoff:** wounded locomotion, the bandage loop, the stitch loop and the splint loop go to Claude under the [[Working Agreement]]. The rules, state and panel stay with Nyx.

## 11. What this does to a fight

- A leg fracture changes the escape, not the damage.
- An arm fracture changes the weapon, not the hit points.
- A deep wound forces a stitch or a bleed-out; the fight continues after the last shot.
- A concussion blurs the aim at the exact moment the player needs it.
- A bite is a crisis now and a virus later.
- Squads get a medic role without a class, a level or a skill tree.
- The open server accumulates damage. The hub is the reset; hunger and thirst are the reason to go back before the backpack is full.

## 12. Scope and tests

**v1 ships:** six wound families (scratch, laceration, deep, lodged bullet, fracture, burn), the bite as a severe laceration with a flag, six treatment items, six body groups, two meters. One module each for wounds and survival.

**Cut:** 17 body parts, per-limb HP pools, First Aid skill, Knox virus, temperature/wetness, exercise fatigue, lodged glass as its own family, dismemberment.

**Tests for `Shared.Rules.WoundRules`:**

1. Bullet, torso, 50 → lodged bullet; bleed 1.8.
2. Bullet, limb, 35 → deep wound; bleed 1.5.
3. Blade, arm, 30 → deep wound.
4. Blunt, arm, 30 → fracture.
5. Fall, leg, 25 studs → fracture.
6. Infected scratch, torso, 8 → scratch.
7. Infected bite, hand, 12 → bite.
8. Bandage on a deep wound → bleed 0, still deep; stitch closes it.
9. Splint on a leg fracture → walk ×0.8, no sprint.
10. Dirty bandage at 10 min → infected.
11. Pain 60 → aim sway 40%, stamina regen −30%.
12. Hunger 0 → −0.1 HP/s; thirst 0 → −0.15 HP/s.

**Balance risks:**

- **UI tax.** Six groups and eight treatment items is the ceiling. Do not add a seventh family before the first one is read in play.
- **Death spiral.** A leg fracture plus a bleed plus hunger can be unrecoverable. The fallback is the knife run; that is allowed, but the player must be able to tell which wound is killing them.
- **Fracture frustration.** A 100-Dollar hub fix is a sting, not a lock. If it feels like a lock, lower the price before removing the fracture.
- **Food trivialization.** If everyone carries three waters, the meter is decoration. Tune the drain and the slot cost, not the punishment.

## 13. Open questions

1. Fractures from bullets, or blunt and falls only? **Recommend blunt and falls in v1.**
2. Pain effects: aim sway only, or movement too? **Recommend aim and stamina, not speed.**
3. Hunger/thirst rates: 80 and 55 minutes, or faster? Test a 60-minute deployment.
4. Bite virus in v1 or v4? **Recommend v4.**
5. Wounds persist through extraction? **Recommend yes; the hub ward is the sink.**
6. Can the secure container hold meds and food? **Recommend yes, small items only.**
7. Does a splinted leg allow the grapple? **Recommend attach yes, launch leap no.**

## 14. Sources

PZ numbers and treatment paths are from the public health documentation, not from No Dawn:

- [PZwiki: Health](https://pzwiki.net/wiki/Health) — injury table, body parts, infection.
- [ZomboidDB: Wound recovery](https://zomboiddb.wiki/tools/injury) — timers, body-part multipliers, infection odds.
- [Pindrop: Injuries & First Aid (B42)](https://pindrop.gg/project-zomboid/injuries) — bite/laceration/scratch odds, treatment sequences.
- [Bamboo Gaming: Health & Medical](https://www.bamboogaming.net/project-zomboid/health) — the five-step triage path.

The PZ values are reference points. No Dawn's numbers are its own and belong in `Config`.
