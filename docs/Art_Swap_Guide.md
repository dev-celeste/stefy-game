# Swapping in your Procreate art

A practical guide for replacing the placeholder colored blocks with real pixel art, written so you can do it a little at a time without breaking anything.

The whole art system is built around two lookup tables (`BG` and `CHAR`) and one function (`showArt`). You mostly edit **data**, not engine code — the same pattern as the rest of the game. Backgrounds can be swapped with **zero code changes**. Characters need one tiny five-line tweak to `showArt`, included below.

---

## How the art works right now

Two palettes near the top of the `<script>` map friendly names to a "look":

```js
const BG = {
  bedroom:    "#2a2140",   // a scene says bg: "bedroom" and gets this
  livingroom: "#241f33",
  // ...8 total
};

const CHAR = {
  stefan: { color: "#d9c27a", label: "STEFAN" },
  penny:  { color: "#c9873f", label: "PENNY"  },
  // ...5 total
};
```

`showArt(scene)` reads those and paints the stage:

```js
function showArt(scene) {
  bgEl.style.background = BG[scene.bg];      // background = a color string
  charsEl.innerHTML = "";
  scene.chars.forEach(function (name) {
    const info = CHAR[name];
    const block = document.createElement("div");
    block.className = "char";
    block.style.background = info.color;     // character = a colored box
    block.textContent = info.label;          // ...with a text label
    charsEl.appendChild(block);
  });
}
```

So today a "background" is just a hex color, and a "character" is a colored `<div>` with a label. The goal is to point those at PNG images instead.

---

## Canvas sizes (draw to these in Procreate)

The game's internal resolution is **640 × 480**, split into a stage on top and a textbox below.

| Piece | Size to draw | Notes |
|---|---|---|
| Background | **640 × 250 px** | Fills the whole stage area. Solid (no transparency needed). |
| Character | **~88 × 150 px** | Transparent PNG. Drawn bottom-aligned, centered, 24px gap between characters. |

Two tips:
- **Draw bigger, then export down.** Draw backgrounds at 1280 × 500 and characters at 176 × 300 (2×), then export at the sizes above. Cleaner edges.
- **Keep characters as transparent PNGs** so the background shows behind them. Backgrounds can be PNG or JPG.
- Pixel art stays crisp because `image-rendering: pixelated` is already set on the stage — the browser won't blur your pixels when it scales them.

---

## Two ways to include the images (pick one)

This matters because of the "single file you can email" goal.

**Option A — an `assets/` folder (simplest to author).**
Put your PNGs in an `assets/` folder next to `stefan.html` and reference them by path. Easy to edit and re-export. **Trade-off:** the game is no longer one self-contained file — to share it you must send the folder *and* the html together (a zip works). It also won't show images if someone opens the html from a different location without the folder.

**Option B — embed images as base64 (keeps it a true single file).**
Convert each PNG into a text "data URI" and paste it straight into the palette. The html stays one shareable file with the art baked in. **Trade-off:** the file gets bigger and the pasted strings are long/ugly. This is the better choice for emailing Stefan the finished game.

You can start with Option A while iterating, then switch to Option B for the final send. **The code below works identically for both** — the only difference is whether the string is a short path or a giant `data:` blob.

---

## Swapping BACKGROUNDS (no code changes)

`showArt` already does `bgEl.style.background = BG[scene.bg]`, and CSS `background` happily accepts an image shorthand. So you only edit the `BG` table.

Change each entry from a hex color to a background shorthand:

```js
const BG = {
  bedroom:    "url('assets/bedroom.png') center/cover no-repeat",
  livingroom: "url('assets/livingroom.png') center/cover no-repeat",
  // ...one line per background
};
```

`center/cover no-repeat` means: center it, scale to fill the stage, don't tile. That's it — reload and the backgrounds are real art. You can convert them one at a time; any entry you leave as a hex color still works, so you're never forced to do all eight at once.

*(Option B version: replace `url('assets/bedroom.png')` with `url('data:image/png;base64,iVBORw0K...')`.)*

---

## Swapping CHARACTERS (one small `showArt` tweak)

Characters need a real change because right now they're colored `<div>`s with text. We'll make `showArt` use an image **if the character has one**, and fall back to the colored block if it doesn't — so you can swap characters in one by one, too.

**Step 1 — give a character an `img` in the `CHAR` table:**

```js
const CHAR = {
  stefan: { color: "#d9c27a", label: "STEFAN", img: "assets/stefan.png" },
  penny:  { color: "#c9873f", label: "PENNY"  },   // no img yet -> still a block
  // ...
};
```

**Step 2 — replace the body of the `forEach` in `showArt` with this:**

```js
scene.chars.forEach(function (name) {
  const info = CHAR[name];
  if (info.img) {                          // has real art -> use an <img>
    const sprite = document.createElement("img");
    sprite.className = "char";
    sprite.src = info.img;
    sprite.alt = info.label;
    charsEl.appendChild(sprite);
  } else {                                 // no art yet -> the old colored block
    const block = document.createElement("div");
    block.className = "char";
    block.style.background = info.color;
    block.textContent = info.label;
    charsEl.appendChild(block);
  }
});
```

**Step 3 — let the sprite size to its art.** The `.char` CSS forces a fixed `88 × 150` box, which is right for the placeholder blocks but may squish a differently-shaped sprite. Loosen it so images keep their proportions:

```css
.char {
  width: auto;          /* was 88px  */
  height: 150px;        /* keep the height; width follows the art */
  max-width: 120px;
  /* you can delete the border, font-size, and color lines now */
  image-rendering: pixelated;
}
```

That's the whole change. Characters with an `img` show your art; the rest stay as labeled blocks until you get to them.

---

## Making a base64 data URI (for Option B)

To turn a PNG into a pasteable string, run this in the folder with your image and copy what it prints:

```bash
# macOS — copies the full data URI onto your clipboard
printf "url('data:image/png;base64,%s')" "$(base64 -i stefan.png)" | pbcopy
```

Then paste it as the `img:` value (or inside `url(...)` for a background). Repeat per image. Yes, the strings are enormous — that's expected; collapse the code section in your editor and forget about them.

---

## Quick test checklist

After each swap, open `stefan.html` and check:

- The background fills the stage with no stretching or tiling artifacts.
- Characters sit on the "floor" (bottom-aligned) and are centered, with clear gaps.
- Transparent areas of character PNGs show the background through them (not a white box).
- Play a scene with two characters (e.g. `morning_choice`) to confirm spacing still looks right.
- If you went with Option A, test that it still works after moving the folder somewhere new — if it breaks, you want Option B before sending it to Stefan.

---

## If something looks off

- **Character has a white/colored rectangle around it:** the PNG isn't transparent, or you kept the `.char` border — remove the border and re-export as transparent PNG.
- **Background looks blurry:** it's being scaled up past its real size. Draw it larger and re-export, or keep `image-rendering: pixelated` (already set).
- **Image doesn't appear at all (Option A):** the path is wrong or the file isn't in `assets/`. Filenames are case-sensitive when shared.
- **Everything vanished after editing:** you probably dropped a comma or quote in the `BG`/`CHAR` table. Undo your last edit and redo it one line at a time.

The engine never needs to change again — from here on, new art is just new strings in two tables.
