---
tags: [nodawn, process]
updated: 2026-10-21
---

# Working Agreement

Back to [[No Dawn - Home]] · Related: [[Design Rules]], [[Lessons]]

Who builds what, so nothing gets bounced between tools twice.

## The split

| Work | Goes to | Examples |
|---|---|---|
| **Code** | Nyx (this session) | Luau modules, services, rules tables, tests, data config, `RemoteRouter`/`RequestGuard` wiring, Studio deploys through MCP, debugging |
| **UI code** | Nyx | Screens, HUD, menus, layout, interaction flow, UI animation driven by code |
| **Generative assets** | Claude | Meshes, textures, PBR material sets, decals, billboards, skyboxes, particle textures |
| **Front-end visual features** | Claude | Lighting looks, post-processing stacks, viewmodel presentation, shader-ish effects, scene dressing passes |
| **Animation assets** | Claude | Clip authoring and export, re-targeting, animation set assembly |

The line is simple: **if the deliverable is a file you import, it is Claude's. If the deliverable is a script, it is Nyx's.** UI is the exception — UI is a file Nyx writes, so it stays here even though it is front-end.

Nyx still owns the wiring after an asset lands: setting `MeshId`/`TextureId`, collision groups, attachment points, animation IDs, and the code that drives them.

## Handoff rules

A handoff prompt is a work order, not a conversation. Claude does not have the vault, the place, or the history, so the prompt has to be self-contained — but self-contained is not the same as long.

1. **Start with the deliverable.** No "I'm working on a Roblox game about…". If the first line is context, delete it.
2. **No preamble, no politeness, no rationale.** Claude does not need to know why the fire escape exists.
3. **One screen.** If it does not fit, the task is underspecified.
4. **Numbers, not adjectives.** "12 × 6 studs", "≤ 9,000 tris", "2048 texture set". Never "high quality" or "realistic".
5. **State the acceptance test.** Something checkable after import.
6. **Say what not to touch.** Prevents collateral edits.
7. **Ask for a manifest, not prose.** File list, tri counts, texture sizes. No commentary.

## Template

```
TASK: <one line>
TARGET: <Roblox Studio build / Blender version / other>
DELIVERABLE: <exact files>
SCALE: <studs and metres>
BUDGET: <tris, texture sizes, material count>
CONSTRAINTS: <pivots, orientation, modularity, naming>
ACCEPT:
  - <testable>
  - <testable>
OUTPUT: <files + manifest.txt; no prose>
DO NOT: <list>
```

## Worked example

```
TASK: Rusted steel fire escape module, three flights, for a 1970s Georgia motel.
TARGET: Blender 4.3, FBX export, Roblox Studio import.
DELIVERABLE: modular set — landing, stair flight, railing run, wall bracket.
SCALE: 1 stud = 0.28 m. Landing 12 x 6 studs (3.36 x 1.68 m). Stair rise 8 studs (2.24 m) per flight.
BUDGET: <= 9,000 tris total; one 2048 set (albedo, normal, roughness, metalness); no vertex colour.
CONSTRAINTS: separate meshes so railings can be removed; pivot on the wall-contact face; -Z faces the building; no baked lighting; no text or decals.
ACCEPT:
  - imports at 1:1, landing measures 12 x 6 x 0.2 studs
  - railing bars are 0.4 studs apart or closer, so the collision check passes
  - texture tiles at 1.0 stud without stretching on the stair stringers
OUTPUT: FBX + PNGs + manifest.txt with file, tris, texture sizes. No prose.
DO NOT: modify existing meshes, add signage, bake AO into albedo.
```

## When not to hand off

- Anything that is a Luau file, including UI.
- A visual change that is one property change in code (fog density, exposure, a colour). That is a script edit, not an asset.
- A bug in how something renders that is caused by code — collision, culling, attachment offset, animation priority.

## Current asset gaps

These are waiting on a handoff, not on code:

- Movement clips: walk, jog, sprint, crouch, lean, slide, vault, mantle, jump, land, breath. About 19 body clips and 8 first-person clips.
- Grapple: attach, swing loop, release. Three clips.
- Surface footstep sets and gear rustle. About 110 files.
- Stance-specific silhouettes for crouch and prone, once the clips exist.
