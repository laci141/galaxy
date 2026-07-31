# Density Wave — spirálgalaxis-szimuláció

[🇬🇧 English version](README.en.md)

Interaktív spirálgalaxis a **sűrűséghullám-elmélet** (Lindblad, 1925) alapján: minden csillag a saját, kissé elforgatott ellipszispályáján kering, a spirálkar pedig nem anyag, hanem „forgalmi dugó", ahol a pályák összesűrűsödnek. Egyetlen HTML-fájl, külső függőség nélkül — Canvas 2D + Web Audio, se könyvtár, se kép- vagy hangfájl.

**Élő demó:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Mindkét cím a `main` ág tartalmát szolgálja ki, és minden commit után automatikusan frissül.

## Vezérlők

A panel három fülre oszlik: **Galaxis**, **Látvány**, **Hang**.

### Galaxis

| Vezérlő | Mit csinál |
|---|---|
| **Hubble-típus** | Sa → Sc: nagyobb mag + szorosabb karok ↔ kisebb mag + nyitottabb karok |
| **Küllő (bar)** | 0–100%: a belső pályák közös nagytengelyre állnak, így küllős (SB) galaxis születik |
| **Feltekeredés** | a karok feltekeredettsége |
| **Spirálkarok** | karok száma (1–6) |
| **Zoom** | valódi kamera-zoom 20–200% (a csillagok mérete is együtt skálázódik) |
| **Idősebesség** | szimulációs sebesség (0–3×) |
| **Dőlésszög** | rálátási szög (0° = szemből) |
| **Készletek** | Andromeda · Örvény M51 · Küllős SBb · Szélkerék — egy kattintásos beállítás-csomagok |
| **Pályák megjelenítése** | a rejtett ellipszispályák kirajzolása |
| **Anyagi karok** | „mi lenne, ha a kar anyagból állna" — a feltekeredési probléma demója |

### Látvány

| Vezérlő | Mit csinál |
|---|---|
| **Színvilág** | 4 paletta: Klasszikus · Indigó por · Kék-arany · Ibolya-tenger |
| **Automatikus váltás** | 2–3 percenként lassú, 4,5 másodperces áttűnéssel új palettát sorsol |
| **Csillagszám** | desktop 5 000–30 000, mobil 3 000–20 000 |
| **Teljesítményőr** | ha az fps 35 alá esik, automatikusan visszaveszi a csillagszámot |
| **Szupernóvák** | ritkán felvillanó csillag táguló lökéshullámmal (hanggal is) |
| **Parallax** | egérmozgásra / mobil-döntésre a háttér és a galaxis elcsúszik → mélységérzet |
| **Tejút-sáv** | átlós, sűrű csillagfolyam sötét porcsíkokkal a háttérben |
| **📷 Fotó (PNG)** | felület nélküli mentés, ahol fér, kétszeres felbontásban |
| **🔗 Link másolása** | az összes beállítás belekerül az URL-be, így megosztható |
| **🎬 Vetítő mód** | teljes képernyős, lassú kamera-utazás automatikus palettaváltással |
| **👁 Felület el** | vezérlők elrejtése |

### Hang — procedurális kozmikus hangtér

Nincs hangfájl: minden réteget a Web Audio API generál élőben. Rétegenként külön csúszka, mint egy hangkeverőben.

| Réteg | Mi szól |
|---|---|
| **Mélyűr-drón** | mély, lassan lélegző alapharmónia szűrt oszcillátorokból |
| **Szoláris szél** | rózsaszín zaj sávszűrőn, 30–50 másodperces hullámzással |
| **Csillagpor-csengők** | ritka, visszhangos harangszerű hangok pentaton skálán |
| **Pulzár-lüktetés** | lassú, mély dobbanás (alapból kikapcsolva) |
| **Kövesse a szimulációt** | a zoom a hangszínt, az idősebesség a csengők ritmusát vezérli |

A hang a bal felső 🔊 gombbal vagy az **M** billentyűvel is kapcsolható (böngésző-szabály miatt csak felhasználói kattintás után indulhat).

### Billentyűk

`H` felület el/be · `F` teljes képernyő · `P` fotó · `Space` vetítő mód · `Esc` kilépés · `M` hang

## Miért néz ki minden zoomon/képernyőn ugyanúgy?

A korábbi változatban a csillag-sprite-ok mérete fix CSS-pixelben volt megadva, míg a galaxis mérete az ablakhoz igazodott. A böngésző-zoom a CSS-viewportot átméretezi, ezért 50%-on finom, 70%-on elhízott, kiégett kép jött létre — additív (`lighter`) keverésnél az átfedő csillagok fénye összeadódik, így a sűrűsödés hatványozottan „fehérít".

A mostani változatban:

- **Világ-lépték:** minden kirajzolt elem mérete a galaxis sugarából származik (`g = scl/REF`), így a csillag/galaxis arány és az additív fedési sűrűség minden zoomnál, ablakméretnél és DPI-nél azonos.
- **Éles sprite-ok:** a sprite-bitmapek az aktuális lépték × `devicePixelRatio` felbontásban készülnek újra (nyugalomban 1:1 pixelblit), a `devicePixelRatio` változását `matchMedia` figyeli.
- **Fényerő-normalizálás:** több csillagnál a csillagonkénti fényerő arányosan csökken, így 30 000 csillagnál is sűrűbb lesz a kép, nem fehérebb.
- **Stabil kép:** seedelt véletlengenerátor — ablakméretezés, zoom vagy palettaváltás nem keveri újra a galaxist.
- **Képernyőn kívüli elemek kihagyása:** erős zoomnál a látótéren kívüli csillagok nem is kerülnek kirajzolásra.

## Ellenőrzés

Playwright + Chromium, azonos fizikai felbontásra normált képek 50 / 70 / 100% szimulált böngésző-zoomon:

| Mérés | Régi | Mostani |
|---|---|---|
| Fényerő 50% → 100% zoomon | +77% | azonos (20,4 → 20,9) |
| Kiégett fehér pixelarány | 8× nőtt | változatlan (0,043%) |
| Pixel-eltérés a zoomszintek között | — | ≤ 0,55/255 |

További ellenőrzött viselkedés: csillagcsúszka 5 000 → 30 000 (fényerő 1,38× — nő, de nem ég ki), mobil-tartomány 3 000–20 000, 4 paletta jól elkülönülő színvilággal és működő áttűnéssel, küllő 0 → 90% látható változás, presetek, szupernóva-életciklus, 3200×1800-as PNG-mentés, link-megosztás és -visszaállítás, hangmotor indulás/leállás, teljesítményőr, vetítő mód, mobil fiók és fülek vízszintes görgetés nélkül. Konzolhiba nincs.

## Futtatás, tesztelés

Nyisd meg az `index.html`-t böngészőben (vagy `python3 -m http.server` és http://localhost:8000).

URL-paraméterek: `?seed=42` determinisztikus galaxis · `?freeze` állókép · `?fps=0` teljesítményőr ki · minden vezérlő értéke is átadható (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `auto`, `sn`, `px`, `band`, `orb`, `mat`) — ezt állítja elő a **🔗 Link másolása** gomb.

## Licenc

MIT — lásd [LICENSE](LICENSE).
