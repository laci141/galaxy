# Density Wave — spiral galaxy simulation

[🇭🇺 Magyar leírás](README.hu.md)

An interactive spiral galaxy based on **density wave theory** (Lindblad, 1925): every star rides its own slightly rotated elliptical orbit, and the spiral arm is not a structure made of matter but a "traffic jam" where the orbits crowd together. A single HTML file with no external dependencies — Canvas 2D plus Web Audio, no libraries, no image or sound assets.

**Live demo:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Both serve the `main` branch and redeploy automatically on every commit.

## Download and run it yourself

The whole application is a single file. Download [index.html](index.html) (or clone the repository) and open it in a browser — no install, no build step, not even an internet connection.

```bash
git clone https://github.com/laci141/galaxy.git
```

The link at the bottom of the page always leads back here so anyone can grab a copy.

## Controls

The control panel sits in the **top-right corner**. Its header carries two buttons: 🔊 toggles the sound, **–** minimises the panel to a single round button (☰, same corner) that brings it back — handy for an unobstructed view of the galaxy. On phones the panel is a drawer sliding up from the bottom edge.

Below the header sits the **language switch — EN · HU · RO · DE**. It translates every string in the interface, including tooltips, notifications and the palette and preset names, and it also switches number formatting (12,000 / 12 000 / 12.000). The choice is remembered in `localStorage`, travels in the share link (`?lang=de`), and on a first visit the browser language is picked automatically when it is one of the four.

Inside are three tabs: **Galaxy**, **Visuals** and **Sound**.

### Galaxy — the shape of it

| Control | What it does |
|---|---|
| **Hubble type** | Sa → Sc: bigger bulge + tighter arms ↔ smaller bulge + more open arms |
| **Bar** | 0–100%: inner orbits align to a shared major axis, producing a barred (SB) galaxy |
| **Winding** | how tightly the arms are wound |
| **Spiral arms** | number of arms (1–6) |
| **Zoom** | true camera zoom, 20–200% (star sizes scale along with it) |
| **Time speed** | simulation speed (0–3×) |
| **Inclination** | viewing angle (0° = face-on) |
| **Presets** | Andromeda · Whirlpool M51 · Barred SBb · Pinwheel — one-click setups |
| **Show orbits** | reveal the hidden elliptical orbits |
| **Material arms** | "what if the arms were made of matter" — a demo of the winding problem |

### Visuals

| Control | What it does |
|---|---|
| **Palette** | 4 colour worlds: Classic · Indigo Dust · Blue-Gold · Violet Sea |
| **Auto-switch** | picks a new palette every 2–3 minutes with a slow 4.5 s crossfade |
| **Colour boost** | 0–25% chroma lift on the palette (default 15%) — 0% is the original, restrained colouring |
| **Nebula density** | 0–25% opacity lift on the cloud and nebula layers (default 15%) |
| **Star count** | desktop 5,000–30,000, mobile 3,000–20,000 |
| **Performance guard** | automatically lowers the star count if the frame rate drops below 35 fps |
| **Supernovae** | a rare star flares up inside a softly expanding shell of light (with sound) |
| **Parallax** | mouse movement / device tilt shifts background and galaxy apart for depth |
| **Milky Way band** | a diagonal dense star stream with dark dust lanes in the background |
| **📷 Photo (PNG)** | saves without the UI, at double resolution where it fits |
| **🔗 Copy link** | encodes every setting into the URL so it can be shared |
| **🎬 Projector** | full-screen slow camera journey with automatic palette changes |
| **👁 Hide UI** | hides the controls |

### Sound — procedural cosmic soundscape

No audio files: every layer is generated live by the Web Audio API, each with its own slider like a mixing desk.

| Layer | What you hear |
|---|---|
| **Deep-space drone** | low, slowly breathing base harmony from filtered oscillators |
| **Solar wind** | pink noise through a bandpass filter, swelling over 30–50 s |
| **Stardust chimes** | sparse, reverberant bell tones on a pentatonic scale |
| **Pulsar pulse** | slow, deep thump (off by default) |
| **Follow the simulation** | zoom drives the timbre, time speed drives the chime rate |

Sound can also be toggled with the 🔊 button in the panel header or the **M** key — browsers only allow audio to start after a user gesture.

### Keys

`H` hide/show UI · `F` fullscreen · `P` photo · `Space` projector mode · `Esc` exit · `M` sound

## Why does it look the same at every zoom level and screen?

In the previous version the star sprites had fixed CSS-pixel sizes while the galaxy itself scaled with the window. Browser zoom resizes the CSS viewport, so the image depended on the zoom level (delicate at 50%, bloated and blown-out at 70%+) — and with additive (`lighter`) blending, overlapping stars sum up, so crowding "whitens" the core disproportionately.

The current version:

- **World-unit rendering:** the size of every drawn element derives from the galaxy radius (`g = scl/REF`), so the star-to-galaxy ratio and the additive overlap density are identical at any browser zoom, window size and DPI.
- **Crisp sprites:** sprite bitmaps are rebuilt at the current scale × `devicePixelRatio` (an exact 1:1 pixel blit when settled); `devicePixelRatio` changes are tracked with `matchMedia`.
- **Brightness normalization:** per-star brightness drops as the count rises, so 30,000 stars look denser rather than whiter.
- **Stable image:** a seeded RNG — resizing, zooming or switching palettes never reshuffles the galaxy.
- **Two clocks:** orbits advance on a clamped `dt` for numerical stability, while crossfades, supernovae and the projector camera run on real elapsed time, so they never drag on a slow machine.
- **Off-screen culling:** at high zoom, stars outside the viewport are skipped entirely.

## Verification

Playwright + Chromium, screenshots normalized to the same physical resolution at 50 / 70 / 100% simulated browser zoom:

| Measure | Old | Current |
|---|---|---|
| Brightness 50% → 100% zoom | +77% | identical (21.1 → 21.6) |
| Blown-out white pixel fraction | grew 8× | unchanged (0.042%) |
| Per-pixel difference across zoom levels | — | ≤ 0.55/255 |

Also verified: star slider 5,000 → 30,000 (brightness 1.39× — rises without blowing out), mobile range 3,000–20,000, minimum colour distance between the four palettes 6.92 (5.71 before the 15% chroma lift), bar 0 → 90% visible change, presets, supernova lifecycle, 3200×1800 PNG export, link sharing and restore, sound engine start/stop, performance guard, projector mode, the panel anchored top-right with the sound button in its header, minimise/restore, all four languages translating every one of the 56 interface strings with none left empty or untranslated, credit-link visibility, and the mobile drawer and tabs with no horizontal scrolling. No console errors.

## Running & testing

Open `index.html` in a browser (or `python3 -m http.server` and visit http://localhost:8000).

URL parameters: `?seed=42` deterministic galaxy · `?freeze` static frame · `?fps=0` performance guard off · `?lang=en|hu|ro|de` interface language · every control can be passed as well (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `sat`, `neb`, `auto`, `sn`, `px`, `band`, `orb`, `mat`) — this is exactly what the **🔗 Copy link** button produces.

## License

MIT — see [LICENSE](LICENSE). Free to use, modify and share.
