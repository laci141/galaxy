# Density Wave — spirálgalaxis-szimuláció

[🇬🇧 English version](README.md)

Interaktív spirálgalaxis a **sűrűséghullám-elmélet** (Lindblad, 1925) alapján: minden csillag a saját, kissé elforgatott ellipszispályáján kering, a spirálkar pedig nem anyag, hanem „forgalmi dugó", ahol a pályák összesűrűsödnek. Egyetlen HTML-fájl, külső függőség nélkül — Canvas 2D + Web Audio, se könyvtár, se kép- vagy hangfájl.

**Élő demó:** https://laci141.github.io/galaxy/ (GitHub Pages) · https://galaxy-90m.pages.dev/ (Cloudflare Pages)

Mindkét cím a `main` ág tartalmát szolgálja ki, és minden commit után automatikusan frissül. A kezelőfelület négy nyelven beszél (EN · HU · RO · DE), a panel tetején lévő gombokkal váltható.

## Letöltés, saját használat

Az egész alkalmazás egyetlen fájl. Töltsd le az [index.html](index.html)-t (vagy klónozd a repót), nyisd meg böngészőben — kész, nincs telepítés, nincs build, internetkapcsolat sem kell hozzá.

```bash
git clone https://github.com/laci141/galaxy.git
```

Az oldal alján lévő link mindig visszavezet ide, hogy bárki letölthesse.

## Vezérlők

A vezérlőpanel a **jobb felső sarokban** van. A fejlécében két gomb: a 🔊 a hangot kapcsolja, a **–** pedig egyetlen kerek gombra (☰, ugyanabban a sarokban) zsugorítja a panelt, ami vissza is hozza — így tiszta, takarásmentes képet kapsz a galaxisról. Telefonon a panel alulról felhúzható fiók.

A fejléc alatt van a **nyelvváltó — EN · HU · RO · DE**. Lefordítja a felület összes szövegét, a buboréksúgókat, az értesítéseket, valamint a paletta- és készletneveket is, és a számformátumot is átállítja (12,000 / 12 000 / 12.000). A választás megmarad (`localStorage`), belekerül a megosztható linkbe (`?lang=hu`), és első látogatáskor automatikusan a böngésző nyelvét veszi át, ha az a négy közül való.

Belül négy fül található: **Galaxis**, **Látvány**, **Hang**, **Tudomány**.

### Galaxis — a galaxis alakja

| Vezérlő | Mit csinál |
|---|---|
| **Hubble type** | Sa → Sc: nagyobb mag + szorosabb karok ↔ kisebb mag + nyitottabb karok |
| **Bar** (küllő) | 0–100%: a belső pályák közös nagytengelyre állnak, így küllős (SB) galaxis születik |
| **Winding** | a karok feltekeredettsége |
| **Spiral arms** | karok száma (1–5) |
| **Zoom** | valódi kamera-zoom 10–100% (a csillagok mérete is együtt skálázódik) |
| **Time speed** | szimulációs sebesség (0–3×) |
| **Inclination** | rálátási szög (0° = szemből) |
| **Presets** | Andromeda · Whirlpool M51 · Barred SBb · Pinwheel — egy kattintásos beállítás-csomagok |
| **Show orbits** | a rejtett ellipszispályák kirajzolása |
| **Material arms** | „mi lenne, ha a kar anyagból állna" — a feltekeredési probléma demója |

#### Automatikus mozgatás

Három csúszka magától is tud dolgozni. A **Zoom**, az **Idősebesség** és a **Dőlésszög** alatt közvetlenül ott van egy kapcsoló és mellé a saját **Köridő** csúszkája, így a kép magától vándorol — jó képernyővédőnek, kivetítéshez vagy videófelvételhez, anélkül hogy bárkinek a panelhez kellene nyúlnia.

| Kapcsoló | Mi között ingázik | Köridő | Alapérték |
|---|---|---|---|
| **Automatikus zoom** | 10% ↔ 90% | 30 mp – 3 perc | 2:00 |
| **Automatikus idősebesség** | 0,2× ↔ 2,8× | 30 mp – 3 perc | 2:00 |
| **Automatikus dőlésszög** | 10° ↔ 70° | 30 mp – 3 perc | 3:00 |

A köridő a **teljes oda-vissza út**, és `P:MP` alakban látszik a csúszka mellett: a 3:00 tehát azt jelenti, hogy másfél perc alatt dől meg a galaxis 10°-ról 70°-ra, és újabb másfél perc alatt jön vissza.

Néhány részlet, amitől nem gépiesnek hat:

- **Koszinuszos lassítás.** Az érték a `min + (max−min)·(0,5 − 0,5·cos 2πφ)` görbét követi, ezért a két véghelyzet közelében lelassul és elidőzik, nem pattan vissza róluk.
- **Bekapcsoláskor nincs ugrás.** A fázis a csúszka aktuális értékéből indul (`φ = acos(1−2u)/2π`), így a mozgás pontosan onnan folytatódik, ahol otthagytad.
- **Valós idő.** A fázis a faliórán halad, nem a szimulációs lépéseken, ezért a köridő menet közbeni átállítása megnyújtja a mozgást ahelyett, hogy megrántaná — és lassú gépen sem lassul le a vándorlás.
- **A kézi állítás az erősebb.** Ha kézzel hozzányúlsz a Zoom, az Idősebesség vagy a Dőlésszög csúszkájához, a hozzá tartozó automatika kikapcsol, tehát sosem küzd veled a vezérlésért.
- Mindhárom kapcsoló és köridő belekerül a megosztható linkbe, és mind a négy felületnyelvre le van fordítva.

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

### Tudomány — mit állít és mit nem a modell

A negyedik fül sima olvasnivaló: hogyan működik a modell (egymásba ágyazott ellipszispályák sugárral együtt forduló nagytengellyel, a küllő a belső pályák egy irányba állításából), mit hagy ki szándékosan (nincs csillagok közti gravitáció, nincs önkonzisztens sűrűséghullám, a szupernóvák és a Tejút-sáv csak látványelemek, a presetek alaktani hasonlóságok, nem fizikai modellek), és mire támaszkodik — Lindblad 1925, Lin & Shu 1964, Binney & Tremaine 2008, Shu 1982, mindegyik ADS-re, DOI-ra vagy a kiadói oldalra linkelve.

A galaxis alatt ugyanez bővebben is olvasható egy legörgethető cikkben, amit a képernyő alján lévő **Görgess a tudományért ↓** gomb nyit meg. Ez valódi szöveg a HTML-ben, tehát a keresők és a JavaScript nélküli olvasók is megkapják.

### Billentyűk

`H` felület el/be · `F` teljes képernyő · `P` fotó · `V` vetítő mód · `Esc` kilépés · `M` hang

A Space szándékosan szabadon maradt, hogy továbbra is görgesse az oldalt, és egyik gyorsbillentyű sem sül el, amíg csúszkán, gombon vagy legördülőn van a fókusz.

## Miért néz ki minden zoomon/képernyőn ugyanúgy?

A korábbi változatban a csillag-sprite-ok mérete fix CSS-pixelben volt megadva, míg a galaxis mérete az ablakhoz igazodott. A böngésző-zoom a CSS-viewportot átméretezi, ezért 50%-on finom, 70%-on elhízott, kiégett kép jött létre — additív (`lighter`) keverésnél az átfedő csillagok fénye összeadódik, így a sűrűsödés hatványozottan „fehérít".

A mostani változatban:

- **Világ-lépték:** minden kirajzolt elem mérete a galaxis sugarából származik (`g = scl/REF`), így a csillag/galaxis arány és az additív fedési sűrűség minden zoomnál, ablakméretnél és DPI-nél azonos.
- **Éles sprite-ok:** a sprite-bitmapek az aktuális lépték × `devicePixelRatio` felbontásban készülnek újra (nyugalomban 1:1 pixelblit), a `devicePixelRatio` változását `matchMedia` figyeli.
- **Fényerő-normalizálás:** több csillagnál a csillagonkénti fényerő arányosan csökken, így 30 000 csillagnál is sűrűbb lesz a kép, nem fehérebb.
- **Stabil kép:** seedelt véletlengenerátor — ablakméretezés, zoom vagy palettaváltás nem keveri újra a galaxist.
- **Kettős időalap:** a pályák felülvágott `dt`-vel lépnek (numerikus stabilitás), de az áttűnések, szupernóvák, a vetítő mód kameramozgása és az automatikus csúszkák valós időt használnak, így lassú gépen sem húzódnak el.
- **Képernyőn kívüli elemek kihagyása:** erős zoomnál a látótéren kívüli csillagok nem kerülnek kirajzolásra.

## Ellenőrzés

Playwright + Chromium, azonos fizikai felbontásra normált képek 50 / 70 / 100% szimulált böngésző-zoomon:

| Mérés | Régi | Mostani |
|---|---|---|
| Fényerő 50% → 100% zoomon | +77% | azonos (21,1 → 21,6) |
| Kiégett fehér pixelarány | 8× nőtt | változatlan (0,042%) |
| Pixel-eltérés a zoomszintek között | — | ≤ 0,55/255 |

További ellenőrzött viselkedés: csillagcsúszka 5 000 → 30 000 (fényerő 1,39× — nő, de nem ég ki), mobil-tartomány 3 000–20 000, a 4 paletta minimális színtávolsága 6,92 (a 15%-os erősítés előtt 5,71), küllő 0 → 90% látható változás, presetek, szupernóva-életciklus, 3200×1800-as PNG-mentés, link-megosztás és -visszaállítás, hangmotor indulás/leállás, teljesítményőr, vetítő mód, a panel jobb felső helyzete a fejlécbe került hanggombbal, kicsinyítés/visszaállítás, GitHub-link láthatósága, mobil fiók és fülek vízszintes görgetés nélkül. Konzolhiba nincs.

Az automatikus mozgatásnak külön futása van: a dőlésszög 11° → 58° között mozog és bent marad a 10–70°-os sávban, a zoom 65% → 90% a 10–90%-os sávban, az idősebesség 1,18× → 2,78× a 0,2–2,8× sávban, a 90 másodperces köridő `1:30`-ként jelenik meg, a dőlésszög-csúszka kézi elhúzása kikapcsolja az automatikát és 45°-on tartja az értéket, a kapcsoló és a köridő pedig túléli a link-megosztást és -visszaállítást. Mellette a szerkezet és a hozzáférhetőség: egyetlen `<h1>`, az 547 szavas cikk ott van a kiszolgált HTML-ben, mind a 17 csúszkához tartozik `label[for]`, mind a 12 kapcsoló `button[role="switch"]`, a `Space` görgeti az oldalt gyorsbillentyű helyett, a `V` indítja a vetítő módot, és mind a négy nyelv üres szöveg nélkül jelenik meg.

## Akadálymentesítés és megtalálhatóság

- Minden csúszkához valódi `<label for>` tartozik, minden kapcsoló `<button role="switch">` `aria-checked` állapottal, a billentyűzet-fókusz pedig mindig látszik (`:focus-visible` keret).
- A gyorsbillentyűk sosem sülnek el fókuszban lévő vezérlőről, a görgetés és a rétegátmenetek pedig figyelik a `prefers-reduced-motion` beállítást.
- A `robots.txt` és a `sitemap.xml` a gyökérből szolgálódik ki; az oldalon kanonikus URL, Open Graph- és Twitter-kártya-adatok vannak `og-image.png`-vel (1200×630), valamint JSON-LD, ami `EducationalApplication`-ként, négy nyelven, a három fő hivatkozással írja le.

## Futtatás, tesztelés

Nyisd meg az `index.html`-t böngészőben (vagy `python3 -m http.server` és http://localhost:8000).

URL-paraméterek: `?seed=42` determinisztikus galaxis · `?freeze` állókép · `?fps=0` teljesítményőr ki · `?lang=en|hu|ro|de` felületnyelv · minden vezérlő értéke is átadható (`hub`, `bar`, `wind`, `arms`, `zoom`, `spd`, `inc`, `stars`, `pal`, `sat`, `neb`, `auto`, `sn`, `px`, `band`, `orb`, `mat`) · és az automatikák a köridejükkel másodpercben (`ainc`, `aincT`, `azoom`, `azoomT`, `aspd`, `aspdT`) — ezt állítja elő a **🔗 Copy link** gomb.

## Licenc

MIT — lásd [LICENSE](LICENSE). Szabadon használható, módosítható, megosztható.
