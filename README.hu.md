# Density Wave — spirálgalaxis-szimuláció

[🇬🇧 English version](README.md)

Interaktív spirálgalaxis a **sűrűséghullám-elmélet** (Lindblad, 1925) alapján: minden csillag a saját, kissé elforgatott ellipszispályáján kering, a spirálkar pedig nem anyag, hanem „forgalmi dugó", ahol a pályák összesűrűsödnek. Egyetlen HTML-fájl, külső függőség nélkül — Canvas 2D + Web Audio, se könyvtár, se kép- vagy hangfájl.

**Élő demó:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Mindkét cím a `main` ág tartalmát szolgálja ki, és minden commit után automatikusan frissül. A kezelőfelület nyelve angol; ez a leírás magyarul ismerteti.

## Letöltés, saját használat

Az egész alkalmazás egyetlen fájl. Töltsd le az [index.html](index.html)-t (vagy klónozd a repót), nyisd meg böngészőben — kész, nincs telepítés, nincs build, internetkapcsolat sem kell hozzá.

```bash
git clone https://github.com/laci141/galaxy.git
```

Az oldal alján lévő link mindig visszavezet ide, hogy bárki letölthesse.

## Vezérlők

A vezérlőpanel a **jobb felső sarokban** van. A fejlécében két gomb: a 🔊 a hangot kapcsolja, a **–** pedig egyetlen kerek gombra (☰, ugyanabban a sarokban) zsugorítja a panelt, ami vissza is hozza — így tiszta, takarásmentes képet kapsz a galaxisról. Telefonon a panel alulról felhúzható fiók.

A fejléc alatt van a **nyelvváltó — EN · HU · RO · DE**. Lefordítja a felület összes szövegét, a buboréksúgókat, az értesítéseket, valamint a paletta- és készletneveket is, és a számformátumot is átállítja (12,000 / 12 000 / 12.000). A választás megmarad (`localStorage`), belekerül a megosztható linkbe (`?lang=hu`), és első látogatáskor automatikusan a böngésző nyelvét veszi át, ha az a négy közül való.

Belül három fül található: **Galaxis**, **Látvány**, **Hang**.

### Galaxis — a galaxis alakja

| Vezérlő | Mit csinál |
|---|---|
| **Hubble type** | Sa → Sc: nagyobb mag + szorosabb karok ↔ kisebb mag + nyitottabb karok |
| **Bar** (küllő) | 0–100%: a belső pályák közös nagytengelyre állnak, így küllős (SB) galaxis születik |
| **Winding** | a karok feltekeredettsége |
| **Spiral arms** | karok száma (1–6) |
| **Zoom** | valódi kamera-zoom 20–200% (a csillagok mérete is együtt skálázódik) |
| **Time speed** | szimulációs sebesség (0–3×) |
| **Inclination** | rálátási szög (0° = szemből) |
| **Presets** | Andromeda · Whirlpool M51 · Barred SBb · Pinwheel — egy kattintásos beállítás-csomagok |
| **Show orbits** | a rejtett ellipszispályák kirajzolása |
| **Material arms** | „mi lenne, ha a kar anyagból állna" — a feltekeredési probléma demója |

### Látvány

| Vezérlő | Mit csinál |
|---|---|
| **Palette** | 4 színvilág: Classic · Indigo Dust · Blue-Gold · Violet Sea |
| **Auto-switch** | 2–3 percenként lassú, 4,5 másodperces áttűnéssel új palettát sorsol |
| **Colour boost** | 0–25%: a palettaszínek telítettsége (alap: 15%) — 0%-on az eredeti, visszafogott színek |
| **Nebula density** | 0–25%: a köd- és felhőrétegek fedettsége (alap: 15%) |
| **Star count** | desktop 5 000–30 000, mobil 3 000–20 000 |
| **Performance guard** | ha az fps 35 alá esik, automatikusan visszaveszi a csillagszámot |
| **Supernovae** | ritkán felvillanó csillag lágyan táguló fénygömbbel (hanggal is) |
| **Parallax** | egérmozgásra / mobil-döntésre a háttér és a galaxis elcsúszik → mélységérzet |
| **Milky Way band** | átlós, sűrű csillagfolyam sötét porcsíkokkal a háttérben |
| **📷 Photo (PNG)** | felület nélküli mentés, ahol fér, kétszeres felbontásban |
| **🔗 Copy link** | az összes beállítás belekerül az URL-be, így megosztható |
| **🎬 Projector** | teljes képernyős, lassú kamera-utazás automatikus palettaváltással |
| **👁 Hide UI** | vezérlők elrejtése |

### Hang — procedurális kozmikus hangtér

Nincs hangfájl: minden réteget a Web Audio API generál élőben. Rétegenként külön csúszka, mint egy hangkeverőben.

| Réteg | Mi szól |
|---|---|
| **Deep-space drone** | mély, lassan lélegző alapharmónia szűrt oszcillátorokból |
| **Solar wind** | rózsaszín zaj sávszűrőn, 30–50 másodperces hullámzással |
| **Stardust chimes** | ritka, visszhangos harangszerű hangok pentaton skálán |
| **Pulsar pulse** | lassú, mély dobbanás (alapból kikapcsolva) |
| **Follow the simulation** | a zoom a hangszínt, az idősebesség a csengők ritmusát vezérli |

A hang a panel fejlécében lévő 🔊 gombbal vagy az **M** billentyűvel is kapcsolható (böngésző-szabály miatt csak felhasználói kattintás után indulhat).

### Billentyűk

`H` felület el/be · `F` teljes képernyő · `P` fotó · `Space` vetítő mód · `Esc` kilépés · `M` hang

## Miért néz ki minden zoomon/képernyőn ugyanúgy?

A korábbi változatban a csillag-sprite-ok mérete fix CSS-pixelben volt megadva, míg a galaxis mérete az ablakhoz igazodott. A böngésző-zoom a CSS-viewportot átméretezi, ezért 50%-on finom, 70%-on elhízott, kiégett kép jött létre — additív (`lighter`) keverésnél az átfedő csillagok fénye összeadódik, így a sűrűsödés hatványozottan „fehérít".

A mostani változatban:

- **Világ-lépték:** minden kirajzolt elem mérete a galaxis sugarából származik (`g = scl/REF`), így a csillag/galaxis arány és az additív fedési sűrűség minden zoomnál, ablakméretnél és DPI-nél azonos.
- **Éles sprite-ok:** a sprite-bitmapek az aktuális lépték × `devicePixelRatio` felbontásban készülnek újra (nyugalomban 1:1 pixelblit), a `devicePixelRatio` változását `matchMedia` figyeli.
- **Fényerő-normalizálás:** több csillagnál a csillagonkénti fényerő arányosan csökken, így 30 000 csillagnál is sűrűbb lesz a kép, nem fehérebb.
- **Stabil kép:** seedelt véletlengenerátor — ablakméretezés, zoom vagy palettaváltás nem keveri újra a galaxist.
- **Kettős időalap:** a pályák felülvágott `dt`-vel lépnek (numerikus stabilitás), de az áttűnések, szupernóvák és a vetítő mód kameramozgása valós időt használ, így lassú gépen sem húzódnak el.
- **Képernyőn kívüli elemek kihagyása:** erős zoomnál a látótéren kívüli csillagok nem kerülnek kirajzolásra.

## Ellenőrzés

Playwright + Chromium, azonos fizikai felbontásra normált képek 50 / 70 / 100% szimulált böngésző-zoomon:

| Mérés | Régi | Mostani |
|---|---|---|
| Fényerő 50% → 100% zoomon | +77% | azonos (21,1 → 21,6) |
| Kiégett fehér pixelarány | 8× nőtt | változatlan (0,042%) |
| Pixel-eltérés a zoomszintek között | — | ≤ 0,55/255 |

További ellenőrzött viselkedés: csillagcsúszka 5 000 → 30 000 (fényerő 1,39× — nő, de nem ég ki), mobil-tartomány 3 000–20 000, a 4 paletta minimális színtávolsága 6,92 (a 15%-os erősítés előtt 5,71), küllő 0 → 90% látható változás, presetek, szupernóva-életciklus, 3200×1800-as PNG-mentés, link-megosztás és -visszaállítás, hangmotor indulás/leállás, teljesítményőr, vetítő mód, a panel jobb felső helyzete a fejlécbe került hanggombbal, kicsinyítés/visszaállítás, mind a négy nyelv lefordítja az 56 felületi szöveg mindegyikét (nincs üres vagy fordítatlan kulcs), GitHub-link láthatósága, mobil fiók és fülek vízszintes görgetés nélkül. Konzolhiba nincs.

## Futtatás, tesztelés

Nyisd meg az `index.html`-t böngészőben (vagy `python3 -m http.server` és http://localhost:8000).

URL-paraméterek: `?seed=42` determinisztikus galaxis · `?freeze` állókép · `?fps=0` teljesítményőr ki · `?lang=en|hu|ro|de` felületnyelv · minden vezérlő értéke is átadható (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `sat`, `neb`, `auto`, `sn`, `px`, `band`, `orb`, `mat`) — ezt állítja elő a **🔗 Copy link** gomb.

## Licenc

MIT — lásd [LICENSE](LICENSE). Szabadon használható, módosítható, megosztható.
