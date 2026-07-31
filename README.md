# Density Wave — spirálgalaxis-szimuláció

Interaktív spirálgalaxis a **sűrűséghullám-elmélet** (Lindblad, 1925) alapján: minden csillag a saját, kissé elforgatott ellipszispályáján kering, a spirálkar pedig nem anyag, hanem „forgalmi dugó", ahol a pályák összesűrűsödnek. Egyetlen HTML-fájl, külső függőség nélkül (Canvas 2D).

**Élő demó:** https://laci141.github.io/galaxy/ *(GitHub Pages: Settings → Pages → Deploy from a branch → `main` / root — az első bekapcsolás után pár perc)*

## Vezérlők

| Vezérlő | Mit csinál |
|---|---|
| **Hubble type** | Sa → Sc: nagyobb mag + szorosabb karok ↔ kisebb mag + nyitottabb karok |
| **Zoom** | valódi kamera-zoom 20–200% (a csillagok mérete is együtt skálázódik) |
| **Winding** | a karok feltekeredettsége |
| **Spiral arms** | karok száma (1–6) |
| **Time speed** | szimulációs sebesség |
| **Inclination** | rálátási szög (0° = szemből) |
| **Show orbits** | a rejtett ellipszispályák megjelenítése |
| **Material arms** | „mi lenne, ha a kar anyagból állna" — a feltekeredési probléma demója |

## Miért néz ki minden zoomon/képernyőn ugyanúgy? (a javítás lényege)

A korábbi változatban a csillag-sprite-ok mérete fix CSS-pixelben volt megadva, míg a galaxis mérete az ablakhoz igazodott. A böngésző-zoom a CSS-viewportot átméretezi, ezért 50%-on finom, 70%-on elhízott, kiégett kép jött létre — additív (`lighter`) keverésnél az átfedő csillagok fénye összeadódik, így a sűrűsödés hatványozottan „fehérít".

A mostani változatban:

- **Világ-lépték:** minden kirajzolt elem (csillag, köd, H-II régió, porsáv, mag) mérete a galaxis sugarából származik (`g = scl/REF`), így a csillag/galaxis arány és az additív fedési sűrűség **minden zoomnál, ablakméretnél és DPI-nél azonos**.
- **Éles sprite-ok:** a sprite-bitmapek az aktuális lépték × `devicePixelRatio` felbontásban készülnek újra (nyugalomban 1:1 pixelblit), a `devicePixelRatio` változását `matchMedia` figyeli.
- **Stabil kép átméretezéskor:** seedelt véletlengenerátor — ablakméretezés/zoom nem keveri újra a galaxist.
- **Csillagszám a kijelzőhöz igazítva:** telefon (<600 px) 5000, közepes (<1000 px) 9000, PC 12000 csillag.

Ellenőrzés (Playwright + Chromium, azonos fizikai felbontásra normált képek): 50% / 70% / 100% böngésző-zoomon a kirajzolt kép pixel-eltérése ≤ 0,3/255, fényerőszórása < 0,5% — a régi változatban ugyanez +77% fényerő és 8× kiégett-fehér arány volt.

## Futtatás, tesztelés

Nyisd meg az `index.html`-t böngészőben (vagy `python3 -m http.server` és http://localhost:8000).

URL-paraméterek teszteléshez: `?seed=42` — determinisztikus galaxis; `?freeze` — állókép (animáció és „lélegzés" nélkül). Példa: `index.html?seed=42&freeze`.

## Licenc

MIT — lásd [LICENSE](LICENSE).
