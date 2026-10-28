# No Dawn — vault conventions

Design and build record for **No Dawn**, a Roblox survival extraction shooter. The place itself is the
source of truth; this vault is the written record of it, versioned at
[github.com/troubledkiller69-lgtm/nodawn](https://github.com/troubledkiller69-lgtm/nodawn).

Start at [[No Dawn - Home]]. Everything else hangs off that page.

## The one rule that outranks the rest

**There is one live list: [[Open Items]].** Finished work leaves it. It does not accumulate.

When a live note and an old one conflict, the live note wins. The live notes are: this vault's
`No Dawn - Home`, `Open Items`, `Build/Hub - Build History`, and `Records/Realism Ledger`. Everything
else that looks like status is history.

## Where a thing goes

| It is | It lives in | Shape |
|---|---|---|
| An idea, not yet decided | `Brainstorm/Idea - <name>.md` | `status: raw \| shaping \| decided \| dead` |
| A question settled | `Spec/<name>.md` | frontmatter, nav line, rules table |
| A handoff to Claude | `Spec/Work Order - <name>.md` | the work-order block from [[Working Agreement]] |
| Something that happened | `Records/<name>.md` | what, numbers, gate results, captures |
| A build pass | `Build/Hub - Build History.md` | append to the existing entry for that date |
| A gotcha | `Lessons.md` | one bullet, bold lead-in, the fix |
| A still-open job | `Open Items.md` | a checkbox under the right heading |
| A picture | `Screenshots/<stage>/` | bare filename, with a width |

Never create a second status page. If you need a new heading, add it to the page that already owns
that subject.

Before adding a note, search for the existing subject owner and update it. Keep source links and video
observations inside the research note they support unless they need an independently maintained subject.
A new pass on the same topic is not a new note by default. When consolidating, preserve unique material,
make the maintained destination explicit, and archive superseded copies with a forwarding banner.
Requirements, proposals, implementation records and live tasks have different roles; linking them is
preferable to repeating their contents.

## Note shape

Live notes carry frontmatter and a nav line:

```markdown
---
tags: [nodawn, spec]
status: shaping
updated: 2026-10-20
---

# Note Title

Back to [[No Dawn - Home]] · Related: [[Design Rules]], [[Working Agreement]]

```

- `tags` — the first tag is the note's role: `nodawn` for project notes, `idea` for brainstorm,
  `reference` for source packs, `reconciliation` for comparison passes, `workorder` for handoffs.
  Then domain tags: `combat`, `movement`, `raids`, `survival`, `economy`, `map`, `detroit`.
- `status` — only on ideas, work orders and reconciliation passes. `raw`, `shaping`, `decided`, `dead`,
  or `in-progress` for a work order.
- `updated` — the date the note last changed, `YYYY-MM-DD`. Update it when you touch the note.
- The nav line sits directly under the H1. `Back to [[No Dawn - Home]]` on project notes;
  `Back to [[Brainstorm - Index]]` on new idea notes. Separate several links with `·`.
- Older idea notes predate the back-link and are reached from [[Brainstorm - Index]] instead. Leave
  them that way unless the note is being rewritten anyway — they are live, not frozen, but a nav
  line is not worth a diff.

## File naming

`Type - Title.md`, title case, no dates in the filename. `Idea - Monetisation.md`,
`Hub - Build History.md`, `Weapon - M1911A1 Build Spec.md`.

Keep the H1 close to the filename. Historical documents keep their original ALL-CAPS or explanatory
H1 — that is the record, and it does not get rewritten.

## Frozen records

When a document stops being the truth, it stays in the vault and gets a banner under its H1:

```markdown
> **Superseded, 2026-10-20.** What changed and what replaced it.
```

Variants in use: `**Superseded.**`, `**Superseded <date> by [[Other Note]].**`,
`**Context snapshot, written before <phase>.**`, `**Partly obsolete.**`

A frozen note is not edited afterwards, and does not get frontmatter. It is a record of what was
true at the time. Do not "fix" numbers in a frozen note — supersede it instead.

## Images

- Live in `Screenshots/`, in a subfolder per stage or pass (`stage4`, `realism-v2`, `realism-v3`).
- Embed with the bare filename and a width, like `![[bus_front_three_quarter.jpg|600]]`.
- They are binary. Never let an editor re-save them, and never hand-edit them.

## Wiki links

- `[[Note Title]]` for a note, `[[Note Title|short label]]` when the title is long.
- `[[Folder/Note Title]]` where the bare name would be ambiguous.
- Link the first mention. A second mention of the same note in the same note does not need a link.

## Working agreement for the tools

Code and UI are Nyx's. Meshes, textures, animation clips and front-end visual features are Claude's,
handed over as a work order. The rule: **if the deliverable is a file you import, it is Claude's; if
it is a script, it is Nyx's.** Full format in [[Working Agreement]].

## Build rules that never bend

These are decisions already made and closed. Reopening one needs an explicit instruction, not a
suggestion.

- No text anywhere. All 71 signs were removed on 2026-10-17.
- No painted-on 2D detail. Anything with depth in the real world is 3D geometry or terrain.
- Real scale. 1 stud = 0.28 m. A bus is ~36 studs, a pickup ~17.6.
- Always night. Foggy and rainy, day-for-night rendering. No day/night cycling.
- Never hand-edit `Workspace.Hub`. Change HubBuilder and rebuild.
- Saving and publishing the place are human actions. Scripts cannot do either.

## Git

The repo is the vault. `obsidian-git` commits every 10 minutes and pushes every 2; it pulls on boot.

- Commit message: `vault backup: YYYY-MM-DD HH:mm:ss` (the plugin default).
- One editor per note at a time. The plugin merges, but Markdown merges are line-based and ugly.
- Never commit credentials. The plugin uses the system git credential manager.
- `.obsidian/workspace.json` is ignored — each machine keeps its own layout. Everything else in
  `.obsidian/` is shared, so the vault behaves the same everywhere.

## Obsidian setup on this machine

The vault is at `C:\Users\Lando\Documents\Obsidian Vaults\No Dawn`.

- Templates: `Templates/`. Daily notes: `Daily/`, format `YYYY-MM-DD`, template `Templates/Daily Note`.
- New notes default to `Brainstorm/`. Attachments default to `Screenshots/`.
- New links are written in shortest-path form, matching the vault's existing style.
- Dark theme, tags and attachments shown in the graph, colour-grouped by folder.

`Daily/` is a scratchpad. A daily note never holds a decision — it gets triaged into the table at
the top of this file before the day closes.
