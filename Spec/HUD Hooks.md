---
tags: [nodawn, ui, spec]
updated: 2026-10-23
---

# HUD Hooks

Back to [[No Dawn - Home]] · Open item: the No Dawn UI pass ([[Open Items]])

What a main HUD (stamina, health and the rest) can read without touching gameplay code. The user is having ChatGPT build it; paste this note in as the brief's data section. All of it lives on the client already. The HUD only reads; the server owns every number.

## The local character (`Players.LocalPlayer.Character`)

| Read | Type | Meaning |
|---|---|---|
| `Humanoid.Health` / `Humanoid.MaxHealth` | number | Health. |
| attribute `Stamina` | number, 0–100 | Stamina, from the server (`Config.Movement.Stamina.Max` = 100). Use `GetAttributeChangedSignal("Stamina")`. |
| attribute `Load` | number, 0–1+ | Carried weight / carry capacity (above 1 is overloaded). |
| attribute `Stance` | "stand", "crouch" or "prone" | The accepted stance. |
| attribute `Lean` | "left", "right" or nil | Leaning around a corner (Q/E). |
| attribute `StaminaRegenScale` | number, 0.1–1 | How fast stamina recovers (hunger, thirst, stress slow it). |
| attribute `SurvivalAction` | "eat", "drink", "treat" or nil | An item use in progress. |

## Survival (`ClientState.view.survival`, owner only)

Read it through `Client.UI.SurvivalStatus.snapshot()`, which also lists the active moodles. Whole numbers; 100 is healthy for both meters.

| Field | Meaning |
|---|---|
| `Nutrition`, `Hydration` | 0–100. Warnings below 50, 25, 10; health only drops at 0. Not Hunger/Thirst. |
| `Stress` | 0–100; slows stamina recovery at 50 and 75. |
| `InjurySeverity` | 0–100, the sum of wound severities. |
| `Bleeding` | 0–100, % of the 3 HP/s cap. |
| `injuries` | `{ id, family, label, part, location, group, severity, state, bleed }` per wound. |
| `view.survivalAction` | The running use `{ token, kind, itemId, wound, seconds }`, or false. |

## Already on screen — don't duplicate or cover

- `PlayerGui.Hud`: money and carried weight, bottom left (BuilderSans, cream text `226, 222, 208`, money in amber `196, 152, 70`).
- The sprint vignette at the screen edges (MovementController).
- First person: the centre of the screen is the aim. Keep the centre clear; put bars at the edges.

## Rules

- No 2D detail painted onto the world ([[Design Rules]]); HUD panels are fine.
- Gamepad and touch must both work (the touch buttons Sprint and Crouch come from ContextActionService).
