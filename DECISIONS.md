# DECISIONS — living log for "106.9 Presents: Stefan's Big Day"

> A running record of the choices we make and change as we build. Newest changes go at the top of the Change Log. The detailed rationale/creative lives in `docs/Stefan_Game_Design_Plan.md`; the technical spec in `docs/Stefan_Game_Implementation_Plan.md`. This file is the quick, always-current "what we decided and why."

---

## Locked decisions (current truth)

**Platform & delivery**
- Single self-contained `stefan.html` + optional `assets/` folder. Runs offline by double-click. USB/email friendly.
- Vanilla HTML/CSS/JS, no build step. Pixel rendering (`image-rendering: pixelated`).
- Story lives in a readable `STORY` data object, separate from engine code (Twine-like editability).

**Story shape**
- Title: **106.9 Presents: Stefan's Big Day**.
- Shared cozy **prologue** → one fork: **go to work (Branch 1)** or **call out (Branch 2)**.
- **Branch 1 — Invasion at Foulk:** aliens hiding among residents at the old folks home; Stefan's scream + handiness save the day. 4 endings (1A Visions/Scream, 1B Co-op w/ Greg, 1C The Reveal—aliens retiring, 1D Lived to Tell It).
- **Branch 2 — The 106.9 Riddle Hunt:** radio contest; game/anime riddle → DDR minigame → finale. Grand prize = Scarpenters opens for August Burns Red. 4 endings (2A Champions, 2B Two of Us, 2C Second Place/Best Day, 2D Penny Goes Viral).
- Recurring **106.9 M&S DJ narrator** between beats.

**Gameplay**
- Illustrated-story + hybrid interactive touches.
- **DDR minigame** (Branch 2 centerpiece): keyboard ↑↓←→ / WASD, Perfect/Good/Miss timing, combo + score bar, clear threshold; two charts (hard = solo run, easy = co-op); Penny wildcard.

**Audio**
- Retro chiptune via Web Audio API (no external files for prototype). Music per scene + SFX (blip, confirm, transition, stinger, meme stings). Always-visible mute toggle.

**Art**
- Start with placeholder art (labeled colored blocks); swap in Maria/Stefan's Procreate PNGs later by filename, no code changes.

**Canon / personalization** — see `docs` for the full cast sheet. Key: Stefan (blonde, all black, Scarpenters singer, Foulk maintenance, KH/FF7/DDR, One Piece/AoT, Eagles, Nanu's Chicken, Wilmington DE). Greg ("eat yo salad before I toss yo salad"). Maria (met on music app, watches AoT). **Penny = male tabby**, naughty. Bands: August Burns Red, Linkin Park.

**Repo & workflow**
- Repo: `github.com/dev-celeste/stefy-game`, local at `~/Desktop/GitHub/stefy-game`.
- Claude makes commits locally in that folder; **Maria pushes** (`git push`) since Claude can't authenticate to GitHub.
- Delete permission enabled for the `kitty` folder so git can manage its own lock files.

---

## Change Log

### 2026-07-17 — Stage 10: sound (finale) — game feature-complete
- Built a **Web Audio sound engine** — everything synthesized with oscillators, **no audio files**, so the game stays a single self-contained HTML file.
- Music: six chiptune loops (cozy / tense / hunt / arcade / win / soft) written in a tiny `seq("C4:2 E4:2 …")` shorthand; scenes carry an optional `music` field and `setMusic` only switches when the mood actually changes (shared-mood scenes don't restart the song).
- SFX + 106.9 meme stings (confirm blip, DDR hit/whiff, vine-boom, airhorn, "OHHH", triumphant stinger), triggered data-driven via an optional `sting` field on scenes, plus a confirm blip on choices and hit/whiff blips inside the DDR game.
- **Autoplay handled correctly:** the AudioContext is created lazily on the first user click (browsers block audio before a gesture). Always-visible **mute toggle** pinned top-right.
- Verified headlessly: SFX silent before unlock, graph builds on init, mood switching (no same-track restart), stinger note count, and mute zeroing the master. Story graph still clean (21 scenes, 8 endings, 0 problems).
- **All ten build stages are done — the game is feature-complete.** Remaining is optional polish: swap placeholder blocks for Procreate art, richer looping music, a branded title screen.

### 2026-07-17 — Stages 8–9: DDR minigame + 106.9 DJ narrator
- **Stage 8:** built a canvas-based DDR minigame wired into the data-driven engine — a choice can carry `minigame: {chart, clear, fail}` instead of `next`, and the game routes to the clear/fail scene by hit percentage. Two charts (hard = solo, easy = co-op), Perfect/Good/Miss timing windows, combo + score HUD, arrow-key/WASD input. Verified with a headless harness (perfect run → end_2A; no play → end_2C).
- **Stage 9:** added a recurring **106.9 M&S DJ narrator** as an optional `dj` string on scenes, rendered as an "ON AIR" radio caption above the story text (appears instantly while the story text still types). 16 DJ lines across the big beats (prologue, the fork, both branch openers, the DDR scenes, and all 8 endings). Pure data + one `showDJ` render function — engine philosophy preserved.
- Only Stage 10 (sound finale) remains.

### 2026-07-17 — Stages 2–7 built; full story content in
- Engine grew stage by stage: data-driven scene rendering (2), choices + navigation and a bigger screen via `transform: scale(1.25)` keeping internal 640×480 (3), typewriter effect (4), click-to-skip (5), layered art stage with BG/CHAR palettes + placeholder character blocks (6).
- **Stage 7:** wrote the entire story graph — prologue, morning fork, Branch 1 and Branch 2, all 8 endings — plus an ending/`Play again` restart mechanic. Branch 2's DDR step is a **placeholder choice** until the dedicated minigame stage.
- Validated: 21 scenes, 8 reachable endings, no dead links/orphans.
- Remaining planned stages: DDR minigame → DJ narrator → sound (finale).

### 2026-07-15 — Switched to a staged, learn-as-we-build approach
- Maria (junior dev) wants to understand the codebase thoroughly, so we're **building from scratch in small, explained stages** (deep, line-by-line explanations) instead of one big prototype.
- The full prototype was set aside as a private reference (kept out of the repo) to keep each stage consistent with the target architecture.
- Workflow per stage: build a small increment → explain it → commit it → Maria pushes.

### 2026-07-15 — Repo setup + prototype build begins
- GitHub repo created and connected; initial commit with docs, README, .gitignore.
- Resolved git lock-file issue in the mounted folder (deletion enabled for `kitty`).
- Established workflow: Claude commits, Maria pushes.
- Starting Phase 1 prototype: prologue + morning fork into both branches + a playable DDR slice.

### 2026-07-14 — Design locked
- Locked both branches, all 8 endings, DDR keyboard minigame, chiptune sound, DJ narrator.
- Branch 2 reworked from a generic "contest of dares" into the **106.9 Riddle Hunt** (game/anime riddle + DDR gate) so it mirrors Branch 1's "Stefan's own skills win the day" theme.
- Settings: workplace = **Foulk** (old folks home); Branch 1 twist = some residents are aliens; opening radio is literally on 106.9 M&S. Penny confirmed male.

---

## Open items (to decide during build)
- Exact Challenge 1 riddle wording (KH + One Piece) and which Wilmington landmark it decodes to.
- Which Scarpenters song becomes the DDR chiptune chart (default: "Visions").
- Placeholder → real-art swap schedule (Procreate pieces).
- Whether to add a title-screen radio-dial animation in polish.
