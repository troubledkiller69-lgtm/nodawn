---
tags: [idea, raids, death]
status: shaping
updated: 2026-10-20
---

# Idea - Death and Loss

**The pitch:** death is not a screen. Your gear stays on the body. If the brain is intact, the body gets up. The only way to keep what you carried is to destroy the head — or have a friend still in the raid.

## 1. What drops

| Carried | On death |
|---|---|
| Equipped weapon, sidearm, melee | On the body |
| Backpack and its contents | On the body |
| Unsecured raid loot | On the body |
| Carried Dollars | On the body |
| Attachments, magazines, ammo | On the body |
| Secure container (2×2) | Protected; not on the body |
| Hub vault | Protected |
| Occupation, traits, account renown, cosmetics | Protected |

The death transaction is atomic and server-owned: one item UID moves once, or nothing moves.

## 2. The secure container

- 2×2 grid, 4 cells, free, never sold.
- Small items only: meds, ammo, keys, quest items.
- No weapons, armour, attachments or oversized items.
- Contents survive death and are not on the body.
- Server-enforced. The client UI is not the boundary.

## 3. The body

- A corpse that does **not** reanimate persists **5 minutes** and is lootable by anyone.
- A reanimated body is an NPC and persists until killed, **10 minutes**, or server close.
- The 20-minute GPT version is rejected. In a 50-minute raid, 20 turns a corpse into a free cache and removes the race.

## 4. Reanimation — the body keeps the loot

This is the rule that makes death physical.

- If the brain is intact, the body reanimates **10 seconds** after death. It becomes a standard infected NPC wearing the dead survivor's clothing and carrying their gear.
- The player returns to the hub. They do not control the body.
- The body is a **loot container on legs**: it cannot be looted while animate. Kill it and the loot drops at the new death spot as a normal corpse, with the same five-minute window.
- If the body despawns, falls out of the world, or the server closes, the loot is lost.
- The body is **leashed** to the death point: 80 studs. It can chase a target beyond the leash, but returns when it loses them. The loot stays findable.
- The body counts against the infected budget. Player bodies reserve slots; with 16 players there are at most 16 of them, and the 80/20 budget absorbs them.
- It does not use the player's gun. It attacks as a standard infected. The gear is cargo, not a weapon.

## 5. Destroying the brain

- A **headshot kill** destroys the brain: no reanimation. The body stays a corpse.
- A **melee finisher to the head** during the 10-second window destroys the brain: no reanimation. 1.0 s, noise 15.
- A torso or limb kill leaves the brain intact: the body rises.
- No random chance. The rule is deterministic and readable.
- The double-tap is a real decision: loot now, or spend a second and a noise event to keep the body down.

## 6. Who can recover it

- The victim returns to the hub with clothing and a knife. No free gun, armour, ammunition or healing.
- **No late joiners in v1**, so the victim cannot re-enter the same raid.
- The body stays in the raid for the players still inside. The victim's squad can kill it and extract the gear; a solo victim cannot.
- A party-only marker shows the **death point**, not a live tracker, so the squad can find the body.
- This is the squad dividend and the solo risk. It is not a second chance; it is a recovery job for someone else.
- The dog tag is on the body. The tag design is account-bound; the dropped tag is a trophy.

## 7. Disconnect and combat logging

- A deliberate leave, timeout, or disconnect during an active raid counts as death.
- The body drops at the last server-validated position. If the brain is intact, it reanimates.
- Rejoining does not restore gear. No inventory rollback, no duplicate loot.
- If persistence fails, do not overwrite a valid profile with a blank one. Log the failure and use the safe recovery path.

## 8. The fallback loop

- Clothing and a knife are free.
- The first contract pays 150–200 Dollars; one free bandage on creation.
- No starting Dollars beyond the creation reward. The knife run is the first raid, not a punishment.
- The first gun is found or two or three good runs away. The 300-Dollar basic firearm is the target.

## 9. What this does to the fight

- A gunfight ends when the brains are destroyed, not when the last player drops.
- Every kill leaves a body that may get up. The killer chooses: loot, stomp, or leave.
- Long fights feed the horde. The dead count against the AI budget.
- Squads can recover a body; solos cannot. The risk is real, and the squad is worth something.

## 10. Open questions

1. Corpse persistence: 5 or 8 minutes? 20 is rejected.
2. Reanimation delay: 8, 10, or 12 seconds?
3. Leash: 60, 80, or 100 studs?
4. Despawn: 10 minutes, or until server close?
5. Can the victim re-enter the same raid? **Recommend no.**
6. Does the secure container hold quest items? **Recommend yes.**
7. Does a reanimated body drop the dog tag, or is the tag on the corpse only? **Recommend on the body.**
