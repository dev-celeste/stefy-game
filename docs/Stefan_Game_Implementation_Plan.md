# 106.9 Presents: Stefan's Big Day — IMPLEMENTATION PLAN
### Self-contained technical spec (built to survive context loss)

> **Purpose of this doc:** capture *everything* needed to build the game from scratch, so any future session — even with zero prior context — can resume and finish it. Read this together with the companion design doc `Stefan_Game_Design_Plan.md` (story/creative). This doc = the *how*.

---

## 0. TL;DR for a fresh session

You are building a **pixel-art choose-your-own-adventure game** as a personal gift for a man named **Stefan**. It is **one self-contained HTML file** (`stefan.html`) plus an `assets/` folder (images + optional audio), runs offline by double-click, and is easy to put on a USB drive. Story = a cozy morning that splits, on one choice, into **Branch 1 (alien invasion at Foulk, the old folks home)** or **Branch 2 (a 106.9 M&S radio riddle-hunt contest)**, each with sub-choices and **4 endings**. Branch 2 contains a **keyboard-playable DDR minigame**. Retro Pokémon/GBA-style **chiptune music + SFX** with a mute toggle. Art starts as **placeholders** and is swapped for the user's own Procreate PNGs later.

**Current status:** Design LOCKED. Next deliverable = **Prototype (Phase 1)**.

---

## 1. Goals & hard constraints

1. **Single-file, offline, portable.** Must run by double-clicking `stefan.html` in any modern browser with no server, no internet, no install. USB/email friendly.
2. **Personal gift.** Every detail should feel made for Stefan (see Cast in §2).
3. **Easy to edit later.** Story content lives in a readable `STORY` data object, separate from engine code, so scenes/text can be edited without touching game logic — Twine-like.
4. **Swappable art.** Reference images by filename; start with generated placeholders, swap for Procreate PNGs later with no code changes.
5. **Retro feel.** Crisp pixel rendering, chiptune music/SFX, text that types out with blips, a 106.9 DJ narrator between beats.
6. **Small scope, complete.** 2 branches, 8 endings, 1 minigame. Keep it tight and replayable.

**Portability note on audio:** to keep it truly single-file/USB-proof, prefer **Web Audio API generated chiptune** (no external files) for the prototype. If richer looping tracks are added later, embed them as base64 data URIs or ship a small `assets/audio/` folder alongside the HTML.

---

## 2. Cast & canon (personalization reference)

- **Stefan (player):** fair skin, blonde hair, brown eyes, all-black outfit. Maintenance man at **Foulk** (old folks home). Lead singer of metal band **Scarpenters** (songs: **"Visions"**, **"Burdened by Weight"**); his scream is canonically a weapon. Loves video games (**Kingdom Hearts**, **Final Fantasy VII**, **Dance Dance Revolution**) and anime (**One Piece**, **Attack on Titan**). Films metal covers. From **Wilmington, DE**. Die-hard **Philadelphia Eagles** fan. Favorite food: pizza + chicken parm. Favorite spot: **Nanu's Chicken**. Favorite bands: **August Burns Red**, **Linkin Park**.
- **Greg:** best friend/coworker at Foulk. Goofy, funny, quick to anger. Catchphrase: **"Eat yo salad before I toss yo salad."**
- **Maria:** girlfriend. Met Stefan on a music-lovers' dating app. Watches **Attack on Titan** with him. Co-solver in Branch 2.
- **Penny:** the tabby cat, **he/him**. Sweet but always up to something naughty. Chaos wildcard.
- **106.9 M&S:** their fake-but-treated-as-real radio station that plays viral millennial meme sounds. The **DJ** is a recurring narrator/comic-relief voice throughout the game.

---

## 3. Tech stack & rendering

- **HTML5 + vanilla JavaScript + CSS.** No build step, no frameworks required. (Optional: a single `<canvas>` for the DDR minigame; the rest can be DOM-based.)
- **Rendering:** pixel scenes shown in a fixed-aspect "screen" (e.g. 640×480 or 320×240 scaled up). Use `image-rendering: pixelated;` on all sprite/background images and on the canvas for crisp pixels. Scale the whole game screen responsively with CSS transform while keeping integer-ish scaling where possible.
- **Layout:** a centered "game window" with (a) a **scene layer** (background image), (b) a **character layer** (sprite[s] positioned over the background), (c) a **text box** at the bottom (dialogue/narration with typewriter effect), (d) a **choice menu** (buttons), and (e) a persistent **top bar** (title + mute toggle).
- **Fonts:** a pixel/monospace web-safe stack, or an embedded pixel font (base64) for consistency. Avoid external font CDNs to stay offline.

---

## 4. File / folder structure

```
StefanGame/
├── stefan.html          ← the whole game (engine + story data + styles + minigame)
├── assets/
│   ├── img/
│   │   ├── bg_apartment.png
│   │   ├── bg_foulk_break.png
│   │   ├── bg_foulk_rec.png
│   │   ├── bg_foulk_invasion.png
│   │   ├── bg_street.png
│   │   ├── bg_barcade.png
│   │   ├── bg_nanus.png
│   │   ├── bg_stage.png
│   │   ├── char_stefan.png
│   │   ├── char_stefan_scream.png
│   │   ├── char_greg.png
│   │   ├── char_maria.png
│   │   ├── char_penny.png
│   │   ├── char_resident.png
│   │   ├── char_alien.png
│   │   └── icons/ (salad, wrench, mic, radio, phone, pizza, treat, ufo, arrows)
│   └── audio/ (optional; else Web Audio generated)
└── README.txt          ← how to run + how to swap art (for the user)
```

Placeholders: generate simple colored pixel blocks/labels for each filename above so the game is fully playable before real art exists. Swapping = drop a same-named PNG into `assets/img/`.

---

## 5. Data model (the STORY schema)

The entire narrative is a single `STORY` object of scene nodes keyed by ID. Engine reads the current node, renders it, and follows `choices` to the next node ID. Keep this human-editable.

```js
const STORY = {
  "start": {
    bg: "bg_apartment",
    chars: ["char_stefan"],        // sprite filenames (positions optional)
    music: "theme_cozy",
    dj: "Gooood morning, Wilmington! You're locked into 106.9 M&S...", // optional DJ narrator line
    text: [                         // array = sequential typed pages
      "You're Stefan. The alarm's been snoozed twice.",
      "Penny is committing a crime in slow motion on the shelf.",
      "Your work phone buzzes — Foulk needs you today."
    ],
    choices: [
      { label: "Save the cup", next: "morning_cup" },
      { label: "Let Penny win", next: "morning_cup" }
    ]
  },
  "morning_choice": {
    bg: "bg_apartment", chars: ["char_stefan","char_maria"], music: "theme_cozy",
    text: ["Maria: 'You working today, babe?'"],
    choices: [
      { label: "Yeah, I'm heading in to Foulk.", next: "b1_clockin" },   // Branch 1
      { label: "Nah... calling out. Let's have a day.", next: "b2_hunt" } // Branch 2
    ]
  },
  // ... Branch 1 nodes (b1_*), Branch 2 nodes (b2_*), endings (end_*) ...
};
```

**Node fields:**
- `bg` (string) — background image key
- `chars` (array of strings) — character sprite keys to overlay (optional positions later)
- `music` (string) — music track key; engine crossfades on change
- `dj` (string, optional) — a 106.9 DJ narrator line shown as an interstitial
- `text` (array of strings) — typed sequentially; player advances pages
- `choices` (array of `{label, next, flag?}`) — buttons; `flag` optionally sets a state flag
- `minigame` (string, optional) — e.g. `"ddr_hard"` / `"ddr_easy"`; engine launches it, then routes to `onWin`/`onLose`
- `ending` (bool/string, optional) — marks a terminal node + which ending ID, shows replay button
- `sfx` (string, optional) — one-shot sound on entering the node (e.g. `"stinger"`)

**State flags:** a small `state = {}` object tracks path choices (e.g. `state.route = "solo" | "team"`, `state.pennyChaos = true`) so the finale can pick the right ending. Keep it minimal.

---

## 6. Full scene graph (build-ready node list)

IDs the engine will use. Text is summarized here; final prose lives in the design doc / to be written during build. **P = prologue, B1 = branch 1, B2 = branch 2, E = ending.**

### Prologue
- `start` → warm-up cup gag → `morning_cup`
- `morning_cup` → Penny knocks something anyway, laugh → `morning_choice`
- `morning_choice` → **THE fork** → `b1_clockin` OR `b2_hunt`

### Branch 1 — Invasion at Foulk
- `b1_clockin` — arrive, Greg + salad joke; things feel off → choices → `b1_investigate` / `b1_toolup`
- `b1_investigate` — residents drop the disguise, aliens revealed → choices → `b1_talk` / `b1_fight`
- `b1_toolup` — barricade + improvise with Greg → choices → `b1_soundcannon` / `b1_hide`
- `b1_talk` — try to connect → routes to `end_1A` (if performs) or `end_1C` (if empathizes)
- `b1_fight` — wrench + gamer combos w/ Greg → `end_1B`
- `b1_soundcannon` — rig PA + mic → `end_1A`
- `b1_hide` — sneak real residents out, wait it out → `end_1D`
- Endings: `end_1A` (Visions/Scream hero), `end_1B` (Co-op Mode), `end_1C` (The Reveal — aliens just retiring), `end_1D` (Lived to Tell It)

### Branch 2 — The 106.9 Riddle Hunt
- `b2_hunt` — cozy morning, DJ announces the hunt, grand prize = open for August Burns Red; first clue on air → `b2_riddle`
- `b2_riddle` — Challenge 1, the KH/One Piece riddle → choices → set `state.route` → `b2_ddr_intro`
  - "Gamer instinct (Kingdom Hearts read)" → `state.route="solo"`
  - "Maria's anime angle (One Piece read)" → `state.route="team"`
- `b2_ddr_intro` — arrive at barcade, DDR machine → choices pick chart → launch minigame
  - "Flawless solo run" → `minigame: ddr_hard` → onWin `b2_finale`, onLose `b2_finale` (still funny)
  - "Co-op with Maria" → `minigame: ddr_easy` → onWin `b2_finale`
  - (Penny wildcard event inside minigame can set `state.pennyChaos=true`)
- `b2_finale` — the last dash; pick ending from state:
  - `route==="solo"` & DDR cleared clean → `end_2A`
  - `route==="team"` → `end_2B`
  - chose funniest/chaotic path or low DDR score → `end_2C`
  - `state.pennyChaos` triggered big → `end_2D`
- Endings: `end_2A` (Champions of 106.9), `end_2B` (Two of Us), `end_2C` (Second Place, Best Day), `end_2D` (Penny Goes Viral)

### Shared
- Every `end_*` node shows the ending title + art + jingle + **"Play Again"** (back to `start`) and **"See other endings"** (back to `morning_choice`).

---

## 7. DDR minigame spec

- **Container:** a `<canvas>` (e.g. 320×480) layered inside the game screen; pauses the story engine while active.
- **Note chart data:** an array of `{time_ms, lane}` where lane ∈ {left, down, up, right}. Two charts: `ddr_easy` (sparse, slower) and `ddr_hard` (denser, faster). Store as plain arrays in the file.
- **Loop:** `requestAnimationFrame`; track elapsed time; spawn/scroll arrows upward so they reach the **target line** at their `time_ms`. Speed = pixels/ms constant.
- **Input:** `keydown` for ArrowLeft/Down/Up/Right (+ A/S/W/D). On press, find the nearest active arrow in that lane within a timing window.
  - |Δt| ≤ 45ms → **Perfect** (+score, +combo)
  - |Δt| ≤ 110ms → **Good** (+partial, +combo)
  - else / arrow passes target unhit → **Miss** (combo reset, energy −)
- **HUD:** score number, combo counter, an **energy/score bar**. Judgment text pops ("PERFECT!/GOOD/MISS").
- **Clear condition:** final score ≥ threshold OR energy > 0 at song end → **cleared**. Otherwise → **failed** (offer retry; it's a gift, so keep failing gentle/funny, and still allow story progress after a couple tries).
- **Music sync:** a Scarpenters chiptune arrangement (Web Audio) drives timing; chart `time_ms` values align to its beat.
- **Penny wildcard:** at a scripted timestamp, spawn 2–3 "cat" arrows / a paw sprite crossing lanes; handle them as normal notes but flavored comedic. Big fumble here can set `state.pennyChaos`.
- **Exit:** on clear/fail, hide canvas, resume story engine at the node's `onWin`/`onLose` target.

---

## 8. Audio system

- **Engine:** Web Audio API. A tiny chiptune helper that plays square/triangle-wave note sequences for looping themes, plus one-shot SFX.
- **Music tracks (keys):** `theme_cozy` (apartment), `theme_foulk` (tense), `theme_hunt` (upbeat chase), `theme_ddr` (the minigame song), `jingle_win` / `jingle_soft` (endings). Engine crossfades when `music` changes between nodes.
- **SFX (keys):** `blip` (per-character text typing), `move` (menu navigate), `confirm` (choice select), `swoosh` (scene transition), `stinger` (invasion/contest reveal), plus meme stings `airhorn`, `ohhh`, `vineboom` used by the DJ narrator at big beats.
- **Mute toggle:** persistent button in the top bar; toggles a master gain node. (No localStorage — keep state in memory per session.)

---

## 9. Art asset manifest

See filenames in §4. **Style:** simple, flat, limited-palette pixel art; small front-facing character sprites; one flat background per scene. **Placeholder plan:** each asset = a labeled colored rectangle at correct dimensions so the game is fully playable pre-art. **Swap path:** user drops same-named PNGs into `assets/img/`. Recommended sprite size ~64–96px tall; backgrounds match the game-screen resolution.

Priority for prototype (Phase 1) placeholders: `bg_apartment`, `bg_foulk_break`, `bg_barcade`, `char_stefan`, `char_greg`, `char_maria`, `char_penny`, DDR arrows.

---

## 10. UI / UX details

- Title screen: "106.9 Presents: **Stefan's Big Day**", start button, mute toggle, tiny radio-dial motif.
- Text box: typewriter effect (~30–50ms/char) with a "▼" advance indicator; click/Space/Enter advances or fast-completes the current page.
- Choices: appear after text finishes; keyboard-navigable (↑↓ + Enter) and clickable; `move`/`confirm` SFX.
- DJ narrator: styled interstitial (e.g. a radio-banner overlay) with a meme sting; dismiss to continue.
- Endings: ending title card + art + jingle + "Play Again" / "See other endings".
- Accessibility: keyboard fully supported; readable text size; mute available.

---

## 11. Build phases & checklist

**Phase 1 — Prototype (next deliverable):**
- [ ] `stefan.html` skeleton: game screen, text box, choice menu, top bar + mute.
- [ ] Engine: render node (bg + chars + typed text + choices), navigate `next`, basic `state`.
- [ ] Placeholder art for Phase-1 priority assets (§9).
- [ ] Web Audio: `blip`, `confirm`, and one looping `theme_cozy`.
- [ ] Content: Prologue (`start`→`morning_cup`→`morning_choice`) + first node of BOTH branches (`b1_clockin`, `b2_hunt`) so the fork is playable.
- [ ] A stubbed DDR screen you can open from `b2_hunt`'s path (even a 1-lane proof) to prove the minigame loop.
- [ ] Runs by double-click, offline. **Goal: feel the game.**

**Phase 2 — Full content:**
- [ ] All Branch 1 nodes + 4 endings.
- [ ] All Branch 2 nodes + 4 endings + finale routing via `state`.
- [ ] Full DDR minigame (both charts, HUD, Penny wildcard, clear/fail).
- [ ] DJ narrator interstitials; all music themes + SFX + meme stings.
- [ ] "Play Again" / "See other endings" flow.

**Phase 3 — Polish & handoff:**
- [ ] Swap in Stefan/Maria's Procreate art.
- [ ] Richer looping music; final SFX pass; title screen art.
- [ ] `README.txt` (how to run, how to swap art).
- [ ] Package `StefanGame/` folder (ready for USB/zip/email).

---

## 12. Verification / test plan

- [ ] **Path coverage:** reach all 8 endings; confirm each choice routes correctly and `state`-based finale picks the right Branch 2 ending.
- [ ] **Offline test:** open the file with no network (and from a copied folder, simulating USB) — art, audio, and minigame all work.
- [ ] **Minigame:** verify hit windows, combo/score, clear + fail + retry, Penny wildcard, keyboard input (arrows + WASD).
- [ ] **Audio:** mute toggle silences everything; music crossfades on scene change; no external requests.
- [ ] **Editability:** confirm a non-coder could change a `text` line or `label` in `STORY` without breaking anything.
- [ ] **Cross-browser sanity:** Chrome + at least one other browser.

---

## 13. Open items (non-blocking)

- Exact riddle wording for Challenge 1 (KH + One Piece) → decode target = a Wilmington landmark (to be written).
- Which Scarpenters song becomes the DDR chiptune chart (default: "Visions").
- Final placeholder → real-art swap schedule (user's Procreate pieces).

---

*Companion doc: `Stefan_Game_Design_Plan.md` (creative/story). This doc + that doc together fully specify the project.*
