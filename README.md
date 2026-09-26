# Density Wave — spiral galaxy simulation

[🇭🇺 Magyar leírás](README.hu.md)

An interactive spiral galaxy based on **density wave theory** (Lindblad, 1925): every star rides its own slightly rotated elliptical orbit, and the spiral arm is not a structure made of matter but a "traffic jam" where the orbits crowd together. A single HTML file with no external dependencies — Canvas 2D plus Web Audio, no libraries, no image or sound assets.

**Live demo:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Both serve the `main` branch and redeploy automatically on every commit. The interface speaks four languages (EN · HU · RO · DE), switchable from the buttons at the top of the panel.

## Download and run it yourself

The whole application is a single file. Download [index.html](index.html) (or clone the repository) and open it in a browser — no install, no build step, not even an internet connection.

```bash
git clone https://github.com/laci141/galaxy.git
```

The link at the bottom of the page always leads back here so anyone can grab a copy.

## Controls

The control panel sits in the **top-right corner**. Its header carries two buttons: 🔊 toggles the sound, **–** minimises the panel to a single round button (☰, same corner) that brings it back — handy for an unobstructed view of the galaxy. On phones the panel is a drawer sliding up from the bottom edge.

Below the header sits the **language switch — EN · HU · RO · DE**. It translates every string in the interface, including tooltips, notifications and the palette and preset names, and it also switches number formatting (12,000 / 12 000 / 12.000). The choice is remembered in `localStorage`, travels in the share link (`?lang=de`), and on a first visit the browser language is picked automatically when it is one of the four.

Inside are four tabs: **Galaxy**, **Visuals**, **Sound** and **Science**.

### Galaxy — the shape of it

| Control | What it does |
|---|---|
| **Hubble type** | Sa → Sc: bigger bulge + tighter arms ↔ smaller bulge + more open arms |
| **Bar** | 0–100%: inner orbits align to a shared major axis, producing a barred (SB) galaxy |
| **Winding** | how tightly the arms are wound |
| **Spiral arms** | number of arms (1–5) |
| **Zoom** | true camera zoom, 10–100% (star sizes scale along with it) |
| **Time speed** | simulation speed (0–3×) |
| **Inclination** | viewing angle (0° = face-on) |
| **Presets** | Andromeda · Whirlpool M51 · Barred SBb · Pinwheel — one-click setups |
| **Rotating pattern** | the spiral pattern turns rigidly; stars overtake the arms inside corotation and fall behind outside it, and the dust lane / H II / young blue star sequence flips sides across it |
| **Show orbits** | reveal the hidden elliptical orbits (plus the dashed corotation circle while the pattern rotates) |
| **Material arms** | "what if the arms were made of matter" — a demo of the winding problem |
| **💥 Galaxy encounter** | a companion galaxy swings past on a prograde, near-parabolic orbit and pulls out tidal tails and a bridge (test particles in the galaxy's potential plus one companion mass, after Toomre & Toomre 1972); **End encounter** rewinds every star back into the spiral |

#### Automatic cycling

Three of those sliders can drive themselves. Directly beneath **Zoom**, **Time speed** and **Inclination** sits a small switch plus its own **Cycle time** slider, so the view can drift on its own — useful for a screensaver, a projection or a recording, without anyone touching the panel.

| Switch | Sweeps between | Cycle time | Default |
|---|---|---|---|
| **Auto zoom** | 10% ↔ 90% | 30 s – 3 min | 2:00 |
| **Auto time speed** | 0.2× ↔ 2.8× | 30 s – 3 min | 2:00 |
| **Auto inclination** | 10° ↔ 70° | 30 s – 3 min | 3:00 |

The cycle time is the **full round trip** — out to one end, back to the other — and it is shown as `M:SS` next to the slider, so 3:00 means the galaxy takes a minute and a half to tilt from 10° to 70° and another minute and a half to come back.

A few details that keep it from feeling mechanical:

- **Cosine easing.** The value follows `min + (max−min)·(0.5 − 0.5·cos 2πφ)`, so it slows down and lingers near both extremes instead of bouncing off them.
- **No jump when you switch it on.** The phase is seeded from the slider's current value (`φ = acos(1−2u)/2π`), so cycling starts from exactly where you left the slider.
- **Real elapsed time.** The phase advances on the wall clock, not on simulation steps, so changing the cycle time mid-sweep stretches the motion rather than snapping it, and a slow machine does not slow the drift.
- **Grabbing the slider wins.** Moving Zoom, Time speed or Inclination by hand switches its cycler off, so manual control is never fought over.
- Each switch and its cycle time travel in the share link, and all of it is translated into the four interface languages.

### Visuals

| Control | What it does |
|---|---|
| **Palette** | 4 colour worlds: Classic · Indigo Dust · Blue-Gold · Violet Sea |
| **Auto-switch** | picks a new palette every 2–3 minutes with a slow 4.5 s crossfade |
| **Colour boost** | 0–25% chroma lift on the palette (default 15%) — 0% is the original, restrained colouring |
| **Nebula density** | 0–25% opacity lift on the cloud and nebula layers (default 15%) |
| **Glow** | 0–100% diffuse light from the galaxy itself (default 50%), built by halving the frame down to 1/16 and adding it back — the unresolved starlight that turns loose grains into glowing arms |
| **Star count** | desktop 5,000–30,000, mobile 3,000–20,000 |
| **Performance guard** | automatically lowers the star count if the frame rate drops below 35 fps |
| **Supernovae** | a rare star flares up inside a softly expanding shell of light (with sound) |
| **Parallax** | mouse movement / device tilt shifts background and galaxy apart for depth |
| **Milky Way band** | a diagonal dense star stream with dark dust lanes in the background |
| **WebGL renderer** (beta) | draws the galaxy on the GPU — up to 200,000 stars on desktop, 100,000 on tablets, 60,000 on phones; switching reloads the page with the same settings |
| **📷 Photo (PNG)** | saves without the UI, at double resolution where it fits |
| **🔗 Copy link** | encodes every setting into the URL so it can be shared |
| **🎬 Projector** | a scripted cinematic tour — five shots (wide view, corotation, an arm close-up that tracks the turning pattern, edge-on bulge, pull-out) with eased camera moves and a caption each; palettes keep changing |
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

### Science — what the model does and does not claim

The fourth tab is plain reading: how the model works (nested elliptical orbits with a radius-dependent twist, a bar built by aligning the inner orbits), what it deliberately leaves out (no gravity between stars, no self-consistent density wave, supernovae and the Milky Way band are visual effects, the presets are morphological matches rather than physical models), and the sources it leans on — Lindblad 1925, Lin & Shu 1964, Binney & Tremaine 2008 and Shu 1982, each linked to ADS, a DOI or the publisher.

Below the galaxy there is also a scrollable article covering the same ground at more length — reachable with the **Scroll for the science ↓** button at the bottom of the screen. It is real text in the HTML, so search engines and readers without JavaScript get it too.

### Keys

`H` hide/show UI · `F` fullscreen · `P` photo · `V` projector mode · `Esc` exit · `M` sound

**Click a star** to follow it: a ring marks it, a fading trail shows its path, and a label says whether it is overtaking the arms, being overtaken, or keeping pace near corotation. Click empty space or press `Esc` to let go.

Tilt the galaxy and the bulge stays round while the disc flattens; bulge stars above the disc plane are drawn after the dust, so a dust lane crosses in front of the bulge.

Space is deliberately left alone so it still scrolls the page, and none of these fire while a slider, button or select has focus.

## WebGL renderer (beta)

Off by default; switch it on in **Visuals** or open the page with `?gl=1`. The Canvas 2D renderer stays the default and the fallback.

- **Same model, on the GPU.** Every population is an instanced quad; the vertex shader repeats the orbit maths (tilt, bar, pattern rotation, material winding, the encounter blend) and the arm-phase brightness of H II regions, young stars and dust. The CPU still advances the phases, integrates the encounter and handles picking; orbits, the corotation circle and the followed star are drawn on the 2D canvas above it.
- **HDR, without changing the look.** Light adds in the same space as the canvas's `lighter` mode, but into a half-float buffer that keeps counting past 1.0. A soft-knee curve leaves everything below 0.75 untouched and rolls the rest off with a Reinhard shoulder, so dense cores keep their structure instead of burning to white. Linear-light adding and ACES were both tried first: faint halos vanished over brighter regions and the ACES toe swallowed the glow.
- **Bloom** is a dual-filter blur down a six-level chain and back up, weighted by the **Glow** slider.
- **Dust hides the background too:** its coverage goes into the alpha channel, so the lanes darken the sky behind the galaxy as they do in Canvas 2D.
- **Phones:** the canvas resolution is capped at 2× device pixels, and the encounter integrates at most 30,000 stars (the rest fade out for its duration and back in on rewind) — at a 6× CPU slowdown 60,000 integrated stars cost ~43 ms per frame, capped ~27 ms.
- **Fallback:** if WebGL2 is missing, a shader fails to compile or the context is lost, the page says so and carries on with Canvas 2D.

Measured against Canvas 2D at 12,000 stars (Playwright, software GPU): mean brightness 40.2 vs 42.3, blown-out pixels 0.10% vs 0.19%; at 60,000 stars 0.07%.

## Why does it look the same at every zoom level and screen?

In the previous version the star sprites had fixed CSS-pixel sizes while the galaxy itself scaled with the window. Browser zoom resizes the CSS viewport, so the image depended on the zoom level (delicate at 50%, bloated and blown-out at 70%+) — and with additive (`lighter`) blending, overlapping stars sum up, so crowding "whitens" the core disproportionately.

The current version:

- **World-unit rendering:** the size of every drawn element derives from the galaxy radius (`g = scl/REF`), so the star-to-galaxy ratio and the additive overlap density are identical at any browser zoom, window size and DPI.
- **Crisp sprites:** sprite bitmaps are rebuilt at the current scale × `devicePixelRatio` (an exact 1:1 pixel blit when settled); `devicePixelRatio` changes are tracked with `matchMedia`.
- **Brightness normalization:** per-star brightness drops as the count rises, so 30,000 stars look denser rather than whiter.
- **Stable image:** a seeded RNG — resizing, zooming or switching palettes never reshuffles the galaxy.
- **Two clocks:** orbits advance on a clamped `dt` for numerical stability, while crossfades, supernovae, the projector camera and the auto-cycling sliders run on real elapsed time, so they never drag on a slow machine.
- **Off-screen culling:** at high zoom, stars outside the viewport are skipped entirely.

## Verification

Playwright + Chromium, screenshots normalized to the same physical resolution at 50 / 70 / 100% simulated browser zoom:

| Measure | Old | Current |
|---|---|---|
| Brightness 50% → 100% zoom | +77% | identical (21.1 → 21.6) |
| Blown-out white pixel fraction | grew 8× | unchanged (0.042%) |
| Per-pixel difference across zoom levels | — | ≤ 0.55/255 |

Also verified: star slider 5,000 → 30,000 (brightness 1.39× — rises without blowing out), mobile range 3,000–20,000, minimum colour distance between the four palettes 6.92 (5.71 before the 15% chroma lift), bar 0 → 90% visible change, presets, supernova lifecycle, 3200×1800 PNG export, link sharing and restore, sound engine start/stop, performance guard, projector mode, the panel anchored top-right with the sound button in its header, minimise/restore, credit-link visibility, and the mobile drawer and tabs with no horizontal scrolling. No console errors.

The auto-cycle controls are covered by their own run: inclination sweeping 11° → 58° and staying inside 10–70°, zoom 65% → 90% inside 10–90%, time speed 1.18× → 2.78× inside 0.2–2.8×, a 90 s period rendering as `1:30`, dragging the inclination slider by hand switching its cycler off and holding 45°, and the switch plus its period surviving a share-link round trip. Structure and access alongside it: a single `<h1>`, the 547-word article present in the served HTML, all 17 sliders carrying a `label[for]`, all 12 toggles being `button[role="switch"]`, `Space` scrolling the page instead of triggering a shortcut, `V` starting projector mode, and all four languages rendering with zero empty nodes.

## Accessibility and findability

- Every slider has a real `<label for>`, every toggle is a `<button role="switch">` with `aria-checked`, and keyboard focus is always visible (`:focus-visible` ring).
- Keyboard shortcuts never fire from a focused control, and `prefers-reduced-motion` is honoured for scrolling and layer transitions.
- `robots.txt` and `sitemap.xml` are served from the site root; the page carries a canonical URL, Open Graph and Twitter card metadata with `og-image.png` (1200×630), and JSON-LD describing it as an `EducationalApplication` in four languages with its three key citations.

## Running & testing

Open `index.html` in a browser (or `python3 -m http.server` and visit http://localhost:8000).

URL parameters: `?seed=42` deterministic galaxy · `?freeze` static frame · `?fps=0` performance guard off · `?lang=en|hu|ro|de` interface language · every control can be passed as well (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `sat`, `neb`, `auto`, `sn`, `px`, `band`, `orb`, `mat`, `pat`, `glow`, `gl`) · and the auto-cyclers with their periods in seconds (`ainc`, `aincT`, `azoom`, `azoomT`, `aspd`, `aspdT`) — this is exactly what the **🔗 Copy link** button produces.

## License

MIT — see [LICENSE](LICENSE). Free to use, modify and share.
