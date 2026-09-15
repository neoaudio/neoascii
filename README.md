# neoascii

**v1.0.2** · Turn a song, a video or a photo into moving text art,
then export it as an MP4 — all in one HTML file, entirely offline.

neoascii rebuilds pictures and music out of typed characters. Feed it a
song and it builds a 3D world from the sound and flies a camera through
it. Feed it a video and it redraws it in characters. Feed it a video
*with* sound and it can do both at once.

No build step, no dependencies, no server, no account. Download the
file, open it in a browser, and it works with the network switched off.

---

## Contents

- [Quick start](#quick-start)
- [Features](#features)
- [The styles](#the-styles)
- [Controls](#controls)
- [Exporting](#exporting)
- [How it works](#how-it-works)
- [Building from source](#building-from-source)
- [Tests](#tests)
- [Browser support](#browser-support)
- [Privacy](#privacy)
- [Changelog](#changelog)

---

## Quick start

1. Download `neoascii-v1.0.2.html`.
2. Open it in a browser — double-click it, or drag it onto a window.
3. Press the **+**, choose a file, and press the arrows to try styles.
4. Press the display in the middle to open the controls.

That is the entire install. The file is self-contained: one HTML
document with no external scripts, fonts, styles or images.

---

## Features

| | |
|---|---|
| **14 styles** | 9 built from audio, 5 from video or stills |
| **45 character sets** | plus anything you type yourself |
| **32 palettes** | Game Boy, C64, CGA, EGA, NES, Spectrum, Pico-8, Nord, Dracula, Matrix… plus your own |
| **7 effect modes** | characters, braille, shade blocks, halftone, squares, edge detection, pixelate |
| **24-band analysis** | log-spaced, each band levelled independently |
| **Grid warp** | symmetry, kaleidoscope, tunnel and dome bends — applies to every style |
| **Post effects** | glow, scanlines, chromatic split, grain, vignette, beat pulse |
| **12 presets** | plus a randomiser |
| **MP4 export** | H.264 + AAC, whole track, with a WebM/VP9/Opus fallback |
| **PNG export** | for stills |
| **Offline** | no network calls of any kind, enforced by CSP |

Input formats: `wav mp3 m4a aac ogg opus flac aiff` · `mp4 m4v mov
webm mkv` · `jpg png gif webp avif heic`

---

## The styles

### Built from audio

| Style | What it does |
|---|---|
| **AUDIO LANDSCAPE** | Flies over terrain whose height is the spectrum. Bass in the centre, treble at the edges, time running to the horizon. |
| **AUDIO TUBE** | A wormhole whose wall is the spectrum wrapped into a cylinder. Breathes with the low end, bends as it travels. |
| **AUDIO TUNNEL** | A corridor with lights, doorways and grime. Turns on heavy bass, with the camera banking into corners. |
| **RINGS** | Top-down, sound travelling outward. A ring at radius *r* is what the track was doing *r* seconds ago — the history *is* the picture. |
| **RAIN** | Falling columns, each assigned its own band, speed and head position. |
| **SPECTROGRAM** | Time across, pitch up, loudness as the glyph. |
| **BARS** | 24 bands with peak caps. The caps are read back out of the table, not carried forward. |
| **ORBIT** | A rotating wireframe sphere with the spectrum pushed through its surface. Opens into a torus. |
| **SKYLINE** | A street between two rows of towers, one band per tower. |

### Built from video or stills

| Style | What it does |
|---|---|
| **ASCIIFY** | Your video or photo, redrawn in characters. |
| **RELIEF** | The picture becomes a heightfield and the camera flies over it. Bass drives the vertical exaggeration. |
| **TUBE SKIN** | The video painted onto the inside of the wormhole. The tube's angle and arc position *are* the texture coordinates. |
| **BAND CURTAIN** | The picture shows only where the spectrum is loud. Column position maps to frequency. |
| **TEAR** | Slices dragged sideways by bass and spectral flux, then knitting back together. |

The last four use audio **and** video together.

---

## Controls

The drawer opens by pressing the dot-matrix display. Tabs follow the
order of the render pipeline.

<details>
<summary><b>GRID</b> — grid shape, the style's own controls, then the warp</summary>

Ordered by the pipeline: the shape of the grid, then what fills it,
then what folds it.

**Grid**
- `COLUMNS` — grid width in cells. Defaults to `floor(0.15 × window
  width)`, capped at 100 / 150 / 300 for windows up to 480 / 960 / above.
- `CELL SHAPE` — cell height ÷ width
- `CELL GAP` — spacing between cells

**World** (or **Source** with a picture) — the current style's own
parameters. Each style declares only its own.

**Warp**
- `SYMMETRY` — off, mirror, 4-fold. Folds *and rescales*: the half it
  keeps is opened out to the full width.
- `KALEIDOSCOPE` — off, mirror, 4-fold. Folds *without* rescaling: the
  half or quarter it keeps stays at its own size and is copied into the
  rest, discarding the other part.
- `KEEP` — which part the kaleidoscope keeps. Left/Right/Top/Bottom in
  mirror mode, one of four corners in 4-fold.
- `BEND` — off, tunnel, dome
- `TWIST` — swirl applied to the bend

The warp remaps the finished cell grid, so all of it applies to every
one of the 14 styles including the video ones. `SYMMETRY` and
`KALEIDOSCOPE` compose.
</details>

<details>
<summary><b>IMAGE</b> — the picture stage (video and stills only)</summary>

`BRIGHTNESS` · `CONTRAST` · `GAMMA` · `SATURATION` · `HUE SHIFT` ·
`INVERT TONES` · `POSTERIZE`

One lookup table for brightness, contrast, gamma, invert and posterize;
a 3×3 matrix for hue and saturation.
</details>

<details>
<summary><b>GLYPH</b> — what the cells are made of</summary>

`EFFECT` (characters · braille dots · shade blocks · halftone dots ·
solid squares · edge lines · pixelate) · `CHARACTER SET` (45) ·
`YOUR OWN CHARACTERS` · `INVERT THE RAMP` · `FONT` (5) · `WEIGHT` ·
`GLYPH SIZE` · `SCRAMBLE` + rate · `DITHER` (ordered 8×8 ·
Floyd–Steinberg · Atkinson) + strength · `EDGES` (Sobel) + threshold +
edges-only
</details>

<details>
<summary><b>COLOUR</b> — ink and background</summary>

`INK` — one ink · two inks · **ink spread** (the default) · palette ·
from the picture

`SPREAD ACROSS` — brightness · vertical · horizontal · diagonal ·
radial · **distance** (audio styles, using the depth buffer)

Plus `PALETTE` (32, or your own hex list) · `COLOUR BOOST` ·
`SHADE BY LEVEL` · background flat/gradient/clear · `GHOST OF THE
PICTURE`

With a picture, palettes snap to the nearest entry. Without one, the
palette is used as a brightness ramp sorted by luminance — which is why
all 32 work on the audio styles.
</details>

<details>
<summary><b>FX</b> — post effects</summary>

`GLOW` · `SCAN LINES` + pitch · `COLOUR SPLIT` + angle + mode ·
`GRAIN` · `VIGNETTE` · `BEAT PULSE`

All run as shader passes on the GPU path.
</details>

<details>
<summary><b>OUT</b> — export settings and presets</summary>

`FRAMES EACH SECOND` (24/30/60) · `WIDTH` (960/1280/1920/2560) ·
`QUALITY` · `INCLUDE THE SOUND`, a line stating exactly what file you
will get, 12 presets, `SURPRISE ME` and `RESET`.
</details>

<details>
<summary><b>Presets</b> — 12 ready-made looks</summary>

| Look | |
|---|---|
| `TERMINAL` | green on black, glowing |
| `MATRIX RAIN` | falling katakana |
| `NEWSPRINT` | black halftone dots on cream |
| `GAME BOY` | four shades of green, ordered dither |
| `BRAILLE FINE` | fine braille with Floyd–Steinberg |
| `AMBER CRT` | amber monitor, scanlines, split |
| `EDGE SKETCH` | Sobel outlines on paper |
| `FRUTIGER GLASS` | blue/green gradient, glassy circles |
| `SUNSET SPREAD` | warm hue spread on the vertical axis |
| `NEON SPLIT` | cyan with heavy chromatic split |
| `DEEP SPREAD` | hue by depth, pulsing with the beat |
| `PARENT LOOK` | plain white, as the app first shipped |

Every look starts from the same base, so one always gives the same
result regardless of what was set before it. `SURPRISE ME` shuffles,
`RESET` returns to the defaults for your screen.
</details>

### Keyboard

| Key | Action |
|---|---|
| `←` `→` | previous / next style |
| `Space` | play / pause |
| `Esc` | hide / show the interface |

---

## Exporting

Press **EXPORT**. The interface hides itself and the render plays out on
the stage frame by frame — what you watch *is* what is being written.
The progress bar stays on screen and is itself the stop button.

- Always the **whole** track. There are no in and out points.
- **MP4** with H.264 video and AAC audio by default.
- Falls back to **WebM** with VP9 and Opus where H.264 is unavailable.
- Sound is worth more than a container: a browser with H.264 but no AAC
  drops to WebM rather than hand you a silent file.
- The OUT tab states which you will get **before** you press anything.
- With a still open the button becomes **SAVE PICTURE** and writes a PNG.

Both muxers are written from scratch in the file — an ISO BMFF writer
with 64-bit `co64`/`mdat` fallbacks past 4 GB, and an EBML writer with
cue points. Neither joins the encoded chunks into one buffer: a `Blob`
takes a list of pieces, so the samples go in as they are. Five minutes
of 1080p is around half a gigabyte, and holding it twice is where a
phone runs out of memory.

---

## How it works

### Every frame is a pure function of its timestamp

Nothing carries over from the previous frame. No accumulators, no
per-frame smoothing, no pattern keyed to a point index or a frame
counter. Where something has to decay or trail, it is found by looking
*back* along the analysis table from the present moment.

This is the whole reason an export matches the preview: your screen
drops frames when it is busy and the export never does, yet both land
on identical pixels. It is enforced by the test suite, which renders the
same timestamp after two different frame histories and compares the
grid byte for byte.

### The grid is measured in columns, never pixels

Cell size follows from the render width, so a 1920px export lands on the
same cells as a 640px preview. **Measured: 0 cells differ** between
640×360 and 1920×1080.

### Rendering

The cell grid goes to the GPU as two small textures — one for which tile
each cell holds, one for its colour — and a fragment shader works out,
for every output pixel, which cell it falls in and what belongs there.
One draw call.

The 2D path it replaced made one `drawImage` per cell: at 110 columns
that is 3630 calls per frame, measured at **12.5 ms of a 12.9 ms** frame.
It was never the pixels — 3630 small blits cost 11.9 ms while 363 blits
covering the same total area cost 1.3 ms. It was the crossing, ~3 µs
each, 32.7 million times in a five-minute export.

A 2D fallback remains for anything that will not give a WebGL2 context.

### Module order

Dependencies run strictly downward:

```
Core      hashing, noise, FFT, 24-band analysis, camera
Tables    character sets, palettes, fonts, modes
Grid      the cell grid, splatting, depth, warp
Ink       palettes, hue spread, per-cell colour map
Atlas     glyph atlas
GLPaint   the WebGL2 painter
Compose   levels, dither, Sobel, paint, post FX
Path      turn planning, ribbons
World     terrain, lanes, band sampling
Styles    the three original audio styles
Styles2   the six band-driven audio styles
Asciify   the video sampler and the plain video style
Combos    the four audio + video styles
Shared    the control set and tab layout
Exporter  MP4 and WebM writers, encoders, scheduling
App       state machine, drawer, main loop
```

`Grid`, `Compose.levels` and `Ink` touch no canvas and no clock, which
is what lets the determinism suite run them in Node.

---

## Building from source

```sh
python3 build.py
```

Concatenates `parts/` in dependency order into
`neoascii-v1.0.2.html` and writes `README.txt` and `README.md` with the
version substituted in. No toolchain, no package manager, no minifier.

To change the version, edit `VER` at the top of `build.py`. The HTML
filename, the page title, the `version` meta tag, the version shown in
the drawer and both READMEs all follow from it.

---

## Tests

```sh
npm install @napi-rs/canvas jsdom
node t_frame.js     # determinism, flicker safety, resolution invariance
node t_render.js    # real frames, inspected rather than assumed
node t_app.js       # the page booted in jsdom, driven like a user
node t_gl.js        # shader uniforms and addressing
node t_readme.js    # the docs against the app
node t_speed.js     # per-stage profiling
```

Around 260 assertions. Notable ones:

- The same timestamp rendered after different frame histories, compared
  byte for byte.
- 640×360 against 1920×1080, same grid, 0 cells different.
- Every style held still with its audio reactions off, checking no
  pattern crawls with a point index.
- Every slider of every style driven to both ends, checking for
  non-finite output.
- Every MP4 box walked and its size verified — which is how a
  hand-written MP4 normally fails.
- Every shader uniform matched between the GLSL and the JavaScript, and
  the shader's pixel-to-cell addressing worked in JavaScript and
  compared against the 2D painter over 412,000 pixels.

`t_render.js` writes PNGs to `shots/` so frames can be looked at rather
than trusted.

---

## Browser support

| | Preview | MP4 export | Sound in the file |
|---|---|---|---|
| Chrome / Edge | ✅ | ✅ | ✅ |
| Safari 16.4+ | ✅ | ✅ | usually |
| Firefox | ✅ | ⚠️ falls back to WebM | ✅ Opus |
| No WebGL2 | ✅ 2D fallback | — | — |

The app probes the browser at startup and tells you on the OUT tab what
you are going to get.

---

## Privacy

There is no analytics, no telemetry and no network code of any kind.
The document sets:

```
Content-Security-Policy: default-src 'none'; connect-src 'none'; ...
```

so the browser refuses outbound connections regardless. No
`localStorage`, no cookies, no IndexedDB. Your files are decoded on your
device and never leave it. The test suite checks all of this on every
build.

---

## Changelog

| Version | Changes |
|---|---|
| **1.0.2** | A paused stage is now drawn only when something changes it, instead of being redrawn sixty times a second. The loop also stops while the tab is hidden. This removes a high-pitched whine some machines made on bright styles |
| **1.0.1** | Renamed to lowercase `neoascii`; browser tab now shows the name alone, with no version number |
| **1.0** | Full code review. Fixed a window resize during a render resizing the canvas being encoded; both muxers now stream into the `Blob` instead of holding the whole film twice; removed unreachable code. `README.txt` dropped in favour of this file |
| **0.9.9** | Grid controls moved to the top of their tab; `KALEIDOSCOPE` added beside `SYMMETRY`; 6-fold symmetry removed |
| **0.9.8.5** | Ink spread as the default colour mode; tidied the opening sheet |
| **0.9.8** | Progress bar stays visible during export and doubles as the stop button; fixed video blanking at loop points; fixed the file picker being read as a pause; ~20% faster loading |
| **0.9.7** | Exports now play out full-screen as they render |
| **0.9.6** | Renamed to NEOASCII; a second file keeps your style and settings |
| **0.9.5** | 10 new styles including 4 audio+video combos; 24-band analysis; grid warp |
| **0.9.4** | The **+** skips the sheet once a file is open |
| **0.9.3** | Column count and cap scale with window width |
| **0.9.0** | Stills supported, with PNG export |
| **0.8.0** | Interface colours derive from your chosen ink and background |
| **0.7.0** | Drawer sized from available space; dot-matrix display fills its panel |
| **0.4.0** | WebGL2 painter, ~8× faster; MP4 + AAC became the default output |
| **0.1.0** | First build: 4 styles, tabbed controls, deterministic offline export |

---

<sub>neoascii v1.0.2 — one file, no accounts, nothing uploaded.</sub>
