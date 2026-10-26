---
tags: [nodawn, combat, infected, animation]
updated: 2026-10-26
---

# Combat - Weapon and Walker Pass

Back to [[No Dawn - Home]] · Related: [[Realism Ledger]], [[Open Items]]

Installed in the open hub place, 119649394550657. User approved Studio playtesting. Studio was returned to Edit; saving/publishing remain human actions.

Recovered Claude session `3e3d0bf6-9fce-4b74-8f71-b0c2dc569408`: last gun solver was compiled but never playtested. An unanchored acceptance check exposed assembly movement from direct arm CFrame writes. Replaced that path with six anchored visual arm parts, cloned outfit, retained disabled rig topology for clothing, calibrated grips, sensible eye relief and camera-space presentation shoulders. Native torso/legs/head/locomotion remain intact; menus/traversal/prone restore original arms; holster/death remove the visual models. First-person body offsets keep locomotion/lean rather than the shotgun's third-person torso twist.

Four calibrated weapon holds plus nine handling clips are in `ServerStorage.Animations.ND_Weapons_v1`. `Shared.Config.WeaponAnimationClips`, `WeaponAnimationSampler` and client `WeaponAnimationPlayer` provide published-client playback without uploading AnimationIds. Complete native clip sets remain supported; partial uploads use the data fallback. WeaponController/FirstPersonView/WeaponRig/WeaponService and ClientMain are wired. The existing M1911A1 and pump models are retained.

Eight infected variants now use a generated gaunt sculpted head, muted skin, varied slow Drifter pace, gradual turns, asymmetric shuffle/reach, bounded wander and actual last-seen searching. Runner remains configurable. Safe-zone/damage checks remain in place. Active sculpt: model83482735732704, mesh89418274753195, texture135545416216481. Head/hair cage and queryable native head remain; fallback decal125532216144100 is retained.

Checks: live gun walking/crouch/lean/prone/aim/inspect/menu/holster and safe-zone refusal; 72 final camera/stance/handling combinations (max hand error9.75e-7stud, ADS angle2.63e-6degrees, no native body changes); 13clips359frames and 39fresh-rig Animator samples; 4,368finite infected poses; isolated fake-target Server chase/search/memory/turn/attack/wall/safe-zone behavior passed with cleanup. Full existing suite in Edit and Server:204/205. Remaining failure is pre-existing missing eyewear in the v1 appearance migration. Third-party plugin errors remain.

Source/artifact package: `C:/Users/howar/Documents/Codex/2026-10-26/1-https-www-tiktok-com-t/outputs/README.md`. Runtime source snapshot, editable animation XML, generator/validators, infected provenance and actual review captures are alongside it. This is not a complete place backup.

Recovery: `ServerStorage.NoDawnBackups.WeaponsBeforeController_20261026`, `WeaponsBeforeHandling_20261026`, `AnimationIdsBeforeWeapons_20261026`; `ServerStorage.DevTools.InfectedV2Backup_20261026_Walkers`, `InfectedHeadBackup_20261026`, `NoDawnAnimationFallbackBackup_20261026`.

Remaining: exact reference matching (all three Sizfi TikTok links hit login/age gate), second-client replication, sustained combat outside hub, terrain foot contact and subjective feel. Catalog revolver38 still has no weapon model/definition. Movement ID upload remains an older separate task. No place save/publish was performed.
