# Background art shortlist (free / CC0 pixel packs)

A starting shortlist for replacing the eight placeholder background colors with real
pixel art, mapped scene-by-scene. Pair this with `Art_Swap_Guide.md` — that guide
shows the *how* (swap a `BG` entry to `url('assets/bedroom.png') center/cover no-repeat`);
this doc is the *where to get the art*.

## Read this first: the license shorthand

You're sending this to Stefan and maybe showing it around, so licensing actually matters.
Three labels you'll see:

- **CC0** — public domain. Use it anywhere, no credit required, no strings. This is the
  gold standard for a gift project. Prefer these.
- **CC-BY** — free to use, but you must credit the artist (a line in your README or an
  in-game credits screen). Totally fine, just don't forget the attribution.
- **"Free" / custom license** — many itch.io packs are free to download but ship their own
  terms (often "free for personal & commercial, no reselling, credit appreciated").
  Always open the pack's own license text before shipping. Free ≠ CC0.

One habit that saves headaches later: when you download a pack, drop its license file into
`assets/licenses/` next to the art. Future-you will thank you.

A note on *style*: the game is a **side view** — characters stand along the bottom of a
640×250 stage. That means full-scene **backdrops** or **parallax** backgrounds fit best.
A lot of pixel "interior" packs on itch are **top-down** tilesets (you assemble a room from
tiles, seen from above). Those can still work if you compose a scene and export a flat
image, but it's more effort. Where I could, I leaned toward backdrop-style art.

---

## The eight scenes → suggested sources

### 1. `bedroom` — dim purple morning light
- **Best fit:** a cozy side-view bedroom backdrop. Browse itch's
  [free interior + pixel-art listing](https://itch.io/game-assets/free/tag-interior/tag-pixel-art)
  and filter for side-view "backdrop" style; several cozy-bedroom packs live here (mixed licenses — check each).
- **CC0 fallback:** build it from [Kenney Furniture Kit](https://kenney.nl/assets/furniture-kit) (CC0) —
  a bed, lamp, shelf on a flat wall color reads as a bedroom instantly.
- **Search tags:** `bedroom`, `cozy`, `interior`, `pixel-art`.

### 2. `livingroom` — cozy apartment
- **Best fit:** same [interior/pixel-art listing](https://itch.io/game-assets/free/tag-interior/tag-pixel-art);
  look for "living room / apartment" backdrops.
- **CC0 fallback:** [Kenney Furniture Kit](https://kenney.nl/assets/furniture-kit) (CC0) — couch, TV, rug.
- **Search tags:** `living room`, `apartment`, `interior`.

### 3. `foulk_break` — break room, fluorescent lighting
- **Best fit:** an "office" or "break room" interior. There isn't a famous single CC0 break-room
  backdrop, so this is a compose-it-yourself scene: a flat wall + [Kenney Furniture Kit](https://kenney.nl/assets/furniture-kit)
  (CC0) table/chairs/vending-machine-ish props, cool fluorescent tint.
- **Search tags:** `office`, `break room`, `interior`.

### 4. `foulk_rec` — rec room, something's off
- **Best fit:** reuse the living-room/office backdrop but recolor it cooler and off-kilter.
  Cheapest win: keep the same art as `foulk_break`/`livingroom` and shift the CSS tint.
- **Search tags:** `office`, `common room`, `interior`.

### 5. `foulk_invaded` — the invasion state (cold, alien)
- **Best fit:** take whichever backdrop you used for #3/#4 and overlay a sci-fi/alien wash.
  For alien props/overlays: [OpenGameArt sci-fi/space theme](https://opengameart.org/content/theme-sci-fi-space)
  (mixed licenses — filter for CC0) or [Kenney 1-Bit Pack](https://kenney-assets.itch.io/1-bit-pack) (CC0)
  for stark alien silhouettes.
- **Trick:** you don't need a whole new background — a cold color grade + a few alien shapes
  on the *same* room sells "the rec room got invaded" better than an unrelated scene would.
- **Search tags:** `sci-fi`, `alien`, `space`.

### 6. `barcade` — neon-dark barcade
- **Best fit:** neon + arcade vibes. Start at itch's
  [free Neon pixel-art listing](https://itch.io/game-assets/free/tag-neon) and the
  [FREE Animated Neon Sign Pack](https://itch.io/game-assets/free/tag-neon) for glowing signage
  to layer over a dark wall (check each pack's license).
- **CC0 backdrop base:** [ansimuz "Industrial Parallax Background"](https://ansimuz.itch.io/industrial-parallax-background)
  — industrial night theme, looped/layered, **no attribution required**. Dark, moody, reads as
  "cool underground bar" with a couple of neon signs on top.
- **Search tags:** `neon`, `arcade`, `cyberpunk`, `parallax`.

### 7. `nanus` — warm Nanu's Chicken glow (restaurant/diner)
- **Best fit:** a pixel diner/restaurant interior. Browse the
  [interior/pixel-art listing](https://itch.io/game-assets/free/tag-interior/tag-pixel-art) for
  "diner / cafe / restaurant" backdrops (mixed licenses).
- **CC0 fallback:** [Kenney Furniture Kit](https://kenney.nl/assets/furniture-kit) (CC0) counter +
  stools + warm amber wall color = fast-food-diner feel.
- **Search tags:** `diner`, `cafe`, `restaurant`, `kitchen`.

### 8. `stage` — concert stage
- **Best fit:** a dark stage with lights. Search itch for "stage / concert / spotlight"
  backdrops. Practical CC0 route: a near-black background + a few bright spotlight cones and
  speaker-stack silhouettes (easy to hand-draw, or pull shapes from
  [Kenney 1-Bit Pack](https://kenney-assets.itch.io/1-bit-pack), CC0).
- **Search tags:** `stage`, `concert`, `spotlight`, `music`.

---

## Reliable CC0 hubs (bookmark these)

- **[Kenney.nl — pixel assets](https://kenney.nl/assets/tag:pixel)** — everything here is **CC0**,
  no attribution, high quality. Furniture Kit, Background Elements, and the 1-Bit Pack are the
  most useful for this game. This is your safest source.
- **[ansimuz on itch.io](https://ansimuz.itch.io/industrial-parallax-background)** — Luis Zuno
  releases many backgrounds as CC0 / no-attribution. Great for parallax backdrops.
- **[OpenGameArt — CC0 backgrounds](https://opengameart.org/content/cc0-backgrounds)** and
  [pixel-art backgrounds](https://opengameart.org/content/pixel-art-backgrounds-0) — filter the
  license dropdown to **CC0** to be safe (the site hosts many licenses side by side).
- **[itch.io — free CC0 pixel-art assets](https://itch.io/game-assets/free/tag-cc0/tag-pixel-art)** —
  the CC0-tagged slice of itch. Still double-check each pack's own license page; the tag is
  self-reported by uploaders.

## The pragmatic recommendation

For a gift you want to actually finish: lean on **Kenney (CC0)** for props and **ansimuz (CC0)**
for backdrops, recolor a handful of base rooms with CSS tints (the invasion/rec-room trick), and
save the hand-drawn Procreate love for the scenes that matter most (the bedroom opener and the
final stage). That gets you a fully-illustrated game without eight from-scratch paintings — and
every source above is safe to ship.

*Licenses and pack availability change over time — always confirm the license on the pack's own
page before shipping. When in doubt, Kenney is the reliable CC0 default.*
