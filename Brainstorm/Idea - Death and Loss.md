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
- A 20-minute corpse is rejected. In an open server that already refills its containers, 20 minutes makes a static cache and removes the race.

## 4. Reanimation — the body keeps the loot

This is the rule that makes death physical.

- If the brain is intact, the body reanimates **10 seconds** after death. It becomes a standard infected NPC wearing the dead survivor's clothing and carrying their gear.
- The player returns to the hub. They do not control the body.
- The body is a **loot container on legs**: it cannot be looted while animate. Kill it and the loot drops at the new death spot as a normal corpse, with the same five-minute window.
- If the body despawns, falls out of the world, or the server closes, the loot is lost.
- The body is **leashed** to the death point: 80 studs. It can chase a target beyond the leash, but returns when it loses them. The loot stays findable.
- The body counts against the infected budget. Player bodies reserve slots; a 24-player server has at most 24 of them, and the dynamic budget absorbs them.
- It does not use the player's gun. It attacks as a standard infected. The gear is cargo, not a weapon.

## 5. Destroying the brain

- A **headshot kill** destroys the brain: no reanimation. The body stays a corpse.
- A **melee finisher to the head** during the 10-second window destroys the brain: no reanimation. 1.0 s, noise 15.
- A torso or limb kill leaves the brain intact: the body rises.
- No random chance. The rule is deterministic and readable.
- The double-tap is a real decision: loot now, or spend a second and a noise event to keep the body down.

## 6. Who can recover it

- The victim returns to the hub with clothing and a knife. No free gun, armour, ammunition or healing.
- **Late join is normal.** The victim can redeploy into the same server if it has space and the body has not despawned. The hub stores the last raid job id and offers a return to that district for the body timer.
- The victim's map and the party marker show the **death point**, not a live tracker.
- If the server is full, the victim cannot rejoin, but the squad is already inside and can still recover the body. If the body has despawned, it is gone.
- Re-entry is a knife run unless the vault holds a spare kit. The body is the prize.
- The squad dividend is speed; the solo cost is the knife run and the timer.
- The dog tag is on the body. The tag design is account-bound; the dropped tag is a trophy.

## 7. Disconnect and combat logging

- A deliberate leave, timeout, or disconnect during an active raid counts as death.
- The body drops at the last server-validated position. If the brain is intact, it reanimates.
- Re-entry returns the player to their hub state; the old body remains an NPC until its timer. No inventory rollback, no duplicate loot.
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
- Squads can recover a body without the victim; solos can return for it, but the window is the timer. The risk is real, and the squad is worth something.

## 10. Open questions

1. Corpse persistence: 5 or 8 minutes? 20 is rejected.
2. Reanimation delay: 8, 10, or 12 seconds?
3. Leash: 60, 80, or 100 studs?
4. Despawn: 10 minutes, or until server close?
5. Can the victim re-enter the same server? **Yes, if it has space and the body timer is live.** Add a redeploy cooldown if it feels too fast.
6. Does the secure container hold quest items? **Recommend yes.**
7. Does a reanimated body drop the dog tag, or is the tag on the corpse only? **Recommend on the body.**
