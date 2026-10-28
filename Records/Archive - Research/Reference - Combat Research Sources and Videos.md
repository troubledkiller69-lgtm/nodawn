# Reference - Combat Research Sources and Videos

> **Superseded, 2026-10-28.** Consolidated into [[Reference - First-Person and Gun Research]]. Historical research retained verbatim below; this copy is not maintained.

Back to [[No Dawn - Home]] · Research hub: [[Reference - First-Person and Gun Research]]

Compiled 2026-10-28. Primary sources take priority. “Read” means the relevant available page/text was retrieved, not that every linked asset was inspected. Video metadata is not footage review. No timestamps or transcript claims are invented.

## Roblox technical sources

| ID | Source | Evidence reviewed and use |
|---|---|---|
| R1 | [RunService API](https://create.roblox.com/docs/reference/engine/classes/RunService) | Read relevant render binding and animation/simulation sections; ordering reference |
| R2 | [Motor6D official documentation source](https://raw.githubusercontent.com/Roblox/creator-docs/main/content/en-us/reference/engine/classes/Motor6D.yaml) | Read Transform timing and replication tags; especially useful because rendered API page exposed less explanatory text |
| R3 | [Camera API](https://create.roblox.com/docs/reference/engine/classes/Camera) | Read API properties; near-plane and camera coordinate reference |
| R4 | [IKControl API](https://create.roblox.com/docs/reference/engine/classes/IKControl) | Retrieved chain/target setup requirements |
| R5 | [Inverse kinematics overview](https://create.roblox.com/docs/animation/inverse-kinematics) | Retrieved overview; procedural pose context |
| R6 | [Client-server boundary](https://create.roblox.com/docs/scripting/security/client-server-boundary) | Read validation guidance; authority reference |
| R7 | [Raycasting](https://create.roblox.com/docs/workspace/raycasting) | Read direction/range, filtering and streaming caveat |
| R8 | [Hit detection with lasers](https://create.roblox.com/docs/tutorials/use-case-tutorials/scripting/intermediate-scripting/hit-detection-with-lasers) | Read camera target, weapon ray and server validation example |
| R9 | [Introducing shapecasts](https://devforum.roblox.com/t/introducing-shapecasts/2320655) | Roblox announcement retrieved; historical behavior needs current API verification |
| R10 | [Studio testing modes](https://create.roblox.com/docs/studio/testing-modes) | Read multi-client/device testing guidance |
| R11 | [Network Simulator](https://create.roblox.com/docs/studio/network-simulator) | Read independent directional impairments and repeatable comparison workflow; feature availability may vary with Studio version/beta settings |
| R12 | [MicroProfiler walkthrough](https://create.roblox.com/docs/performance-optimization/microprofiler/use-microprofiler) | Read diagnostic walkthrough; investigate frame spikes rather than guessing |
| R13 | [Acoustic simulation release](https://devforum.roblox.com/t/full-release-acoustic-simulation-emit-audio-with-presence/4307121) | Read announcement with September 22, 2026 update and quality caveat |
| R14 | [AudioListener API](https://create.roblox.com/docs/reference/engine/classes/AudioListener) | Retrieved acoustic simulation property requirements |

## Developer and manufacturer sources

| ID | Source | Evidence reviewed and use |
|---|---|---|
| D1 | [Bungie weekly update, March 26, 2015](https://www.bungie.net/7/en/News/article/12707/undefined) | Search-indexed developer text retrieved; direct page fetch timed out. Limited first-person animation framing only |
| D2 | [GDC: The Art of First Person Animation for Destiny](https://www.gdcvault.com/play/1022298/The-Art-of-First-Person) | Session abstract and speaker identity reviewed; full talk not watched |
| D3 | [Valve: The AI Systems of Left 4 Dead](https://steamcdn-a.akamaihd.net/apps/valve/2009/ai_systems_of_l4d_mike_booth.pdf) | Official slide text retrieved; pacing section inspected, especially slides 77–91 |
| D4 | [DayZ update 1.29](https://dayz.com/article/game-update/dayz-update-129) | Official article retrieved; distant identification and audio consistency |
| D5 | [Project Zomboid B42 feature overview](https://projectzomboid.com/blog/upcoming-features-b42/) | Retrieved adaptive-music discussion; historical feature page, not current build-status authority |
| D6 | [Project Zomboid: Reinforcing the Barricades](https://projectzomboid.com/blog/news/2026/05/reinforcing-the-barricades/) | Official post retrieved; magazine/item/condition synchronization bugs |
| D7 | [Colt 1911 Classic](https://www.colt.com/detail-page/classic/) | Product page retrieved; accessible image description supplies 7-round .45 and 5-inch barrel reference. Modern variant, not a complete military-pattern reference |
| D8 | [Mossberg pump-action manual](https://resources.mossberg.com/hubfs/manuals/103855-Pump-Action-Firearms-002.pdf) | Manufacturer manual excerpt retrieved on model/shell-dependent capacity. Does not identify No Dawn's existing pump |

The Colt page's linked manual returned 404 during this pass. Do not cite that inaccessible manual as reviewed. The Watch_Dogs “In Your Hands” slides were discovered but the PDF exceeded the browser fetch limit; no detailed implementation claims are based on them. Several third-party guide/wikis appeared in searches; they were excluded from technical authority.

## YouTube study queue

Four direct videos collected. **Updated 2026-10-28:** in-app browser playback worked; selected timestamped frames, on-screen code and short live-caption excerpts were reviewed. See [[Reference - Video Review - First Person and Guns]] for exact observations. This was not a full uninterrupted watch. Transcript panels did not load, and audio was not assessed. The table below retains the original study questions.

| Order | Video | Creator / date where available | What to extract during an actual viewing |
|---|---|---|---|
| 1 | [The Art of Destiny's First-Person Animation](https://www.youtube.com/watch?v=CXFLu8cityA) | David Helsby / GDC session, 2015; YouTube URL resolved from the Game Anim embed | How arms, weapon and camera communicate weight and responsiveness; distinguish principles from Roblox-specific implementation |
| 2 | [Roblox FPS Tutorial Part 1 (Viewmodel)](https://www.youtube.com/watch?v=kFpvVTD9-jg) | Ampro, 2022-12-23 | Rig hierarchy, root choice and local presentation; compare against current APIs and No Dawn's visible-body requirement |
| 3 | [Roblox FPS Tutorial Part 2 Shooting, Replication, Recoil](https://www.youtube.com/watch?v=lLxCUNRdiDw) | Ampro, 2023-01-06 | Which effects run locally, what state replicates and how recoil composes; independently review authority/validation |
| 4 | [DayZ Update 1.29 Weapon Audio Comparison](https://www.youtube.com/watch?v=EAJLaW5KDc0) | Official DayZ channel, 2026-04-08 | Compare close/distant sound identity and environment tails; reference listening only, not reusable audio assets |

The Destiny YouTube URL was discovered via [Game Anim's talk link](https://www.gameanim.com/2017/04/03/art-destinys-first-person-animation/); the matching session is independently documented by GDC. The initial web fetches yielded a generic shell, errors or throttling. The later browser visual review supplies the timestamped observations in the linked review note; those findings come from displayed video content rather than title/description inference.

## Study capture form

For each video or technical article studied further, record:
- Exact title, creator, publication date, URL and access date.
- Evidence reviewed: complete video, timestamped segment, transcript, description or source code.
- One specific mechanism and a plain-language explanation.
- Where it maps onto No Dawn's actual controller/service.
- Assumptions: engine, rig type, coordinate space, replication model.
- A small experiment that could falsify the proposed application.
- Limitations, rights and whether any assets are placeholders.

Do not copy tutorial code wholesale to replace a working subsystem. Reproduce one principle in isolation and compare against current official documentation. A successful demonstration in an old R6 tutorial is not evidence that it handles No Dawn's R15 body, prone, lean, clothing or authoritative inventory.
