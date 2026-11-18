---
name: motion-infografikk-film
description: Lag forklarende infografikk- og motion graphics-filmer fra et hvilket som helst manus (forklaringsfilm, pitch, strategi, prosess, metode, datahistorie, animatic, animert modell), bygget i kode som én interaktiv HTML-side med spiller, kapitler og synkront manus, og eksportert til MP4 med lydbed. Har to ferdige stilvalg med forklaring av uttrykket, Punktet (inspirert av Apple «Intention») og Tråden (fra en trådbasert forklaringsfilm om KI-styring), et bibliotek av former, gester og infografikk-grep, standardvalg og selvkontroll, så den kan kjøre hele løpet uten at brukeren koder. Bruk den når noen vil lage, planlegge, storyboarde, animere, måle eller forbedre en motion graphics- eller infografikkfilm, også når de bare sier «lag en film av dette manuset», «animer trinnene», «gjør det som en Apple-reklame», «explainer video» eller «animated infographic».
---

# Motion-infografikk i kode

Denne skillen gjør et manus om til en ferdig forklaringsfilm. Filmen tegnes i canvas fra et tidskart, og den samme HTML-siden er både det publikum ser (spill, skrubb, kapitler, manus) og kilden som eksporteres til MP4 bilde for bilde. Du kjører hele løpet selv: tidskart, stil, gester, motor, kontroll, lyd og levering. Brukeren trenger ikke kode, bare et manus eller et tema.

Grammatikken bygger på to filmer som er analysert bilde for bilde, og som gir hvert sitt stilvalg:

- **Punktet**, inspirert av Apple «Intention» (åpningsfilm WWDC 2013, TBWA\Media Arts Lab): stillhet, så én bokstavelig gest på ordet. Punktet er pennen.
- **Tråden**, fra en trådbasert forklaringsfilm om KI-styring: ett materiale som skifter form for hver fase, hvit flate, venstre kolonne og en sluttmodell man kan peke på.

Begge er stilreferanser, ikke innhold. Filmen skal handle om brukerens tema og se ut som brukerens avsender.

## Arbeidsgang

Lag en oppgaveliste med stegene under når verktøyet finnes, så brukeren kan følge med. Feil som oppdages i tidskartet koster ett minutt, feil som oppdages etter eksport koster en time, så rekkefølgen er viktig.

1. **Les og avklar** (steg 1). Still høyst fire spørsmål.
2. **Tidskart** (steg 2). Dramaturgi, tider, skjermtekst og stillheter i én tabell.
3. **Stil** (steg 3). Punktet, Tråden eller en blanding.
4. **Materiale og formkjede** (steg 4).
5. **Gestkart og infografikk** (steg 5).
6. **Styleframes** (steg 6). Tre bilder til godkjenning før hele filmen.
7. **Bygg** (steg 7). Fyll tabellene i `film.html`.
8. **Selvkontroll** (steg 8). Kontaktark, rytmemåling, rett opp, høyst to runder.
9. **Lyd** (steg 9). Lydbed fra tidskartet.
10. **Levering** (steg 10). Side, MP4, lydbed og et kort notat.

### Standardvalg

Bruk disse når brukeren ikke har sagt noe annet, og skriv valgene øverst i tidskartet:

| Valg | Standard |
|---|---|
| Format | 1920 × 1080, 30 bilder/s, lengde fra manuset |
| Stil | Blanding: Tråden som materiale for prosess, metode og system; Punktet for holdning, visjon og korte filmer |
| Verden | Hvit flate. To verdener (lys og mørk) bare når filmen har et tydelig vendepunkt fra spørsmål til svar |
| Farger | Den nøytrale paletten under «Farger som roller», eller avsenderens farger fylt inn i rollene |
| Font | Avsenderens font. Ellers Mona Sans (åpen lisens) hentet med `font.py` |
| Skjermtekst | Bare tekst fra manuset, pluss én kildelinje under hvert tall |
| Lyd | Syntetisert lydbed fra tidskartet. Ingen stemme med mindre den er spilt inn |
| Levering | Interaktiv side + MP4 1080p + lydbed (WAV og MP3) |

## Steg 1: Les og avklar

Still bare spørsmålene du ikke kan svare på selv, med en anbefaling i hvert (bruk AskUserQuestion når verktøyet finnes):

- **Manus.** Er manuset låst? Finnes VO-innganger eller innspilt stemme? Uten stemme: tidfest på anslag, ca. 2–2,5 ord per sekund med pauser, og si at tidene er anslag.
- **Avsender.** Farger, font, logo. Mangler de: standardvalgene.
- **Stil og verden.** Anbefal etter tabellen over.
- **Levering.** Bare siden, eller også MP4, lydbed og kortversjoner?

Mangler manuset helt, skriv et utkast etter dramaturgimalene i steg 2: én setning per gest, korte skjermtekster, og få det godkjent før du animerer. Er brukeren ikke til stede, velg standardvalgene og fortsett.

## Steg 2: Dramaturgi og tidskart

Velg en dramaturgi. Begge referansene går fra uro til ro: mye bevegelse og mange elementer i problemet, så roligere fase for fase, og en signatur i stillhet.

| Mal | Delene | Passer for |
|---|---|---|
| **A Metode** (Tråden) | Anslag (én) → Vekst (mange) → Problemet (uro, spørsmål, stillhet) → Vendepunkt (retning) → Faser med trinn → Modell → Signatur | Metoder, prosesser, rammeverk, strategi i trinn |
| **B Holdning** (Punktet) | Påstand → Mange eksempler (tempo) → Spørsmålet (mørk verden) → Vendepunkt (lys) → Én konklusjon → Signatursetning | Visjon, verdier, lansering |
| **C Datahistorie** | Påstand → Tallet → Sammenligning → Konsekvens → Hva vi gjør → Signatur | Rapporter, resultater, innsikt |

Fordeling av tiden (andel av lengden): anslag 5–8 %, problemet 25–30 %, vendepunkt 8–10 %, hoveddelen 40–50 %, modell og signatur 10–12 %. Behold rundt 5 sekunder hale etter siste setning.

Tidskartet er kontrakten for filmen. Lag det som en tabell før du tegner:

| Tid | Del | VO | Skjermtekst | Gest | Form |
|---|---|---|---|---|---|
| 0:00,8 | Anslag | Alt starter med én idé. | Det starter med én idé. | Punktet settes med et lite sprett (1,2) | Én prikk |
| 0:05,2 | Mengden | …de fleste blir aldri noe av. | Så blir det mange. · 7 av 10 | 70 av 100 prikker blir hule (8,0) | Rutenett 10 × 10 |
| 0:14,6 | Uro | Til slutt står alt stille. | – | Alt fryser, lyden går ut, 1 s | Spredt |

Regler:

- VO-innganger og skjermtekst er låst når manuset er låst. Endringer i uttrykk rører aldri ordlyd eller tider.
- Hver gest starter på ordet den viser, ikke før. En påstand skal ikke stå på skjermen mens stemmen snakker om noe annet.
- Stillheter (bilde og lyd) står med fra–til, på ordet som handler om stillhet, stopp eller alvor.
- Siste setning står alene. Avsender eller logo kommer 0,8 s etter.

## Steg 3: Velg stil

### Stilvalg A: Punktet (inspirert av Apple «Intention»)

Slik ser Intention ut, målt på alle 2 179 bilder (1:31):

- **Ett punkt er atom og penn.** Alt starter som et punkt. Punkter i bevegelse tegner streker, og strekene lukkes til former: punkt → strek → sirkel. Publikum lærer grammatikken på ti sekunder.
- **Ordet blir handling.** 28 visuelle handlinger på 88 sekunder, omtrent én hvert tredje sekund. «Focus» er én skarp skive blant uskarpe. «Simplify» strekker punktet til en strek, «perfect» gjør streken til en sirkel. «Ett ja» er linjer fra alle punktene som samles i ett.
- **Stillhet og gest.** 30 % av bildene står helt stille, men de travleste sekundene beveger seg ti ganger mer enn et vanlig sekund. Toppene ligger der ordet krever handling, stillheten der teksten står alene.
- **To verdener uten farge.** Lys varmgrå (#D6D1D5, vignett til #CEC9CD) og nesten svart (#1E1A1D), 2,4 % metning, i fem blokker. Mørket kommer når filmen ser innover, lyset når den konstaterer. Skiftene er motivert av noe i bildet: punkter faller inn i mørket, en sirkel vokser til den fyller bildet. Ett hardt kutt i hele filmen, på ordet for overraskelse.
- **Teksten er stemmen.** Én liten sentrert linje, ord for ord. Kommende ord står svakt, ordene forsvinner fra venstre til nøkkelordet står alene. Rundt 60 ord i minuttet.
- **Dybde som mening.** Uskarpt er det uavklarte, skarpt er beslutningen. Brukes bare der ordet handler om fokus eller valg.
- **Fysikk gir varme.** Punkter faller, spretter, smelter og tiltrekkes.
- **Nesten tomt bilde.** Blekk (piksler som skiller seg fra flaten) har median 0,44 %. Signaturen er en setning, ikke en logo.

I motoren: prikker (`P.dot`), verdensskifte som voksende skive (`WORLDS`), tekststilen `linje`, tilstanden `dempet` for fokus, gestene `pop`, `kobling` og `strek`. Demoen i `film.html` er bygget i denne stilen.

### Stilvalg B: Tråden (fra KI-styringsfilmen)

Slik ser trådfilmen ut, målt på alle 3 600 bilder (2:00):

- **Ett hvitt rom uten klipp.** Ren hvit flate med en svak lilla dis bak objektet, 0–1,6 % metning. Ingen mørke partier.
- **Tråden er eneste materiale.** Hver tråd vokser fra et punkt på en grunnlinje, og punktet blir stående som enheten tråden representerer. De samme trådene går gjennom åtte former, én per fase: spørsmålstegn, floke, bånd, stamme, eierkrans, portsøyle, ring og sluttmodell. Hver form forklarer sitt trinn: kransen viser eierskap, portene sortering, ringen drift som henger sammen.
- **Rikdom i objektet.** Vevde porter av mange tynne løkker, perler som vandrer langs trådene, tette strømmer. Tydeligheten kommer fra én gest, stillhet og strekvekt, ikke fra å fjerne tråder.
- **Varme som fremdrift.** Trådene går fra kjølig fiolettgrå mot kobber etter hvert som systemet bygges (varme piksler 0 % → 26 % → 45 % → 55 % gjennom filmen). Styring vises som at materialet skifter temperatur, ikke som en ny farge.
- **Én aksent**, på ett element om gangen: signalet, avviket, beslutningen.
- **Venstre kolonne.** Fasenavn, stort trinntall og trinnavn til venstre. Objektet står til høyre. Korte påstander sentrert over objektet.
- **Sluttmodellen er et verktøy.** Ringen med nummererte noder og fasebuer kan stå alene i en presentasjon.

I motoren: `trad.js` (floke, bånd, løkker, tråder som tegnes av pennen fra grunnlinjen, perler), gesten `port`, tilstanden `varm`, tekststilene `fase` og `trinn`.

### Blanding (anbefalt for forklarende film)

Trådens materiale og formkjede, Punktets rytme: én gest per trinn på ordet, stillhet før og etter, og total stillhet på det alvorligste ordet. Trådfilmen manglet nettopp gester i trinnene. En film bygget slik fikk tydelige topper i trinnene uten å miste roen (se «Måltall»).

## Steg 4: Materiale, betydning og formkjede

Velg **ett** materiale (prikk eller tråd) og gi det en fast betydning som holder hele filmen:

| Form | Betyr (bytt ut med temaets enhet) |
|---|---|
| Prikk eller punkt på grunnlinjen | Én enhet: en kunde, en sak, en idé, en krone |
| Linje, tråd | Enheten i bevegelse. En rett linje er retning |
| Ring | Eierskap, ramme, modell, helhet |
| Hul prikk | Ukjent, tapt eller stanset (stanset, ikke slettet) |
| Dempet | Uavklart, ikke i fokus |
| Aksentfarge | Krever handling: signal, avvik, beslutning |
| Varm farge | Bygget, styrt, ferdig |

Skriv formkjeden som én setning før du tegner, for eksempel «én prikk → rutenett → spredt → linje → søyler → ring». Tittelen eller budskapet skal kunne leses i kjeden, uten klipp.

To regler gjør filmen troverdig. Ordenen kommer fra synlige strukturer (grunnlinje, port, ramme, ring), ikke fra at materialet ordner seg selv. Når temaet er ansvar, risiko eller teknologi, får materialet aldri vilje eller følelser.

## Steg 5: Gestkart og infografikk

Gi hvert trinn eller nøkkelord én bokstavelig handling. Bildet skal gjøre ordet, ikke illustrere det. Gesten varer 0,5–1,5 s, med kort hold før og etter.

| Ordet | Gest | I motoren |
|---|---|---|
| Begynnelse, én | Én prikk settes med et sprett | form `P.dot`, gest `pop` |
| Vekst, mange | Én blir mange; tråder vokser fra grunnlinjen | `MORPHS` fra én prikk til rutenett, eller `REVEAL` på tråder |
| Uro, kaos | Spredte prikker som driver, eller en floke | `LAY.scatter` med `clock`, eller `tangle` i `trad.js` |
| Stillhet, stopp | Alt fryser, lyden går ut, ca. 1 s | `SILENCE` |
| Fokus, se | Én skarp, resten dempet | `STATES`: `aksent` på én, `dempet` på resten |
| Retning | Alt legger seg på én linje med aksent i spissen | form `strek` eller `baand`, gest `strek` |
| Forenkle, perfeksjonere | Punkt → strek → sirkel | to `MORPHS` |
| Beslutte, samle | Linjer fra mange punkter til ett | gest `kobling` |
| Eierskap | En ring lukker seg rundt hver enhet, i rytme | gest `ring` med forskjøvne tider |
| Kartlegge, finne | En lyssøyle feier, skjulte prikker fylles | gest `skann` + `STATES` (`hul` → `normal`) |
| Regel, rammer | En linje trekkes tvers over i ett strøk | gest `strek` |
| Sortere, klassifisere | Elementene legger seg i hver sin gruppe eller port | `LAY.bars` med grupper, gest `port` |
| Fjerne, avvikle | Gruppen faller eller blir hul | `STATES` `hul` eller `borte` |
| Godkjenne, vurdere | Ring og hake, så slippes enheten inn | gest `hake` |
| Spre, lære | Én bølge ut fra midten | gest `bolge` |
| Kontrollere | Enheten stopper ved et sjekkpunkt | gest `stopp` eller `hake` |
| Overvåke, gjenta | Et signal går rundt i fast takt | gest `signal`, puls i lyden |
| Prosess, trinn | Noder tennes én for én rundt en ring | gest `node` + tekst `trinn` |
| Verdensskifte | En skive vokser til den fyller bildet | `WORLDS` |

Bruk **oppsett og utløsning**: plant en halv gest tidlig (en linje som stopper halvveis på spørsmålet) og fullfør den når svaret kommer.

Infografikk-grep, slik at tall blir bilder:

| Hva tallet sier | Grep | I motoren |
|---|---|---|
| Andel («7 av 10») | Rutenett av 100 der andelen skifter tilstand | `LAY.grid` 10 × 10 + `STATES` med `S.share(.7)` |
| Sammenligning (før og etter) | To søyler av prikker med riktig forhold | `LAY.bars(i, [80, 20], …)` |
| Utvikling over tid | Pennen tegner kurven | `P.line` eller `P.svg`-sti med `REVEAL` |
| Del av helhet | Et utsnitt av ringen skifter farge | `LAY.ring` + `STATES` på en indeks-rekke |
| Rangering | Prikkene sorterer seg på en linje | `LAY.row` med indeks etter verdi |
| Telling | Tallet teller opp mens bildet endres | tekststil `tall` med `fra` og `til` |
| Sted, logo, ikon | Elementene tegner formen | `P.svg(d, x, y, skala)` |

Ett tall om gangen, alltid med kildelinje. Eksempeltall merkes tydelig og byttes ut før levering.

## Steg 6: Styleframes

Velg de tre vanskeligste øyeblikkene (vendepunktet, den tetteste fasen, klimakset) og tegn dem i motoren, ikke i et annet verktøy:

```
python3 eksport.py film.html --stills 12,21,27 styleframes/
```

Vis dem til brukeren og få dem godkjent før du bygger resten. Tegner du dem i motoren, blir det godkjente bildet det samme som filmen.

## Steg 7: Bygg i motoren

Kopier `film.html`. Seksjon 1–8 er data du fyller inn, seksjon 9–11 endres sjelden. For Tråden: lim inn `trad.js` i seksjon 5 som beskrevet øverst i den.

| Tabell | Format | Hva den styrer |
|---|---|---|
| `CHAPTERS` | `[start, navn]` | Kapitler i spilleren, pads i lyden, deler i rytmemålingen |
| `SCRIPT` | `[VO-inngang, del, VO, skjermtekst]` | Manuset på siden, synkront med avspillingen |
| `SILENCE` | `[fra, til]` | Klokkene står (bilde) og lyden går helt ut |
| `PAL`, `WORLDS` | palett per verden; `[tid, verden, x, y]` der verden er `'lys'` eller `'mork'` | Farger og verdensskifter. x, y er på skjermen |
| `FORMS`, `FORM0` | navn → `(e, t) ⇒ K punkter` | Formasjonene. Bygg med `P` og `LAY` |
| `MORPHS` | `[start, varighet, til form, rekkefølge]` | Forvandlinger. Rekkefølge: `index`, `x`, `center`, `random`, `none` |
| `REVEAL` | `[start, varighet, velger, rekkefølge]` | Pennen tegner elementet. Skjult før start |
| `STATES` | `[start, varighet, velger, stil]`, sortert på tid | `normal`, `aksent`, `hul`, `varm`, `dempet`, `borte` |
| `GESTURES` | `[tid, type, etikett, parametre]` | Gestene. Etiketten vises i tidslinjen og regilinjene, typen styrer lyden |
| `TEXT` | `[inn, ut, stil, tekst, valg]` | Skjermteksten. Stilene står under tabellen |
| `CAM` | `[start, varighet, skala, fokus i verden, fokus på skjermen]` | Ett kort skyv per gest |

Tekststilene i `TEXT`:

- `midt`, `kolonne`, `fase`, `signatur`: kommer ord for ord i lesetakt og går ut samlet. Valget `y` flytter linjen.
- `trinn`: tall og navn skilt med loddrett strek, for eksempel `'01|Utforsk'`.
- `tall`: mal og kilde skilt med loddrett strek, der `{}` er tallet som teller, for eksempel `'{} av 10|Kilde: navn, år'` med valgene `{ fra: 0, til: 7 }`.
- `linje`: Intention-linjen. Valgene `{ nokkel: 1, ved: 13.2 }` lar ord nummer 1 (fra 0) stå alene fra 13,2 s.
- `avsender`: avsendernavnet, eller bytt til `g.drawImage(logo, …)` i `drawText`.

Velgere: `S.all`, `S.share(p)` (fast, tilfeldig andel), `S.ids(…)`, `S.first(n)`, `S.not(f)` eller en egen funksjon `e => …`. Hvert element har `e.i`, `e.x` (0–1 i rekkefølge), `e.r` (0 i midten, 1 ytterst), `e.rk` (fast tilfeldig rang) og `e.h1–h3` (faste tilfeldige tall).

Gestene tegnes i verdenskoordinater og følger kameraet. Teksten tegnes på skjermen og følger ikke kameraet. Parameteren `life` overstyrer hvor lenge en gest er synlig, og `dur` gir lange gester riktig lengde i lyden.

Prinsippene som gjør motoren robust:

- **`renderFrame(t)` er en ren funksjon av tiden.** Samme t gir alltid samme bilde, så skrubbing, styleframes og eksport blir like. Bruk `hash()` i stedet for `Math.random()`, og aldri klokkeslett. Bevegelse med varierende fart lages med `clock()`, som integrerer farten én gang og stopper i stillhetene.
- **Alle former har K punkter.** Da kan enhver form forvandles til enhver annen. En prikk er K like punkter, så prikk → strek → sirkel er bare to forvandlinger.
- **Forskjøvet overgang.** Hvert element har sitt eget vindu i forvandlingen, så den flyter i stedet for å skje på én gang.
- **Budsjett.** Ett bilde bør ta under 8 ms å tegne. Demoen tar 1–3 ms. Blir det tyngre (hundrevis av lange tråder), bruk modus B: eksporter MP4, bytt canvas med `<video>`, la klokken være `video.currentTime` og behold resten av siden.

### Typografi og layout (1920 × 1080)

| Element | Spesifikasjon |
|---|---|
| Venstre kolonne | Marg 200 px. Tittelsikker sone 5 % (96 / 54 px) |
| `midt` | 84 px Light, sentrert, y 300 |
| `kolonne` | 84 px Light, venstre, y 243 |
| `fase` | 92 px Light, y 243 |
| `trinn` | Tall 182 px Light i materialfargen (y 527), navn 46 px Regular (y 622), aksentstrek 86 × 2,5 px (y 690) |
| `tall` | 150 px Light, teller opp på 1,3 s. Kildelinje 24 px Regular, 60 px under |
| `linje` | Intention-linjen: 58 px Regular, sentrert, y 880, ord for ord, nøkkelordet blir stående |
| `signatur` | 64 px Light, sentrert, y 905. `avsender` eller logo under |
| Strek og prikk | Tråd 1,6 px, prikk 7 px, perle 2,6 px, node 9 px |

Light (300) for store ord, Regular (400) for små. Light blir for tynn under ca. 40 px.

### Farger som roller

| Rolle | Brukes til | Nøytral lys | Nøytral mørk |
|---|---|---|---|
| `flate` | Bakgrunn | #FFFFFF | #15171C |
| `dis` | Radial tone bak objektet | #F2F3F6 | #1E2128 |
| `blekk` | Tekst | #1F2430 | #ECEDF1 |
| `materiale` | Prikker, tråder, trinntall | #4A5468 | #AEB6C6 |
| `myk` | Sekundære linjer, baner | #B8BFCC | #4C5363 |
| `aksent` | Signal, avvik, beslutning. Ett element om gangen | #E8590C | #F27B3A |
| `varm` | Det som er bygget og styrt | #A0674B | #D29A7E |
| `node` | Grunnlinjepunkter og noder | #3D4250 | #C9CED8 |
| `kilde` | Kildelinjer | #6F7480 | #8D93A0 |

Fyll rollene med avsenderens farger. Behold én aksent, og la materialet være en dempet tone av hovedfargen.

## Steg 8: Selvkontroll

Gjør dette før du viser noe til brukeren, høyst to runder:

1. `python3 eksport.py film.html --ark ark.png` og se på kontaktarket. Det viser hvert kapittel og hver gest med tidskode.
2. Se etter:
   - Tekst som ligger over objektet, eller utenfor tittelsikker sone.
   - At hver gest synes på sin tid, og at bildet ikke er tomt eller grått uten grunn.
   - At bare ett element har aksentfarge om gangen.
   - At tall og kildelinjer stemmer med manuset.
   - At siste setning og logo står på låste tider. Sjekk med `--stills` på nøyaktig tid.
   - At hver gest starter på ordet (±0,2 s).
   - At bildet leses nedskalert til telefonstørrelse.
3. Eksporter og mål: `python3 rytme.py film.mp4 tidskart.json`. Sammenlign med «Måltall» og juster gestene.
4. Siden skal ikke ha feil i konsollen (`eksport.py` stopper og skriver dem ut), og den skal fungere på 400 px bredde, i lys og mørk modus og med tastatur.

## Steg 9: Lyd

`lydbed.py` lager lyden fra tidskartet: en pad per kapittel, én lyd per gest (stigende pentatonisk, slik at filmen bygger seg opp), puls for `signal`, klikk og dypt slag for `stopp`, susing for `strek` og `skann`, en klokke når avsenderen kommer, og total stillhet i `SILENCE`. Topp −3 dBFS og snitt rundt −23 dBFS gir plass til en stemme.

```
python3 eksport.py film.html --tidskart tidskart.json
python3 lydbed.py tidskart.json lydbed.wav
ffmpeg -i film.mp4 -i lydbed.wav -map 0:v -map 1:a -c:v copy -c:a aac -b:a 192k -shortest master.mp4
ffmpeg -ss 14.7 -to 15.5 -i master.mp4 -vn -af volumedetect -f null -    # stillheten skal vise under −80 dB
```

Lyden er syntetisert. Si alltid til brukeren at den ikke er hørt av et menneske.

## Steg 10: Levering

| Fil | Slik | Størrelse (2 min) |
|---|---|---|
| Master 1080p | `eksport.py` (crf 16) + lyd som over | ca. 70 MB |
| Kompakt 1080p | `ffmpeg -i master.mp4 -c:v libx264 -crf 26 -preset medium -c:a copy kompakt.mp4` | ca. 26 MB |
| 720p til siden | `-vf scale=1280:720:flags=lanczos -crf 25` | ca. 14 MB |
| Lydbed | WAV 48 kHz og MP3 | 23 MB / 4 MB |

- Eksporten går med ca. 8–9 bilder i sekundet. Lange filmer eksporteres i deler på 60 s (`… del1.mp4 0 60`, `… del2.mp4 60 120`) i forgrunnen, og settes sammen med `ffmpeg -f concat -safe 0 -i liste.txt -c copy film.mp4`.
- Filer som sendes i chatten må være under 30 MB, og binærfiler som publiseres sammen med en artefakt under 15 MB. Del en stor master uten tap ved nøkkelbilder: `ffmpeg -i master.mp4 -map 0 -c copy -f segment -segment_times 25,42 -reset_timestamps 1 del%d.mp4`. Velg kuttene etter størrelse (`ffprobe -show_entries packet=pts_time,size master.mp4`), og kontroller at delene satt sammen gir samme `-f md5`.
- Siden publiseres som artefakt når verktøyet finnes. Fjern da `<!doctype>`, `<html>`, `<head>` og `<body>` og behold `<title>`, `<style>` og innholdet. Lyd og video legges ved som filer og lastes med relativ sti, for eksempel `new Audio('lydbed.mp3')`.
- Kortversjoner (30 og 60 s): kutt hele deler i stedet for å komprimere tiden, behold låste setninger ordrett, og behold modellen og signaturen.
- Skriv et kort prosjektnotat når filmen hører til et prosjekt: beslutninger, tidskart, måling, filer og åpne punkter.
- Avslutt med et kort svar: hva filmen gjør, rytmetallene mot referansene, forbeholdene (lyd ikke hørt, stemme ikke spilt inn, eksempeltall, avvik fra låste tider) og ett naturlig neste steg.

## Måltall

Mål alle filmer med samme metode, ellers kan tallene ikke sammenlignes: snitt absolutt bildeforskjell mellom bilder, gråtone 0–255, på 192 × 108. Stillstand er bilder med forskjell under 0,05. Blekk er piksler mer enn 18 gråtoner fra bildets median.

| Film | Snitt | Median | 90-persentil | Høyeste sekund | Stillstand | Blekk, median |
|---|---|---|---|---|---|---|
| Apple «Intention» | 0,71 | 0,23 | 2,08 | 5,3 | 30 % | 0,44 % |
| Trådfilmen | 0,28 | 0,15 | 0,75 | 1,53 | 11 % | 7,5 % |
| Blanding (tråd + Intention-rytme) | 0,24 | 0,11 | 0,60 | 1,27 | 38 % | 9,9 % |

Mål for en ny film:

- Median 0,05–0,2 (grunnroen), 90-persentil minst 0,6 og helst 1,0–1,8 (gestene), 20–50 % stillstand.
- Kurven per sekund følger historien: mest i problemet, roligere fase for fase, en tydelig topp på hver gest.
- Hver del med trinn har 90-persentil over 0,5. Trådfilmen hadde 0,23–0,28 i trinnene. Blandingen fikk 0,65 og 0,79.
- Et verdensskifte gir en topp på 5–8 i ett sekund. Det er meningen; Intention har 5,3.

Tynne linjer gir lavere tall enn fylte flater. Er 90-persentilen for lav: gjør gestene kortere (0,5–0,8 s), gi gesten et kameraskyv på 8–20 %, la en fylt form vokse på gesten, og hold lenger før. Er stillstanden for lav: stopp bakgrunnsbevegelse (drift, perler, rotasjon) mellom gestene. Ikke fjern materiale for å få bedre tall.

## Koden

Skriv filene under ut i arbeidsmappen. Verktøyene krever Python 3 med numpy, Pillow og Playwright (`pip install playwright && playwright install chromium` om det mangler), ffmpeg og npm (for fonten).

```
python3 font.py film.html mona-sans 300,400,600     # bygg inn fonten (avsenderens font om den finnes på Fontsource)
python3 eksport.py film.html --ark ark.png          # kontaktark til selvkontroll
python3 eksport.py film.html film.mp4               # video, eventuelt i deler: film.mp4 0 60
```

Har avsenderen en egen font som ikke finnes på Fontsource, delsett den med `pyftsubset Font-Light.ttf --unicodes="U+0020-007E,U+00A0-017F,U+2013-2014,U+2018-201D,U+2022,U+2026,U+2212" --flavor=woff2 --output-file=film-300.woff2` (krever `pip install brotli`), base64-kod filen og legg en `@font-face`-regel med familienavnet "Film" mellom `/*FONTER*/` og `/*SLUTT*/`.

### film.html

Demoen er en 36 sekunders film i stilvalget Punktet. Den viser rutenett med andel, verdensskifte, Intention-linjen, stillhet, linje med aksentspiss, søyler, tall som teller, ring med fire trinn og signatur.

```html
<!doctype html>
<html lang="nb">
<head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<title>Én idé</title>
<style>
/*FONTER*/
:root{--bg:#FAFAFB;--fg:#1F2430;--muted:#5F6573;--line:#E2E4EA;--band:#F1F2F5;--accent:#E8590C;--accent-text:#B24409;
  --sans:"Film","Helvetica Neue",Arial,system-ui,sans-serif}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#121419;--fg:#ECEDF1;--muted:#A3A8B4;--line:#2C303A;--band:#1A1D24;--accent:#F27B3A;--accent-text:#F6A06F;color-scheme:dark}}
:root[data-theme="dark"]{--bg:#121419;--fg:#ECEDF1;--muted:#A3A8B4;--line:#2C303A;--band:#1A1D24;--accent:#F27B3A;--accent-text:#F6A06F;color-scheme:dark}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--fg);font:15px/1.5 var(--sans)}
.wrap{max-width:1120px;margin:0 auto;padding-inline:clamp(16px,4vw,40px);padding-block:32px 64px;display:grid;gap:18px}
h1{font-weight:300;font-size:clamp(2rem,4.5vw,3.4rem);line-height:1.05;margin:0}
.lede{color:var(--muted);max-width:62ch;margin:0}
.stage{aspect-ratio:16/9;max-width:100%;border:1px solid var(--line);border-radius:6px;overflow:hidden;background:#fff}
.stage canvas{display:block;width:100%;height:100%}
.bar{display:flex;align-items:center;gap:10px;flex-wrap:wrap}
button{font:inherit;color:inherit;background:none;border:1px solid var(--line);border-radius:4px;padding:5px 10px;cursor:pointer}
button[aria-pressed="true"],button[aria-current="true"]{border-color:var(--accent);color:var(--accent-text)}
button:focus-visible,#track:focus-visible{outline:2px solid var(--accent);outline-offset:2px}
#tc{font-variant-numeric:tabular-nums;min-width:5.5em;color:var(--muted)}
#track{flex:1 1 320px;height:56px;min-width:0;cursor:pointer;touch-action:none}
.chap{list-style:none;margin:0;padding:0;display:flex;flex-wrap:wrap;gap:8px}
.cues{border-top:1px solid var(--line)}
.cue{display:grid;grid-template-columns:5rem 9rem minmax(0,1fr) minmax(0,14rem);gap:4px 18px;padding:10px 0;border-bottom:1px solid var(--line)}
.cue button{border:0;padding:0;color:var(--accent-text);font-variant-numeric:tabular-nums;text-align:left}
.cue.on{background:linear-gradient(90deg,var(--band),transparent 80%)}
.cue p{margin:0;min-width:0}.cue .scr{color:var(--muted);font-size:.9em}
@media (max-width:720px){.cue{grid-template-columns:4.2rem minmax(0,1fr)}.cue>*:nth-child(n+3){grid-column:2}}
.keys{color:var(--muted);font-size:.85em;margin:0}
</style>
</head>
<body>
<main class="wrap">
  <header><h1>Én idé</h1><p class="lede">Eksempelfilm laget med motoren. Mellomrom spiller, piltaster flytter, G går til neste gest, R viser regilinjer.</p></header>
  <div class="stage"><canvas id="film" width="1920" height="1080" role="img" aria-label="Filmen"></canvas></div>
  <div class="bar">
    <button id="play" type="button">Spill</button><span id="tc">0:00,0</span>
    <canvas id="track" tabindex="0" role="slider" aria-label="Tidslinje" aria-valuemin="0"></canvas>
    <button id="rate" type="button" aria-pressed="false">½ fart</button><button id="guide" type="button" aria-pressed="false">Regi</button>
  </div>
  <ol class="chap" id="chapters"></ol>
  <section class="cues" id="cues" aria-label="Manus"></section>
  <p class="keys">Mellomrom spill/pause · ← → 1 s (Shift 5 s) · , . ett bilde · 1–9 kapittel · G neste gest · R regilinjer</p>
</main>
<script>
"use strict";
// ═════ 1. TIDSKART: det eneste stedet tider står ═════
const W = 1920, H = 1080, FPS = 30, DUR = 36;
const CHAPTERS = [[0, 'Anslag'], [5, 'Mengden'], [11, 'Uro'], [16, 'Retning'], [20, 'Resultat'], [25, 'Modell'], [32.6, 'Signatur']];
const SCRIPT = [ // [VO-inngang, del, voice-over, skjermtekst]
  [0.8, 'Anslag', 'Alt starter med én idé.', 'Det starter med én idé.'],
  [5.2, 'Mengden', 'Så kommer det mange. De fleste blir aldri noe av.', 'Så blir det mange. · 7 av 10 (eksempeltall)'],
  [11.2, 'Uro', 'Uten retning sprer kreftene seg. Til slutt står alt stille.', 'Uten retning spres kreftene.'],
  [16.0, 'Retning', 'Det som hjelper, er en retning.', 'En retning.'],
  [20.0, 'Resultat', 'Da går det fortere fra idé til første test.', '3 uker (eksempeltall)'],
  [25.0, 'Modell', 'Og det stopper ikke der: utforsk, test, bygg, lær, og begynn på nytt.', 'Modell. · 01–04'],
  [32.6, 'Signatur', 'Gode idéer trenger en form.', 'Gode idéer trenger en form.']];
const SILENCE = [[14.6, 15.6]];                    // bilde og lyd står helt stille
const MOTION = null;                                // bevegelse per sekund fra rytme.py (tegnes i tidslinjen)
const AUDIO = null;                                 // f.eks. new Audio('lydbed.mp3'): da er lyden klokken

// ═════ 2. PROFIL: farger som roller, én palett per verden ═════
const PAL = {
  lys:  { flate: '#FFFFFF', dis: '#F2F3F6', blekk: '#1F2430', blekk2: '#2B3140', materiale: '#4A5468', myk: '#B8BFCC', aksent: '#E8590C', varm: '#A0674B', node: '#3D4250', kilde: '#6F7480' },
  mork: { flate: '#15171C', dis: '#1E2128', blekk: '#ECEDF1', blekk2: '#D5D8DF', materiale: '#AEB6C6', myk: '#4C5363', aksent: '#F27B3A', varm: '#D29A7E', node: '#C9CED8', kilde: '#8D93A0' } };
const WORLDS = [[0, 'lys'], [11.0, 'mork', 1284, 484], [16.6, 'lys', 1420, 560]]; // [tid, 'lys'|'mork', x, y]: en skive vokser fra (x, y) på skjermen
const FF = getComputedStyle(document.documentElement).getPropertyValue('--sans').trim();
const F = (w, px) => `${w} ${px}px ${FF}`;

// ═════ 3. VERKTØY ═════
const TAU = Math.PI * 2, K = 64, N = 100, DOT = 7, BEADS = false; // K punkter per element, N elementer, prikkradius, perler på trådene
const clamp = (x, a = 0, b = 1) => Math.max(a, Math.min(b, x)), lerp = (a, b, t) => a + (b - a) * t;
const eio = t => t < .5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2, eo = t => 1 - Math.pow(1 - t, 3), ei = t => t * t * t, lin = t => t;
const pr = (t, a, b, e = eio) => e(clamp((t - a) / (b - a)));            // 0→1 mellom a og b
const env = (t, a, b, f = .4) => pr(t, a, a + f) * (1 - pr(t, b - f, b));       // inn, hold, ut
const hash = (i, s = 0) => { const x = Math.sin(i * 127.1 + s * 311.7) * 43758.5453; return x - Math.floor(x); };
const rgba = (h, a) => { const n = parseInt(h.slice(1), 16); return `rgba(${n >> 16 & 255},${n >> 8 & 255},${n & 255},${clamp(a)})`; };
const mixHex = (a, b, t) => { const p = parseInt(a.slice(1), 16), q = parseInt(b.slice(1), 16);
  return '#' + [16, 8, 0].map(s => Math.round(lerp(p >> s & 255, q >> s & 255, t)).toString(16).padStart(2, '0')).join(''); };
function cub(p0, p1, p2, p3, n, out = []) { for (let k = 0; k < n; k++) { const t = k / (n - 1), u = 1 - t;
  out.push([u*u*u*p0[0] + 3*u*u*t*p1[0] + 3*u*t*t*p2[0] + t*t*t*p3[0], u*u*u*p0[1] + 3*u*u*t*p1[1] + 3*u*t*t*p2[1] + t*t*t*p3[1]]); } return out; }
function resample(pts, n = K) { const L = [0];
  for (let i = 1; i < pts.length; i++) L.push(L[i - 1] + Math.hypot(pts[i][0] - pts[i - 1][0], pts[i][1] - pts[i - 1][1]));
  const tot = L[L.length - 1] || 1, out = []; let j = 0;
  for (let k = 0; k < n; k++) { const d = k / (n - 1) * tot; while (j < L.length - 2 && L[j + 1] < d) j++;
    const u = clamp((d - L[j]) / ((L[j + 1] - L[j]) || 1)); out.push([lerp(pts[j][0], pts[j + 1][0], u), lerp(pts[j][1], pts[j + 1][1], u)]); }
  return out; }
const mix = (A, B, t) => t <= 0 ? A : t >= 1 ? B : A.map((p, i) => [lerp(p[0], B[i][0], t), lerp(p[1], B[i][1], t)]);
const silent = t => SILENCE.reduce((m, [a, b]) => m * (1 - pr(t, a - .25, a, eo) * (1 - pr(t, b, b + .6))), 1);
function clock(speed, hz = 240) { const a = [0]; let s = 0;   // fart integrert én gang: samme t gir alltid samme bilde
  for (let i = 1; i <= (DUR + 1) * hz; i++) { s += speed(i / hz) * silent(i / hz) / hz; a.push(s); }
  return t => a[Math.max(0, Math.min(a.length - 1, Math.round(t * hz)))]; }
const flow = clock(() => .06);                      // perlenes vandring langs trådene

// ═════ 4. FORMBIBLIOTEK: hver funksjon gir K punkter (alle like = en prikk) ═════
const P = {
  dot: ([x, y]) => Array.from({ length: K }, () => [x, y]),
  line: (x0, y0, x1, y1) => Array.from({ length: K }, (_, k) => [lerp(x0, x1, k / (K - 1)), lerp(y0, y1, k / (K - 1))]),
  arc: (cx, cy, rx, ry, a0, a1) => Array.from({ length: K }, (_, k) => { const a = lerp(a0, a1, k / (K - 1)); return [cx + Math.cos(a) * rx, cy + Math.sin(a) * ry]; }),
  stem: (x, base, [ex, ey]) => resample(cub([x, base], [x, base - 150], [lerp(ex, x, .25), ey + 140], [ex, ey], K)), // tråd fra grunnlinjen
  svg: (d, x = 0, y = 0, s = 1) => { const ns = 'http://www.w3.org/2000/svg', sv = document.createElementNS(ns, 'svg'), p = document.createElementNS(ns, 'path');
    p.setAttribute('d', d); sv.append(p); sv.style.cssText = 'position:absolute;width:0;height:0'; document.body.append(sv);
    const L = p.getTotalLength(), out = Array.from({ length: K }, (_, k) => { const q = p.getPointAtLength(k / (K - 1) * L); return [x + q.x * s, y + q.y * s]; }); sv.remove(); return out; } };
const LAY = { // plasseringer: hvor element i står i en formasjon
  grid: (i, cols, x, y, dx, dy) => [x + (i % cols) * dx, y + Math.floor(i / cols) * dy],
  row: (i, n, x0, x1, y) => [lerp(x0, x1, n > 1 ? i / (n - 1) : .5), y],
  ring: (i, n, cx, cy, rx, ry, a0 = -Math.PI / 2) => { const a = a0 + i / n * TAU; return [cx + Math.cos(a) * rx, cy + Math.sin(a) * ry]; },
  bars: (i, counts, x0, base, colW, gap, cols, dy) => { let c = 0, j = i; while (c < counts.length - 1 && j >= counts[c]) { j -= counts[c]; c++; }
    return [x0 + c * (cols * colW + gap) + (j % cols) * colW, base - Math.floor(j / cols) * dy]; },
  scatter: (e, s, cx, cy, rx, ry) => [cx + Math.sin(e.h1 * 9 + s * (.6 + e.h2)) * rx * (.3 + .7 * e.h3), cy + Math.cos(e.h2 * 9 + s * (.5 + e.h1)) * ry * (.3 + .7 * e.h1)] };

// ═════ 5. MATERIALET OG KOREOGRAFIEN ═════
const EL = Array.from({ length: N }, (_, i) => ({ i, x: i / (N - 1), h1: hash(i, 1), h2: hash(i, 2), h3: hash(i, 3) }));
[...EL].sort((a, b) => a.h1 - b.h1).forEach((e, r) => e.rk = r / (N - 1));        // tilfeldig, men fast rekkefølge
EL.forEach(e => e.r = Math.abs(e.i - (N - 1) / 2) / ((N - 1) / 2));                 // 0 i midten, 1 ytterst
const S = { all: () => true, share: p => e => e.rk < p, ids: (...ids) => e => ids.includes(e.i), first: n => e => e.i < n, not: f => e => !f(e) };
const drift = clock(t => .35 + .9 * pr(t, 11, 13.5) - 1.1 * pr(t, 15.6, 17));
const FORMS = { // navn → (element, t) → K punkter
  en:       () => P.dot([960, 470]),
  rutenett: e => P.dot(LAY.grid(e.i, 10, 1050, 250, 52, 52)),
  uro:      (e, t) => P.dot(LAY.scatter(e, drift(t), 1280, 520, 420, 300)),
  strek:    e => P.dot(LAY.row(e.i, N, 360, 1560, 560)),
  soyler:   e => P.dot(LAY.bars(e.i, [80, 20], 1060, 860, 26, 140, 8, 26)),
  ring:     e => P.dot(LAY.ring(e.i, N, 1240, 500, 300, 300)) };
const FORM0 = 'en';
const MORPHS = [ // [start, varighet, til form, rekkefølge: index | x | center | random | none]
  [5.2, 2.0, 'rutenett', 'index'], [11.0, 1.6, 'uro', 'random'], [16.0, 1.4, 'strek', 'x'], [20.0, 1.4, 'soyler', 'index'], [25.0, 1.4, 'ring', 'index']];
const REVEAL = [];                                  // [start, varighet, velger, rekkefølge]: pennen tegner elementet (skjult før)
const STATES = [ // [start, varighet, velger, stil: normal | aksent | hul | varm | dempet | borte]
  [8.0, .8, S.share(.7), 'hul'], [11.0, .8, S.all, 'normal'], [16.6, .5, S.ids(N - 1), 'aksent'], [20.0, .6, S.all, 'normal'],
  [21.0, .6, e => e.i >= 80, 'aksent'], [25.0, .8, S.all, 'varm']];
const STYLE = { normal: c => [c.materiale, 1, .9], aksent: c => [c.aksent, 1, 1], hul: c => [c.materiale, 0, .75], varm: c => [c.varm, 1, .95], dempet: c => [c.materiale, 1, .2], borte: c => [c.materiale, 1, 0] };

// ═════ 6. GESTER: én bokstavelig handling på ordet. Brukes også i tidslinjen, regilinjene og lyden ═════
const GESTURES = [ // [tid, type, etikett, parametre]
  [1.2, 'pop', 'Idéen settes', { x: 960, y: 470 }], [5.2, 'merke', 'Én blir mange'], [8.0, 'merke', '7 av 10 blir hule'],
  [11.0, 'merke', 'Verden blir mørk, rutenettet løses opp'], [14.6, 'merke', 'Alt står stille'], [16.0, 'strek', 'Punktene legger seg på én linje', { x0: 360, y0: 560, x1: 1560, y1: 560 }],
  [21.0, 'merke', 'Den lille søylen lyser'], [22.0, 'hake', 'Testet', { x: 1640, y: 640, r: 34 }], [25.0, 'merke', 'Søylene blir en sirkel'],
  [26.6, 'node', '01 Utforsk', { cx: 1240, cy: 500, r: 380, k: 0, n: 4 }], [27.8, 'node', '02 Test', { cx: 1240, cy: 500, r: 380, k: 1, n: 4 }],
  [29.0, 'node', '03 Bygg', { cx: 1240, cy: 500, r: 380, k: 2, n: 4 }], [30.2, 'node', '04 Lær', { cx: 1240, cy: 500, r: 380, k: 3, n: 4 }],
  [31.2, 'signal', 'Runden begynner på nytt', { cx: 1240, cy: 500, r: 380, a0: -Math.PI / 2 + 1.5 * TAU / 4 * 2, a1: -Math.PI / 2 + TAU, dur: 1.2 }]];
const G = { // hver gest tegnes i verdenskoordinater (følger kameraet). c = palett, k = tid siden start
  merke: () => {},
  pop: (k, p, c) => { const a = pr(k, 0, .6, eo); circ(p.x, p.y, 10 + 50 * a, rgba(c.aksent, 1 - a), 2); },
  ring: (k, p, c) => circ(p.x, p.y, p.r, rgba(c.blekk, .55), 1.4, -Math.PI / 2, -Math.PI / 2 + TAU * pr(k, 0, .6)),
  strek: (k, p, c) => { const a = pr(k, 0, .7); line(p.x0, p.y0, lerp(p.x0, p.x1, a), lerp(p.y0, p.y1, a), rgba(c.aksent, .9 * (1 - pr(k, 2.6, 3.2))), 2.4); },
  skann: (k, p, c) => { const x = lerp(p.x0, p.x1, pr(k, 0, p.dur || 3, lin)), gr = g.createLinearGradient(x - 60, 0, x + 60, 0);
    gr.addColorStop(0, rgba(c.aksent, 0)); gr.addColorStop(.5, rgba(c.aksent, .18 * (1 - pr(k, (p.dur || 3), (p.dur || 3) + .4)))); gr.addColorStop(1, rgba(c.aksent, 0)); g.fillStyle = gr; g.fillRect(x - 60, p.y0, 120, p.y1 - p.y0); },
  hake: (k, p, c) => { circ(p.x, p.y, p.r || 34, rgba(c.blekk, .6), 1.4, -Math.PI / 2, -Math.PI / 2 + TAU * pr(k, 0, .4)); const a = pr(k, .35, .75), r = p.r || 34;
    if (a > 0) { g.beginPath(); g.moveTo(p.x - r * .4, p.y); g.lineTo(p.x - r * .1, p.y + r * .3); g.lineTo(lerp(p.x - r * .1, p.x + r * .45, a), lerp(p.y + r * .3, p.y - r * .35, a)); g.strokeStyle = c.aksent; g.lineWidth = 2.4; g.stroke(); } },
  bolge: (k, p, c) => { const a = pr(k, 0, 1.4, eo); circ(p.x, p.y, p.r * a, rgba(c.aksent, .7 * (1 - a)), 2); },
  kobling: (k, p, c) => EL.filter(p.sel).forEach((e, j) => { const q = pose(e, T0 + k)[0], a = pr(k, j * .06, j * .06 + .5) * (1 - pr(k, 2.4, 3)); line(q[0], q[1], lerp(q[0], p.x, a), lerp(q[1], p.y, a), rgba(c.aksent, .55 * a), 1.2); }),
  node: (k, p, c) => { for (let j = 0; j <= p.k; j++) { const a = -Math.PI / 2 + j / p.n * TAU; dot(p.cx + Math.cos(a) * p.r, p.cy + Math.sin(a) * p.r, j === p.k ? 9 * pr(k, 0, .35, eo) : 9, j === p.k ? c.aksent : c.node); }
    circ(p.cx, p.cy, p.r, rgba(c.myk, .8), 1.2, -Math.PI / 2, -Math.PI / 2 + (p.k + pr(k, 0, 1)) / p.n * TAU); },
  signal: (k, p, c) => { const s = pr(k, 0, p.dur || 1.5), a = lerp(p.a0, p.a1, s); if (s >= 1) return;
    circ(p.cx, p.cy, p.r, rgba(c.aksent, .9), 2.4, a - .45, a); dot(p.cx + Math.cos(a) * p.r, p.cy + Math.sin(a) * p.r, 8, c.aksent); },
  stopp: (k, p, c) => { const a = pr(k, 0, .25, eo); g.fillStyle = c.aksent; g.fillRect(p.x - 14 * a, p.y - 14 * a, 28 * a, 28 * a); circ(p.x, p.y, 40, c.aksent, 2, -Math.PI / 2, -Math.PI / 2 + TAU * pr(k, .2, .6)); },
  port: (k, p, c) => { const s = pr(k, 0, .7), col = p.aksent ? c.aksent : c.materiale, ry = p.ry || p.r * .3, [b0, b1] = s >= 1 ? [0, TAU] : [-Math.PI / 2, -Math.PI / 2 + TAU * s]; // vevd port
    for (let i = 0; i < 30; i++) { const f = i / 29, r = p.r * (.75 + .5 * f); g.beginPath(); g.ellipse(p.x, p.y, r, ry * (.75 + .5 * f), 0, b0, b1);
      g.strokeStyle = rgba(col, (.15 + .35 * Math.sin(f * Math.PI)) * (1 - pr(k, p.lukk ?? 99, (p.lukk ?? 99) + .5))); g.lineWidth = 1; g.stroke(); } dot(p.x, p.y, 6 * s, col); } };
const LIFE = { pop: 1, strek: 3.3, skann: 4, hake: 99, bolge: 1.5, kobling: 3.1, node: 99, signal: 2, stopp: 99, ring: 99, port: 99, merke: 0 }; // sekunder synlig

// ═════ 7. TEKST: [inn, ut, stil, tekst, valg]. Stiler: midt | kolonne | fase | trinn | tall | linje | signatur | avsender ═════
const TEXT = [
  [1.0, 4.6, 'midt', 'Det starter med én idé.'],
  [5.4, 10.6, 'kolonne', 'Så blir det mange.'], [7.6, 10.6, 'tall', '{} av 10|Eksempeltall', { fra: 0, til: 7 }],
  [11.6, 14.4, 'linje', 'Uten retning spres kreftene.', { nokkel: 1, ved: 13.2 }], [16.8, 19.6, 'midt', 'En retning.'],
  [20.6, 24.6, 'kolonne', 'Fra idé til test:'], [21.0, 24.6, 'tall', '{} uker|Eksempeltall', { fra: 12, til: 3 }],
  [25.4, 31.8, 'fase', 'Modell.'], [26.6, 27.7, 'trinn', '01|Utforsk'], [27.8, 28.9, 'trinn', '02|Test'], [29.0, 30.1, 'trinn', '03|Bygg'], [30.2, 31.8, 'trinn', '04|Lær'],
  [32.8, 99, 'signatur', 'Gode idéer trenger en form.'], [33.6, 99, 'avsender', 'AVSENDER']];

// ═════ 8. KAMERA: [start, varighet, skala, fokus i verden, fokusets plass på skjermen] ═════
const CAM = [[5.2, 2, 1, [1280, 500], [1280, 500]], [11, 3.4, 1.06, [1280, 520], [1280, 520]], [16, 1.2, 1, [960, 560], [960, 560]],
  [16.4, .5, 1.1, [1560, 560], [1420, 560]], [18.4, .9, 1, [960, 560], [960, 560]], [20, 1.2, 1, [1280, 600], [1300, 600]], [21, .5, 1.08, [1300, 700], [1320, 680]],
  [22.4, .8, 1, [1300, 600], [1300, 600]], [25, 1.4, 1, [1240, 500], [1240, 500]], [31.2, .6, 1.05, [1240, 500], [1240, 500]], [32.6, 1.8, .82, [1240, 500], [960, 430]]];
function camera(t) { let s = 1, f = [960, 540], p = [960, 540];
  for (const [t0, d, sc, F0, S0] of CAM) { if (t < t0) break; const k = pr(t, t0, t0 + d);
    s = lerp(s, sc, k); f = [lerp(f[0], F0[0], k), lerp(f[1], F0[1], k)]; p = [lerp(p[0], S0[0], k), lerp(p[1], S0[1], k)]; } return { s, f, p }; }

// ═════ 9. TEGNING (endres sjelden) ═════
const cv = document.getElementById('film'), g = cv.getContext('2d'); let T0 = 0;
function line(x0, y0, x1, y1, col, w) { g.beginPath(); g.moveTo(x0, y0); g.lineTo(x1, y1); g.strokeStyle = col; g.lineWidth = w; g.lineCap = 'round'; g.stroke(); }
function dot(x, y, r, col) { if (r <= 0) return; g.beginPath(); g.arc(x, y, r, 0, TAU); g.fillStyle = col; g.fill(); }
function circ(x, y, r, col, w, a0 = 0, a1 = TAU) { if (r <= 0 || a1 - a0 < 1e-4) return; if (a1 - a0 >= TAU - 1e-4) { a0 = 0; a1 = TAU; } // hel runde må være 0→TAU (Skia)
  g.beginPath(); g.arc(x, y, r, a0, a1); g.strokeStyle = col; g.lineWidth = w; g.stroke(); }
function text(s, x, y, font, col, align = 'left') { g.font = font; g.fillStyle = col; g.textAlign = align; g.fillText(s, x, y); g.textAlign = 'left'; }
const order = (o, e, A) => o === 'x' ? clamp(A[0][0] / W) : o === 'center' ? e.r : o === 'random' ? e.h2 : o === 'none' ? 0 : e.x;
function pose(e, t) { let cur = FORM0;
  for (const [t0, d, to, o] of MORPHS) { if (t < t0) break;
    if (t < t0 + d) { const A = FORMS[cur](e, t), ed = Math.min(1.2, d * .55), s = t0 + order(o, e, A) * (d - ed); return mix(A, FORMS[to](e, t), pr(t, s, s + ed)); } cur = to; }
  return FORMS[cur](e, t); }
function penOf(e, t) { let p = 1; for (const [t0, d, sel, o] of REVEAL) if (sel(e)) { const ed = Math.min(1.2, d * .55), s = t0 + order(o, e, [[0, 0]]) * (d - ed); p = pr(t, s, s + ed, eo); } return p; }
function styleOf(e, t, c) { let [col, fill, a] = STYLE.normal(c);
  for (const [t0, d, sel, st] of STATES) { if (t < t0) break; if (!sel(e)) continue; const k = pr(t, t0, t0 + d), [c2, f2, a2] = STYLE[st](c);
    col = mixHex(col, c2, k); fill = lerp(fill, f2, k); a = lerp(a, a2, k); } return [col, fill, a]; }
function drawEl(e, t, c) { const pen = penOf(e, t); if (pen <= 0) return; const pts = pose(e, t), [col, fill, a] = styleOf(e, t, c); if (a <= 0) return;
  if (e.base) dot(e.base[0], e.base[1], 3, rgba(c.node, a));                       // punktet på grunnlinjen
  const n = Math.max(1, Math.round(pen * K)), span = Math.abs(pts[K - 1][0] - pts[0][0]) + Math.abs(pts[K - 1][1] - pts[0][1]) + Math.abs(pts[K >> 1][0] - pts[0][0]) + Math.abs(pts[K >> 1][1] - pts[0][1]);
  if (span < 1) { const [x, y] = pts[0]; if (fill > 0) dot(x, y, DOT, rgba(col, a * fill)); if (fill < 1) circ(x, y, DOT - .8, rgba(col, a * (1 - fill)), 1.6); return; }
  g.beginPath(); g.moveTo(pts[0][0], pts[0][1]); for (let k = 1; k < n; k++) g.lineTo(pts[k][0], pts[k][1]);
  g.strokeStyle = rgba(col, a); g.lineWidth = 1.6; g.lineCap = g.lineJoin = 'round'; g.stroke(); if (n < K) dot(pts[n - 1][0], pts[n - 1][1], 3.5, rgba(col, a)); // pennespiss
  if (BEADS && n > 8) { const q = pts[Math.floor(((flow(t) * (1 + e.h3) + e.h1) % 1) * (n - 1))]; dot(q[0], q[1], 2.6, rgba(col, a)); } }
function words(s, x, y, font, col, t, tin, tout, align = 'left', per = .11) { // ord for ord i lesetakt, ut samlet
  if (t < tin || t > tout + .5) return; g.font = font; const ws = s.split(' '), sp = g.measureText(' ').width, wd = ws.map(w => g.measureText(w).width);
  let cx = align === 'center' ? x - (wd.reduce((a, b) => a + b, 0) + sp * (ws.length - 1)) / 2 : x; const out = 1 - pr(t, tout, tout + .5);
  ws.forEach((w, i) => { const a = pr(t, tin + i * per, tin + i * per + .45) * out; if (a > 0) { g.globalAlpha = a; text(w, cx, y + (1 - a) * 8 * (out < 1 ? 0 : 1), font, col); g.globalAlpha = 1; } cx += wd[i] + sp; }); }
function drawText(t, c) { const L = 200;
  for (const [tin, tout, st, s, o = {}] of TEXT) { if (t < tin || t > tout + .6) continue; const out = 1 - pr(t, tout, tout + .5), a = pr(t, tin, tin + .45) * out;
    if (st === 'midt') words(s, 960, o.y || 300, F(300, 84), c.blekk, t, tin, tout, 'center');
    else if (st === 'kolonne') words(s, L, o.y || 243, F(300, 84), c.blekk, t, tin, tout);
    else if (st === 'fase') words(s, L, 243, F(300, 92), c.blekk, t, tin, tout);
    else if (st === 'signatur') words(s, 960, o.y || 905, F(300, 64), c.blekk, t, tin, tout, 'center', .09);
    else if (st === 'avsender') { g.globalAlpha = a; text(s, 960, o.y || 985, F(600, 28), c.blekk, 'center'); g.globalAlpha = 1; } // eller drawImage(logo)
    else if (st === 'trinn') { const [num, name] = s.split('|'); g.globalAlpha = a; text(num, L, 527 + (1 - a) * 14, F(300, 182), c.materiale); text(name, L + 2, 622, F(400, 46), c.blekk2);
      g.globalAlpha = 1; g.fillStyle = rgba(c.aksent, out); g.fillRect(L, 690, 86 * pr(t, tin + .1, tin + .6, eo), 2.5); }
    else if (st === 'tall') { const [tpl, src] = s.split('|'), v = Math.round(lerp(o.fra ?? 0, o.til, pr(t, tin + .1, tin + 1.4, eo)));
      g.globalAlpha = a; text(tpl.replace('{}', v), L - 4, o.y || 520, F(300, 150), c.blekk); if (src) text(src, L + 2, (o.y || 520) + 60, F(400, 24), c.kilde); g.globalAlpha = 1; }
    else if (st === 'linje') { g.font = F(400, 58); const ws = s.split(' '), sp = g.measureText(' ').width, wd = ws.map(w => g.measureText(w).width); // Intention: én liten linje
      let x = 960 - (wd.reduce((p, q) => p + q, 0) + sp * (ws.length - 1)) / 2;
      ws.forEach((w, i) => { const on = pr(t, tin + i * .28, tin + i * .28 + .3), keep = o.nokkel === i ? 1 : 1 - pr(t, o.ved ?? 99, (o.ved ?? 99) + .6);
        g.globalAlpha = (.22 + .78 * on) * keep * out; text(w, x, o.y || 880, F(400, 58), c.blekk); x += wd[i] + sp; }); g.globalAlpha = 1; } } }
function worldAt(t) { let w = WORLDS[0][1], prev = w, k = 1, at = null;
  for (const [t0, to, x = 960, y = 540] of WORLDS.slice(1)) { if (t < t0) break; prev = w; w = to; k = pr(t, t0, t0 + .5, ei); at = [x, y]; }
  return { w, prev, k, at }; }
function renderFrame(t) { const wo = worldAt(t), c = PAL[wo.k > .5 ? wo.w : wo.prev]; T0 = 0;
  g.setTransform(1, 0, 0, 1, 0, 0); g.fillStyle = PAL[wo.prev].flate; g.fillRect(0, 0, W, H);
  if (wo.at && wo.k > 0) { g.beginPath(); g.arc(wo.at[0], wo.at[1], wo.k * 2300, 0, TAU); g.fillStyle = PAL[wo.w].flate; g.fill(); } // verdensskifte: skiven vokser
  const cam = camera(t), rg = g.createRadialGradient(cam.p[0], cam.p[1], 80, cam.p[0], cam.p[1], 640);
  rg.addColorStop(0, rgba(c.dis, .95)); rg.addColorStop(1, rgba(c.dis, 0)); g.fillStyle = rg; g.fillRect(0, 0, W, H);
  g.setTransform(cam.s, 0, 0, cam.s, cam.p[0] - cam.f[0] * cam.s, cam.p[1] - cam.f[1] * cam.s);
  for (const e of EL) drawEl(e, t, c);
  for (const [t0, type, , p = {}] of GESTURES) if (t >= t0 && t < t0 + (p.life ?? LIFE[type] ?? 2)) { T0 = t0; G[type](t - t0, p, c); }
  g.setTransform(1, 0, 0, 1, 0, 0); drawText(t, c); }

// ═════ 10. SPILLER, TIDSLINJE OG REGILINJER (endres sjelden) ═════
let T = 0, playing = false, rate = 1, guide = false, last = 0, dirty = true;
const $ = id => document.getElementById(id), tr = $('track'), tg = tr.getContext('2d');
const tc = t => `${Math.floor(t / 60)}:${(t % 60).toFixed(1).padStart(4, '0').replace('.', ',')}`;
const css = n => getComputedStyle(document.documentElement).getPropertyValue(n).trim();
function seek(t) { T = clamp(t, 0, DUR); if (AUDIO) AUDIO.currentTime = T; dirty = true; }
function setPlaying(on) { if (on && T >= DUR) seek(0); playing = on; if (AUDIO) on ? AUDIO.play().catch(() => {}) : AUDIO.pause(); dirty = true; }
function drawGuide(t) { g.setTransform(1, 0, 0, 1, 0, 0); g.strokeStyle = 'rgba(232,89,12,.6)'; g.lineWidth = 1; g.setLineDash([6, 6]);
  g.strokeRect(96, 54, W - 192, H - 108); g.beginPath(); g.moveTo(200, 0); g.lineTo(200, H); g.moveTo(W / 2, 0); g.lineTo(W / 2, H); g.stroke(); g.setLineDash([]);
  const cue = [...SCRIPT].reverse().find(q => t >= q[0] - .05), nx = GESTURES.find(x => x[0] > t);
  text(`${tc(t)}  ${cue ? cue[1] + ': ' + cue[2] : ''}`, 110, H - 70, F(400, 22), '#6F7480');
  if (nx) text(`Neste gest om ${(nx[0] - t).toFixed(1).replace('.', ',')} s: ${nx[2]}`, 110, H - 40, F(400, 22), '#B24409'); }
function drawTrack() { const d = devicePixelRatio || 1, w = tr.clientWidth, h = tr.clientHeight; if (!w) return;
  tr.width = w * d; tr.height = h * d; tg.setTransform(d, 0, 0, d, 0, 0); const X = t => t / DUR * w;
  CHAPTERS.forEach(([a], i) => { const b = (CHAPTERS[i + 1] || [DUR])[0]; if (i % 2 === 0) { tg.fillStyle = css('--band'); tg.fillRect(X(a), 0, X(b) - X(a), h); } });
  tg.fillStyle = css('--line'); SILENCE.forEach(([a, b]) => tg.fillRect(X(a), h - 8, X(b) - X(a), 8));
  if (MOTION) { tg.beginPath(); MOTION.forEach((v, i) => tg.lineTo(X(i + .5), h - 10 - Math.min(v, 1.6) / 1.6 * (h - 22))); tg.strokeStyle = css('--muted'); tg.lineWidth = 1.2; tg.stroke(); }
  tg.fillStyle = css('--accent'); GESTURES.forEach(([a]) => tg.fillRect(X(a) - .5, 0, 1.5, 10)); tg.fillRect(X(T) - 1, 0, 2, h);
  tg.font = `11px ${FF}`; tg.fillStyle = css('--muted');
  CHAPTERS.forEach(([a, n], i) => { const b = (CHAPTERS[i + 1] || [DUR])[0]; if (tg.measureText(n).width < X(b) - X(a) - 8) tg.fillText(n, X(a) + 4, 24); });
  tr.setAttribute('aria-valuemax', DUR); tr.setAttribute('aria-valuenow', T.toFixed(1)); tr.setAttribute('aria-valuetext', tc(T)); }
function syncUI() { $('tc').textContent = tc(T); const ci = CHAPTERS.reduce((m, q, i) => T >= q[0] - .05 ? i : m, 0);
  document.querySelectorAll('#chapters button').forEach((b, i) => b.setAttribute('aria-current', i === ci));
  const si = SCRIPT.reduce((m, q, i) => T >= q[0] - .05 ? i : m, -1); document.querySelectorAll('.cue').forEach((r, i) => r.classList.toggle('on', i === si));
  $('play').textContent = playing ? 'Pause' : 'Spill'; drawTrack(); }
function loop(now) { if (playing) { T = AUDIO ? AUDIO.currentTime : T + (now - last) / 1000 * rate; if (T >= DUR) { T = DUR; setPlaying(false); } dirty = true; } last = now;
  if (dirty) { renderFrame(T); if (guide) drawGuide(T); syncUI(); dirty = false; } requestAnimationFrame(loop); }
$('play').onclick = () => setPlaying(!playing);
$('rate').onclick = e => { rate = rate === 1 ? .5 : 1; if (AUDIO) AUDIO.playbackRate = rate; e.currentTarget.setAttribute('aria-pressed', rate < 1); };
$('guide').onclick = e => { guide = !guide; e.currentTarget.setAttribute('aria-pressed', guide); dirty = true; };
let drag = false; const tAt = e => (e.clientX - tr.getBoundingClientRect().left) / tr.clientWidth * DUR;
tr.onpointerdown = e => { drag = true; tr.setPointerCapture(e.pointerId); seek(tAt(e)); }; tr.onpointermove = e => drag && seek(tAt(e)); tr.onpointerup = () => drag = false;
addEventListener('keydown', e => { if (e.target.tagName === 'BUTTON' && e.key === ' ') return; const k = e.key;
  if (k === ' ') setPlaying(!playing); else if (k === 'ArrowRight' || k === 'ArrowLeft') seek(T + (k === 'ArrowRight' ? 1 : -1) * (e.shiftKey ? 5 : 1));
  else if (k === '.' || k === ',') seek(T + (k === '.' ? 1 : -1) / FPS); else if (/^[1-9]$/.test(k) && CHAPTERS[k - 1]) seek(CHAPTERS[k - 1][0]);
  else if (k === 'g' || k === 'G') { const nx = GESTURES.find(x => x[0] > T + .05); if (nx) seek(nx[0] - .4); } else if (k === 'r' || k === 'R') $('guide').click(); else return; e.preventDefault(); });
CHAPTERS.forEach(([t, n], i) => { const li = document.createElement('li'), b = document.createElement('button'); b.type = 'button'; b.textContent = `${i + 1} ${n}`; b.onclick = () => seek(t); li.append(b); $('chapters').append(li); });
SCRIPT.forEach(([t, del, vo, scr]) => { const r = document.createElement('div'); r.className = 'cue'; const b = document.createElement('button'); b.type = 'button'; b.textContent = tc(t); b.onclick = () => seek(t);
  const d = document.createElement('strong'), v = document.createElement('p'), s = document.createElement('p'); d.textContent = del; v.textContent = vo; s.className = 'scr'; s.textContent = scr; r.append(b, d, v, s); $('cues').append(r); });
new ResizeObserver(() => dirty = true).observe(tr);

// ═════ 11. EKSPORT: eksport.py henter bilder og tidskart herfra ═════
window.FILM = { W, H, FPS, DUR, ready: false, frameAt: t => { renderFrame(t); return cv.toDataURL('image/png'); },
  timeline: () => ({ DUR, FPS, CHAPTERS, SILENCE, GESTURES: GESTURES.map(([t, type, label, p = {}]) => [t, type, label, p.dur || 0]),
    TEXT: TEXT.map(([a, b, st]) => [a, b, st]) }) };
document.fonts.ready.then(() => { FILM.ready = true; dirty = true; requestAnimationFrame(loop); });
</script>
</body>
</html>
```

### trad.js (stilvalg B: Tråden)

```js
// Stilvalg B, Tråden: N tråder vokser fra en grunnlinje, floker seg, rettes ut til ett bånd og vikles til en ring av løkker.
// Erstatter alt fra «const drift» til og med STATES i seksjon 5. Sett K = 120, N = 36 og BEADS = true i seksjon 3, og WORLDS = [[0, 'lys']].
const AX = 1240, BASE = 930; EL.forEach(e => { e.bx = AX + (e.i - (N - 1) / 2) * 17; e.base = [e.bx, BASE]; });
const swirl = clock(t => .25 + .9 * pr(t, 7, 10) - .8 * pr(t, 12, 13.5));          // flokens indre fart
function tangle(e, t, cx, cy, R) { const s = swirl(t), dir = e.i % 2 ? 1 : -1, a = Math.PI / 2 + (e.bx - AX) / 420;
  const o = [cx + (e.h1 - .5) * 100 + 16 * Math.sin(.7 * s + e.h3 * 6), cy + (e.h2 - .5) * 70 + 10 * Math.cos(.5 * s + e.h1 * 6)];
  const at = u => { const ph = a + dir * TAU * (1.4 + 1.1 * e.h3) * u + (.6 + .8 * e.h2) * dir * s;     // tre frekvenser: floke, ikke rosett
    const r = R * (.55 + .25 * Math.sin(1.7 * ph + e.h1 * 6 + .8 * s) + .14 * Math.sin(3.3 * ph + e.h2 * 6 - .5 * s) + .06 * Math.sin(7.1 * ph + e.h3 * 6));
    return [o[0] + Math.cos(ph) * r * 1.1, o[1] + Math.sin(ph) * r * .88]; };
  const E = at(0), pts = cub([e.bx, BASE], [e.bx, BASE - 150], [lerp(E[0], e.bx, .25), E[1] + 140], E, 40);   // stammen fra grunnlinjen
  for (let k = 1; k <= 90; k++) pts.push(at(k / 90)); return resample(pts); }
const FORMS = {
  floke: (e, t) => tangle(e, t, 1240, 440, 200),
  baand: (e, t) => { const off = (e.i - (N - 1) / 2) * 3.4, still = pr(t, 12.9, 14.3);                 // bølgende bånd som rettes ut og spisser seg
    return Array.from({ length: K }, (_, k) => { const u = k / (K - 1), tp = 1 - pr(u, .62, 1);
      return [lerp(300, 1500, u), 560 + tp * (off + (1 - still) * 30 * Math.sin(u * 9 + e.h1 * 6 + t * 1.3) * (e.h2 - .5) * 2)]; }); },
  lokker: e => { const li = e.i % 12, rx = 300 - li * 9, ry = 240 - li * 7, a0 = .3 * Math.PI, E = [1240 + Math.cos(a0) * rx, 470 + Math.sin(a0) * ry]; // nestede løkker
    const pts = cub([e.bx, BASE], [e.bx, BASE - 120], [E[0] + 60, E[1] + 90], E, 36);
    for (let k = 1; k <= 110; k++) { const a = a0 - k / 110 * .9 * TAU; pts.push([1240 + Math.cos(a) * rx, 470 + Math.sin(a) * ry]); } return resample(pts); } };
const FORM0 = 'floke';
const MORPHS = [[12.2, 2.0, 'baand', 'x'], [17.6, 1.8, 'lokker', 'center']];
const REVEAL = [[5.2, 2.6, S.all, 'center']];                                          // pennen tegner trådene, innerst først
const STATES = [[16.6, .5, S.ids(N >> 1), 'aksent'], [17.6, .6, S.ids(N >> 1), 'normal'], [18.2, 2.2, S.all, 'varm']];
```

### font.py

```python
# Henter en åpen font fra Fontsource (npm) og bygger den inn i film.html som data-URI, under familienavnet "Film".
#   python3 font.py film.html mona-sans 300,400,600      (navnet er Fontsource-ID-en, f.eks. ibm-plex-sans, hanken-grotesk)
import sys, re, base64, subprocess, tarfile, tempfile, pathlib
html, fid, weights = pathlib.Path(sys.argv[1]), sys.argv[2], sys.argv[3].split(',')
with tempfile.TemporaryDirectory() as d:
    subprocess.run(['npm', 'pack', f'@fontsource/{fid}', '--silent'], cwd=d, check=True, capture_output=True)
    tar = tarfile.open(next(pathlib.Path(d).glob('*.tgz'))); rules = []
    for w in weights:
        css = tar.extractfile(f'package/{w}.css').read().decode()
        for block in re.findall(r'/\*\s*([\w-]+)\s*\*/\s*@font-face\s*{(.*?)}', css, re.S):
            if not re.search(r'-latin(-ext)?-\d+-normal$', block[0]): continue   # latin dekker æ ø å, latin-ext resten av Europa
            woff = re.search(r"url\(\./(files/[^)]+\.woff2)\)", block[1]).group(1)
            data = base64.b64encode(tar.extractfile('package/' + woff).read()).decode()
            rng = re.search(r'unicode-range:\s*([^;]+);', block[1]).group(1)
            rules.append(f'@font-face{{font-family:"Film";font-weight:{w};font-display:block;src:url(data:font/woff2;base64,{data}) format("woff2");unicode-range:{rng}}}')
s = html.read_text(encoding='utf-8')
s = re.sub(r'/\*FONTER\*/.*?/\*SLUTT\*/|/\*FONTER\*/', lambda m: '/*FONTER*/\n' + '\n'.join(rules) + '\n/*SLUTT*/', s, count=1, flags=re.S)
html.write_text(s, encoding='utf-8'); print(f'{len(rules)} skriftsnitt bygget inn i {html}')
```

### eksport.py

```python
# Rendrer film.html bilde for bilde i hodeløs Chromium.
#   python3 eksport.py film.html film.mp4 [fra] [til]        video uten lyd (crf 16)
#   python3 eksport.py film.html --stills 4,13,20 mappe/      stillbilder (styleframes)
#   python3 eksport.py film.html --ark ark.png                kontaktark: hvert kapittel og hver gest, med tidskode
#   python3 eksport.py film.html --tidskart tidskart.json     tidskartet som lydbed.py og rytme.py leser
import sys, io, json, base64, subprocess, pathlib, time
from playwright.sync_api import sync_playwright

src, mode = pathlib.Path(sys.argv[1]).resolve().as_uri() + '#export', sys.argv[2]
with sync_playwright() as p:
    b = p.chromium.launch(); pg = b.new_page(viewport={'width': 1280, 'height': 800}); errors = []
    pg.on('pageerror', lambda e: errors.append(str(e))); pg.on('console', lambda m: m.type == 'error' and errors.append(m.text))
    pg.goto(src); pg.wait_for_function('window.FILM && FILM.ready', timeout=20000)
    TL = pg.evaluate('FILM.timeline()'); FPS, DUR = TL['FPS'], TL['DUR']
    png = lambda t: base64.b64decode(pg.evaluate('t => FILM.frameAt(t)', t).split(',', 1)[1])
    if mode == '--tidskart':
        pathlib.Path(sys.argv[3]).write_text(json.dumps(TL, ensure_ascii=False, indent=1), encoding='utf-8')
    elif mode == '--stills':
        out = pathlib.Path(sys.argv[4]); out.mkdir(parents=True, exist_ok=True)
        for t in map(float, sys.argv[3].split(',')): (out / f'f_{t:06.2f}.png').write_bytes(png(t))
    elif mode == '--ark':
        from PIL import Image, ImageDraw, ImageFont
        font = ImageFont.load_default()
        for name in ('DejaVuSans.ttf', 'Arial.ttf', 'LiberationSans-Regular.ttf', 'Helvetica.ttc'):  # en font med æ ø å
            try: font = ImageFont.truetype(name, 15); break
            except OSError: pass
        marks = sorted({round(c[0] + 1.2, 2) for c in TL['CHAPTERS']} | {round(g[0] + .6, 2) for g in TL['GESTURES']})
        marks = [m for m in marks if m < DUR][:32]; cols, w, h = 4, 480, 270
        sheet = Image.new('RGB', (cols * (w + 6) + 6, ((len(marks) + cols - 1) // cols) * (h + 30) + 6), '#d0d0d0')
        for i, t in enumerate(marks):
            im = Image.open(io.BytesIO(png(t))).convert('RGB').resize((w, h)); x, y = 6 + (i % cols) * (w + 6), 6 + (i // cols) * (h + 30)
            sheet.paste(im, (x, y)); gest = next((g[2] for g in TL['GESTURES'] if abs(g[0] + .6 - t) < .01), '')
            ImageDraw.Draw(sheet).text((x + 2, y + h + 6), f'{int(t // 60)}:{t % 60:04.1f}  {gest}', fill='#222', font=font)
        sheet.save(sys.argv[3])
    else:
        a = float(sys.argv[3]) if len(sys.argv) > 3 else 0.0; z = float(sys.argv[4]) if len(sys.argv) > 4 else DUR
        ff = subprocess.Popen(['ffmpeg', '-v', 'error', '-y', '-f', 'image2pipe', '-c:v', 'png', '-r', str(FPS), '-i', '-', '-c:v', 'libx264',
                               '-preset', 'medium', '-crf', '16', '-pix_fmt', 'yuv420p', '-movflags', '+faststart', mode], stdin=subprocess.PIPE)
        t0 = time.time()
        for n in range(round(a * FPS), round(z * FPS)):
            ff.stdin.write(png(n / FPS))
            if n % (FPS * 10) == 0: print(f'{n / FPS:.0f} av {z:.0f} s ({time.time() - t0:.0f} s brukt)', file=sys.stderr)
        ff.stdin.close(); ff.wait()
    b.close()
    if errors: print('Feil i siden:', *errors[:5], sep='\n  ', file=sys.stderr); sys.exit(1)
```

### rytme.py

```python
# Måler rytmen i en ferdig film: snitt absolutt bildeforskjell (gråtone 0–255) på 192×108, stillstand og blekk.
#   python3 rytme.py film.mp4 [tidskart.json | 0,6.2,12.2]     (kapittelstarter gir tall per del)
import sys, subprocess, json, numpy as np

src, W, H = sys.argv[1], 192, 108
num, den = subprocess.run(['ffprobe', '-v', 'error', '-select_streams', 'v', '-show_entries', 'stream=r_frame_rate', '-of', 'csv=p=0', src],
                          capture_output=True, text=True).stdout.strip().split('/'); fps = int(num) / int(den)
p = subprocess.Popen(['ffmpeg', '-v', 'error', '-i', src, '-vf', f'scale={W}:{H}', '-f', 'rawvideo', '-pix_fmt', 'gray', '-'], stdout=subprocess.PIPE)
diff, ink, prev = [], [], None
while (b := p.stdout.read(W * H)) and len(b) == W * H:
    a = np.frombuffer(b, np.uint8).astype(np.float32)
    ink.append(float((np.abs(a - np.median(a)) > 18).mean()))
    if prev is not None: diff.append(float(np.abs(a - prev).mean()))
    prev = a
d = np.array(diff); f = int(round(fps))
per_sec = [round(float(d[i:i + f].mean()), 2) for i in range(0, len(d) - f + 1, f)]
print(f'snitt {d.mean():.2f}  median {np.median(d):.2f}  p90 {np.percentile(d, 90):.2f}  p99 {np.percentile(d, 99):.2f}  '
      f'stillstand {(d < 0.05).mean() * 100:.0f} %  høyeste sekund {max(per_sec):.2f} ved {per_sec.index(max(per_sec))} s  '
      f'blekk median {np.median(ink) * 100:.1f} %')
if len(sys.argv) > 2:
    arg = sys.argv[2]
    chaps = json.load(open(arg, encoding='utf-8'))['CHAPTERS'] if arg.endswith('.json') else [[float(x), ''] for x in arg.split(',')]
    cuts = [c[0] for c in chaps] + [len(d) / fps]
    for (a, name), z in zip(chaps, cuts[1:]):
        s = d[int(a * fps):int(z * fps)]
        if len(s): print(f'  {name:<14} {a:6.1f}–{z:6.1f} s  snitt {s.mean():.2f}  p90 {np.percentile(s, 90):.2f}  stillstand {(s < 0.05).mean() * 100:.0f} %')
print('MOTION =', json.dumps(per_sec))
```

### lydbed.py

```python
# Lydbed fra filmens tidskart: én lyd per gest, en pad per kapittel, stillhet der bildet står stille. 48 kHz stereo WAV.
#   python3 lydbed.py tidskart.json lydbed.wav [grunntone, f.eks. 62 = D]
import sys, json, wave, numpy as np
TL = json.load(open(sys.argv[1], encoding='utf-8')); ROOT = int(sys.argv[3]) if len(sys.argv) > 3 else 62
SR, DUR = 48000, float(TL['DUR']); N = int(SR * DUR); L = np.zeros(N); R = np.zeros(N); rng = np.random.default_rng(7)
hz = lambda m: 440 * 2 ** ((m - 69) / 12); PENTA = [ROOT + o for o in (0, 2, 4, 7, 9, 12, 14, 16, 19, 21, 24, 26)]
CHORDS = [[0, 7, 12, 16], [5, 12, 17, 21], [9, 16, 21, 24], [7, 14, 19, 23]]   # I, IV, vi, V (halvtoner over grunntonen −12)
def tv(d): return np.arange(int(d * SR)) / SR
def add(s, t0, pan=0.0):
    i = int(t0 * SR); s = s[:max(0, N - i)]; L[i:i + len(s)] += s * np.cos((pan + 1) * np.pi / 4); R[i:i + len(s)] += s * np.sin((pan + 1) * np.pi / 4)
def chime(f, d=3.0, a=.11):
    t = tv(d); return a * np.minimum(1, t / .004) * sum(w * np.sin(2 * np.pi * f * m * t) * np.exp(-t * (1.6 + m * .9)) for m, w in [(1, 1), (2, .35), (3.01, .18), (4.2, .08)])
def pluck(f, d=1.0, a=.03):
    t = tv(d); return a * (np.sin(2 * np.pi * f * t) + .3 * np.sin(4 * np.pi * f * t)) * np.exp(-t * 6) * np.minimum(1, t / .002)
def thud(a=.14, f0=70, f1=40, d=.8):
    t = tv(d); f = f1 + (f0 - f1) * np.exp(-t * 18); return a * np.sin(2 * np.pi * np.cumsum(f) / SR) * np.exp(-t * 6) * np.minimum(1, t / .003)
def swell(d, a=.02):
    t = tv(d); n = np.convolve(rng.standard_normal(len(t)), np.ones(200) / 200, 'same'); return a * 6 * n * np.minimum(1, t / (d * .6)) * np.minimum(1, (d - t) / (d * .4))
def pad(notes, d, a=.026):
    t = tv(d); s = sum(np.sin(2 * np.pi * hz(n) * (1 + c / 100) * t) + .25 * np.sin(4 * np.pi * hz(n) * t) for n in notes for c in (-.12, .12))
    return s / (2 * len(notes)) * np.minimum(1, t / 2) * np.minimum(1, (d - t) / 1.5) * a

ch = TL['CHAPTERS'] + [[DUR, '']]
for i, ((a, _), (z, _)) in enumerate(zip(ch, ch[1:])):
    chord = CHORDS[0] if i in (0, len(ch) - 2) else CHORDS[i % 4]
    add(pad([ROOT - 12 + n for n in chord], z - a + 1.5), a)
step = 0
for t, typ, label, d in TL['GESTURES']:                      # én lyd per gest, stigende gjennom filmen
    f = hz(PENTA[min(step, 11)]); pan = np.sin(step * 1.7) * .5
    if typ == 'pop': add(pluck(hz(PENTA[5] + 12), a=.05), t, pan)
    elif typ in ('strek', 'skann'): add(swell(max(.8, d or 1.0)), t, pan); add(chime(f, a=.06), t + (d or .6), pan)
    elif typ == 'kobling': [add(pluck(hz(PENTA[k % 8]), .5, .02), t + k * .06) for k in range(10)]
    elif typ == 'bolge': add(swell(1.4, .025), t); add(chime(f * .5, 2.5, .08), t)
    elif typ == 'signal': [add(thud(.08), t + k) for k in range(int(max(1, d)))]; add(chime(f * 2, 1.5, .05), t + max(1, d))
    elif typ == 'stopp': add(pluck(hz(PENTA[11] + 12), .2, .06), t); add(thud(.22, 60, 32, 1.4), t + .25)
    else: add(chime(f), t, pan)
    step += typ != 'pop'
logo = [a for a, b, st in TL['TEXT'] if st == 'avsender']
if logo: add(chime(hz(ROOT + 36), 4.0, .06), logo[0])
g = np.ones(N)                                               # stillhet: alt ut, også etterklangen
for a, z in TL['SILENCE']:
    i0, i1, j0, j1 = int((a - .2) * SR), int(a * SR), int(z * SR), int((z + .25) * SR); g[i0:i1] = np.linspace(1, 0, i1 - i0); g[i1:j0] = 0; g[j0:j1] = np.linspace(0, 1, j1 - j0)
fo = np.ones(N); i = int((DUR - 2.5) * SR); fo[i:] = np.linspace(1, 0, N - i) ** 2
st = np.stack([L, R], 1) * (g * fo)[:, None]; st = st / np.abs(st).max() * 10 ** (-3 / 20)   # topp −3 dBFS
print(f'snitt {20 * np.log10(np.sqrt((st ** 2).mean())):.1f} dBFS, {len(TL["GESTURES"])} gester, {len(TL["SILENCE"])} stillheter')
with wave.open(sys.argv[2], 'wb') as w: w.setnchannels(2); w.setsampwidth(2); w.setframerate(SR); w.writeframes((st * 32767).astype(np.int16).tobytes())
```

## Fallgruver

- **Hel sirkel i Skia.** `arc()` og `ellipse()` fra −π/2 til −π/2 + 2π kan tegne ingenting. Tegn en hel runde som 0 → 2π, slik `circ()` og gesten `port` gjør.
- **Forvandling som krysser seg.** Går to former motsatt vei, krysser elementene hverandre. Snu punktlisten med `.reverse()`.
- **Floke som blir rosett.** Én sinus gir et blomstermønster. Legg sammen tre frekvenser med ulik fase per tråd, og la sentrum drive litt.
- **Kameraskyv inn i teksten.** Når venstre kolonne har tekst, må fokuspunktet på skjermen stå til høyre for x ≈ 700.
- **Tekst over objektet.** Flytt objektet med `CAM` eller formens koordinater, ikke teksten. Tekstposisjonene er faste for at filmen skal se lik ut hele veien.
- **Etiketter over tall.** Legg etiketter i en egen radius utenfor nodene, og tall innenfor.
- **Font som mangler i eksporten.** Bruk alltid `font.py` eller en innebygd data-URI. En systemfont kan se annerledes ut i eksporten enn i nettleseren.
- **Video i testnettleseren.** Playwrights Chromium spiller ikke H.264. En side med video som ikke laster i en hodeløs test, er ikke nødvendigvis feil.
- **Tynning.** Å fjerne elementer for å roe bildet gjør det fattig. Juster heller strekvekt, kontrast og stillhet.

## Rettigheter og etikk

- Referansefilmene brukes for prinsipper, rytme og stil. Ikke gjenskap scener, komposisjoner, tekst, typografi, musikk eller logoer fra dem, og ikke bruk Apples merker eller signaturlinje. Filmen skal se ut som avsenderens egen.
- Tall på skjermen trenger kilde. Svake kilder merkes, for eksempel «Undersøkelser tyder på det».
- Lyden er syntetisert og ikke hørt av et menneske. Si det alltid.
