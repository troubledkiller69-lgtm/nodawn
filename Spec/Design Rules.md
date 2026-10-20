---
tags: [nodawn, rules]
updated: 2026-10-18
---
# Design Rules (standing preferences)

Back to [[No Dawn - Home]]

| Rule | Why / how |
|---|---|
| **Realistic, never blocky or toy-like** | Greybox is only a layout step. Props and vehicles need real silhouettes and detail. Use generated meshes for complex man-made props, parts only for simple structure. Visual reference: the West Georgia prison from *The Walking Dead*. |
| **Real-world scale** | 1 stud ≈ 0.28 m. Check sizes against real objects (a bus is about 36 studs long, a pickup about 17.6). |
| **No signs or text anywhere** | All 71 signs were removed on 2026-10-17. That covers wayfinding, labels, licence plates and destination boards. Ask before adding any back (even an easter egg). Vendors and the gate are identified by NPCs, layout and prompts. |
| **No painted-on 2D detail** | Anything with depth in real life (gravel, rubble, bricks, cracks, grass, debris) is built in 3D: terrain or real meshes. Flat textures only for flat things (paint, stains, posters, floor tiles). |
| **Ground is terrain; no pebble scatter** | Terrain dirt, mud and grass were liked. A 2,000-piece rock scatter was rejected. A few big boulders are wanted. |
| **Always night** | A permanent foggy, rainy night (day-for-night rendering). Don't bring back day/night or weather cycling unless asked. |
| **A populated hub** | Fill it with people going about their lives: the med bay, courtyard, camp and farm all have NPCs. |
| **"Too much going on"** | Earlier feedback: declutter the exterior and give the yard a clear focal hierarchy (vendors, lockers, gate first). Still to do. |
| **The game is called No Dawn** | A UI pass to fit the name is wanted (not started). |

How work is divided: code and UI are Nyx's; meshes, textures, animation clips and front-end visuals go to Claude as a work order ([[Working Agreement]]). Studio captures and camera restore still apply ([[Lessons|Lessons]]).
