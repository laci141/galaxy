# Density Wave — spiral galaxy simulation

[🇭🇺 Magyar leírás](README.md)

An interactive spiral galaxy based on **density wave theory** (Lindblad, 1925): every star rides its own slightly rotated elliptical orbit, and the spiral arm is not a structure made of matter but a "traffic jam" where the orbits crowd together. A single HTML file with no external dependencies — Canvas 2D plus Web Audio, no libraries, no image or sound assets.

**Live demo:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Both serve the `main` branch and redeploy automatically on every commit.

## Controls

The panel has three tabs: **Galaxis** (galaxy), **Látvány** (visuals) and **Hang** (sound).

### Galaxy

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
| **Auto switch** | picks a new palette every 2–3 minutes with a slow 4.5 s crossfade |
| **Star count** | desktop 5,000–30,000, mobile 3,000–20,000 |
| **Performance guard** | automatically lowers the star count if the frame rate drops below 35 fps |
| **Supernovae** | a rare star flares up with an expanding shock ring (and a sound) |
| **Parallax** | mouse movement / device tilt shifts background and galaxy apart for depth |
| **Milky Way band** | a diagonal dense star stream with dark dust lanes in the background |
| **📷 Photo (PNG)** | saves without the UI, at double resolution where it fits |
| **🔗 Copy link** | encodes every setting into the URL so it can be shared |
| **🎬 Projector mode** | full-screen slow camera journey with automatic palette changes |
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

Sound can also be toggled with the 🔊 button (top left) or the **M** key — browsers only allow audio to start after a user gesture.

### Keys

`H` hide/show UI · `F` fullscreen · `P` photo · `Space` projector mode · `Esc` exit · `M` sound

## Why does it look the same at every zoom level and screen?

In the previous version the star sprites had fixed CSS-pixel sizes while the galaxy itself scaled with the window. Browser zoom resizes the CSS viewport, so the image depended on the zoom level (delicate at 50%, bloated and blown-out at 70%+) — and with additive (`lighter`) blending, overlapping stars sum up, so crowding "whitens" the core disproportionately.

The current version:

- **World-unit rendering:** the size of every drawn element derives from the galaxy radius (`g = scl/REF`), so the star-to-galaxy ratio and the additive overlap density are identical at any browser zoom, window size and DPI.
- **Crisp sprites:** sprite bitmaps are rebuilt at the current scale × `devicePixelRatio` (an exact 1:1 pixel blit when settled); `devicePixelRatio` changes are tracked with `matchMedia`.
- **Brightness normalization:** per-star brightness drops as the count rises, so 30,000 stars look denser rather than whiter.
- **Stable image:** a seeded RNG — resizing, zooming or switching palettes never reshuffles the galaxy.
- **Off-screen culling:** at high zoom, stars outside the viewport are skipped entirely.

## Verification

Playwright + Chromium, screenshots normalized to the same physical resolution at 50 / 70 / 100% simulated browser zoom:

| Measure | Old | Current |
|---|---|---|
| Brightness 50% → 100% zoom | +77% | identical (20.4 → 20.9) |
| Blown-out white pixel fraction | grew 8× | unchanged (0.043%) |
| Per-pixel difference across zoom levels | — | ≤ 0.55/255 |

Also verified: star slider 5,000 → 30,000 (brightness 1.38× — rises without blowing out), mobile range 3,000–20,000, four palettes with clearly distinct colours and a working crossfade, bar 0 → 90% visible change, presets, supernova lifecycle, 3200×1800 PNG export, link sharing and restore, sound engine start/stop, performance guard, projector mode, and the mobile drawer and tabs with no horizontal scrolling. No console errors.

## Running & testing

Open `index.html` in a browser (or `python3 -m http.server` and visit http://localhost:8000).

URL parameters: `?seed=42` deterministic galaxy · `?freeze` static frame · `?fps=0` performance guard off · every control can be passed as well (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `auto`, `sn`, `px`, `band`, `orb`, `mat`) — this is exactly what the **🔗 Copy link** button produces.

## License

MIT — see [LICENSE](LICENSE).
