# Studio Rooijakkers — v0

Open `index.html` in any browser. Geen build, geen dependencies.

## Designsysteem (vastgelegd, niet improviseren)

| | |
|---|---|
| Ground | `#F3EEE2` bone · surface `#FAF7EF` · dieper `#E8DFCB` · hairline `#CFC4A9` |
| Ink | `#1A1A18` · zacht `#6E6857` |
| Accent | `#A0472A` gebrande siena — **één** accent |
| Support | `#1B4B5A` diep teal — alleen voor de statusbol |
| Donker | ground `#16150F`, ink `#EFE9DA`, accent `#C86A45` |
| Display | Newsreader (serif) |
| Body/UI | Instrument Sans, 17px, line-height 1.65 |
| Spacing | 4 / 8 / 16 / 24 / 40 / 64 / 96 |
| Radius | 2px op kaarten en frames · pill alleen op knoppen |
| Motion | 240–420ms, `cubic-bezier(.2,.7,.3,1)` |

Alle kleuren staan als CSS-variabelen bovenaan `index.html`. Wijzig daar, nergens anders.

## Wat nog moet gebeuren (v1)

1. **Formulier koppelen.** Nu vangt JavaScript de submit op en toont enkel de
   bedanktboodschap — er vertrekt nog geen mail. Kies één van:
   - **Formspree** — maak een form aan, zet `action="https://formspree.io/f/JOUW_ID"`
     en `method="post"`, en verwijder de `e.preventDefault()`-handler onderaan.
   - **Netlify Forms** — host op Netlify, voeg `netlify` en `name="intake"` toe aan
     de `<form>`, en verwijder dezelfde handler.
   Test daarna met een echte inzending naar je eigen mailbox.

2. **Drie voorbeeldconcepten afwerken** — loodgieter, kinesist, restaurant.
   Elk krijgt een eigen palet en eigen prioriteit (loodgieter: grote belknop;
   kinesist: online afspraken; restaurant: menukaart + reserveren).
   De bakkerij staat er al volledig in als sjabloon.

3. **Echte gegevens** — ingevuld op twee na:
   - ✅ e-mail `robbe.rooijakkers@gmail.com`, telefoon `+32 487 29 45 02`, regio Limburg
   - ✅ BTW-regel verwijderd (nog geen KBO-inschrijving)
   - ✅ **foto** — `assets/robbe-rooijakkers.jpg`, bijgesneden tot 4:5 (560×700).
     Er staat een lichte warme correctie op via CSS (`filter:saturate(.9) sepia(.12)`
     op `.portrait--photo img`); die ene regel weghalen geeft de originele kleuren.
     De bron was een gecomprimeerde kopie van 1200 px breed — stuur de originele
     foto door als je hem scherper wil op een retina-scherm.
   - ⬜ **ondernemingsnummer** — zodra je bij de KBO ingeschreven bent, moet dit
     wettelijk terug op de site. De footer is de plek; de regel stond er al en is
     nu weg, dus die kan er zo weer in.

4. **Voor livegang** — favicon, `og:image` voor deelbaarheid, en een
   privacyverklaring (verplicht zodra het formulier persoonsgegevens verzamelt, GDPR).

## Bewuste keuzes

- **Geen prijzen.** Het formulier is de conversie: mensen vertellen wat ze willen
  en jij antwoordt met een voorstel op maat.
- **Geen valse klantlogo's of testimonials.** De voorbeelden zijn expliciet
  gelabeld als concept. Zodra je echt werk hebt, vervang je de frames.
- **Geen stockillustraties of AI-getekende mensen.** Alle beeldplekken zijn
  eerlijke placeholders met hun beeldverhouding erop.

## Animatiesysteem (toegevoegd)

Beweging volgt één systeem, gebouwd op de standaardcurves — niet zelf verzonnen:

| | |
|---|---|
| `--ease-out` | `cubic-bezier(0.23, 1, 0.32, 1)` — alles wat in- of uitkomt |
| `--ease-in-out` | `cubic-bezier(0.77, 0, 0.175, 1)` — beweging óp het scherm (tab-indicator) |
| `--fast` 160ms | hover en drukfeedback |
| `--slow` 240ms | UI-overgangen — blijft bewust onder 300ms |
| `--dur-2/3` 620/820ms | alleen scroll-reveals; marketing mag trager |
| `--stagger` 55ms | tussen opeenvolgende items |

Wat beweegt, en waarom:

- **Woord-voor-woord kopreveal** — elk woord zit in een masker en schuift omhoog. De signatuur van de pagina.
- **Gestaffelde secties** — lijsten en stappen komen één voor één op, 55ms uit elkaar.
- **Lijnen tekenen zichzelf** — de scheidingslijnen en de streep boven elke stap.
- **Browserframe stijgt op** met zijn schaduw. De schaduw zit op een pseudo-element zodat alleen `opacity` animeert, niet `box-shadow`.
- **Tab-indicator** — er ligt een geklipte kopie van de tabrij overheen. Achtergrond en tekstkleur wisselen daardoor exact samen, in plaats van twee kleuren die los interpoleren.
- **Paneelwissel** — het oude voorbeeld rolt van onder naar boven dicht, het nieuwe rolt naar beneden open (`clip-path`, 380ms, ease-in-out). De hoogte van de container loopt mee zodat de pagina er niet onder springt. Tijdens de wissel staat het oude paneel even absoluut, zodat beide kunnen overlappen.

Regels die het systeem bewaakt:

- Alleen `transform` en `opacity` animeren (plus `clip-path` voor de tabs) — die slaan layout en paint over.
- Nergens `transition: all`, nergens `ease-in`, nergens `scale(0)`.
- Elke hover-beweging zit achter `@media (hover:hover) and (pointer:fine)`, zodat een tik op gsm geen valse hover afvuurt.
- `prefers-reduced-motion` haalt de verplaatsing weg maar houdt het rustige opdoemen — zachter, niet nul.
- Reveals vuren **één keer**; terugscrollen animeert niet opnieuw.

**Tweaks-paneel** (rechtsonder, volledig verborgen wanneer dicht): zet de beweging op
Rustig / Normaal / Expressief, of speel de animaties opnieuw af. Handig om live te
tonen aan een klant. De keuze wordt onthouden.

### Twee valkuilen die hierin zijn opgelost

1. **Positie en clip-path in één keer wijzigen animeert niet.** Zet je een element
   tegelijk op `position:absolute` én geef je het een nieuwe `clip-path`, dan heeft de
   browser geen vastgelegde beginstaat en springt het zonder overgang naar het eindpunt.
   De uitgang gebeurt daarom in twee stappen: eerst `.p-exit-pos` (alleen de positie),
   een reflow, en pas dan `.p-exit` (de animatie).
2. **`requestAnimationFrame` vuurt niet in een achtergrondtab.** Zonder vangnet blijft
   het binnenkomende paneel dichtgerold hangen. Naast de rAF staat daarom een
   `setTimeout(..., 60)`; wie het eerst is, wint.

### Wat nog met eigen ogen bekeken moet worden

De voorbeeldpagina is hier getest in een venster dat niet schildert: de overgangen
worden er wél aangemaakt (juiste duur en curve, gecontroleerd), maar hun klok loopt
niet, dus het afspelen zelf is niet bekeken. Open `index.html` in een echte browser
en klik een paar keer tussen de tabjes. Voelt de 380ms te traag of te snel, zet dan
`--dur-panel` bij (bovenaan het motion-blok), of gebruik Tweaks → Expressief om het
effect uitvergroot te zien.
