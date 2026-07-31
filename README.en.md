# Density Wave — spiral galaxy simulation

[🇭🇺 Magyar leírás](README.md)

An interactive spiral galaxy based on **density wave theory** (Lindblad, 1925): every star rides its own slightly rotated elliptical orbit, and the spiral arm is not a structure made of matter but a "traffic jam" where the orbits crowd together. A single HTML file with no external dependencies (Canvas 2D).

**Live demo:** https://laci141.github.io/galaxy/ *(deployed automatically by GitHub Actions on every commit to `main`)*

## Controls

| Control | What it does |
|---|---|
| **Hubble type** | Sa → Sc: bigger bulge + tighter arms ↔ smaller bulge + more open arms |
| **Zoom** | true camera zoom, 20–200% (star sizes scale along with it) |
| **Winding** | how tightly the arms are wound |
| **Spiral arms** | number of arms (1–6) |
| **Time speed** | simulation speed |
| **Inclination** | viewing angle (0° = face-on) |
| **Show orbits** | reveal the hidden elliptical orbits |
| **Material arms** | "what if the arms were made of matter" — a demo of the winding problem |

## Why does it look the same at every zoom level and screen? (the fix)

In the previous version the star sprites had fixed CSS-pixel sizes while the galaxy itself scaled with the window. Browser zoom resizes the CSS viewport, so the image depended on the zoom level (delicate at 50%, bloated and blown-out at 70%+) — and with additive (`lighter`) blending, overlapping stars sum up, so crowding "whitens" the core disproportionately.

The current version:

- **World-unit rendering:** the size of every drawn element (stars, nebulae, H-II regions, dust lanes, core) is derived from the galaxy radius (`g = scl/REF`), so the star-to-galaxy ratio and the additive overlap density are **identical at any browser zoom, window size and DPI**.
- **Crisp sprites:** sprite bitmaps are rebuilt at the current scale × `devicePixelRatio` (an exact 1:1 pixel blit when settled); `devicePixelRatio` changes are tracked with `matchMedia`.
- **Stable image on resize:** a seeded RNG — resizing/zooming no longer reshuffles the galaxy.
- **Star budget matched to the display:** phone (<600 px) 5,000, medium (<1000 px) 9,000, PC 12,000 stars.

Verification (Playwright + Chromium, screenshots normalized to the same physical resolution): at 50% / 70% / 100% browser zoom the rendered image differs by ≤ 0.3/255 per pixel with < 0.5% brightness spread — the old version showed +77% brightness and an 8× blown-out-white fraction over the same range.

## Running & testing

Open `index.html` in a browser (or `python3 -m http.server` and visit http://localhost:8000).

URL parameters for testing: `?seed=42` — deterministic galaxy; `?freeze` — static frame (no animation or "breathing"). Example: `index.html?seed=42&freeze`.

## License

MIT — see [LICENSE](LICENSE).
