---
name: kosthold
description: >-
  Bruk når brukeren sender et bilde av mat eller et måltid, eller spør om hva de spiser,
  kalorier, makroer (protein, karbohydrat, fett) eller kostholdsstatistikk. Logger måltider,
  vann og vekt, og gir dags-, uke- og månedsstatistikk mot mål.
---
# Kosthold – matlogging og ernæringsstatistikk

Generisk skill for food logging: fotologging av måltider, kalorier og makroer, daglige mål, vann, vekt, streaks, ukes- og månedsstatistikk, matsøk, favoritter og oppskrifter.

## Konfigurasjon
- Datamappe: konfigurerbar, standard `/workspace/kosthold/`.
- Per bruker: `<datamappe>/<bruker>/log.jsonl` (append-only) og `<datamappe>/<bruker>/goals.json`. Ved én bruker kan filene ligge direkte i datamappen.
- Valgfritt: `favorites.json` og `recipes.json` i samme mappe.

## Workflow
1. **Identifiser** – fra bilde: list hver rett/komponent (f.eks. «kyllingfilet», «ris», «salat med dressing»). Bruk referanser i bildet (tallerken, bestikk, hånd) til å anslå porsjon i gram.
2. **Anslå porsjon med confidence** – `high` (pakning/vekt oppgitt, tydelig porsjon), `medium` (vanlig porsjon, synlig), `low` (skjult innhold, sauser, olje, blandingsretter).
3. **Slå opp næringsverdier** – i denne rekkefølgen:
   1. Matvaretabellen.no (primærkilde for norsk mat)
   2. USDA FoodData Central
   3. Næringsdeklarasjon på pakning/produsentens side, hvis brukeren oppgir produkt
   Oppgi alltid kilde. **Aldri finn på verdier**; finnes ikke treff, bruk nærmeste sammenlignbare matvare og si det eksplisitt, eller spør.
4. **Spør kort ved tvil** – kun når porsjon eller innhold er tvetydig og påvirker estimatet vesentlig (f.eks. «Var det ca. 1 eller 2 dl ris?»). Ett spørsmål, med et forslag brukeren kan bekrefte.
5. **Logg** – append én linje per måltid til `log.jsonl`. Endringer skrives som ny linje med `"type":"correction"` og `ref` til opprinnelig `id` – aldri overskriv.
6. **Svar** med oppsummeringsformatet under.

Andre handlinger: logg vann (`"type":"water"`, ml), vekt (`"type":"weight"`, kg), matsøk (slå opp i kildene og vis per 100 g og per porsjon), favoritter (lagre ofte brukte måltider for rask logging), oppskrifter (summer ingredienser, del på antall porsjoner, lagre i `recipes.json`).

## Dataformat
`log.jsonl` – én JSON per linje:
```json
{"id":"2026-01-15T12:30-1","type":"meal","datetime":"2026-01-15T12:30:00+01:00","meal":"lunsj",
 "items":[{"name":"Grovbrød","grams":80,"kcal":190,"protein_g":7.0,"carbs_g":32.0,"fat_g":2.4,"fiber_g":5.5,"sugar_g":2.0,"salt_g":0.8,
           "source":"Matvaretabellen.no","confidence":"high"}],
 "totals":{"kcal":190,"protein_g":7.0,"carbs_g":32.0,"fat_g":2.4,"fiber_g":5.5,"sugar_g":2.0,"salt_g":0.8},
 "confidence":"medium","photo":"<referanse/filsti eller meldings-id>","note":""}
```
- `meal`: `frokost` | `lunsj` | `middag` | `kveldsmat` | `snack`
- Andre typer: `{"type":"water","datetime":...,"ml":250}`, `{"type":"weight","datetime":...,"kg":0}`, `{"type":"correction","ref":"<id>",...}`.

`goals.json`:
```json
{"kcal":null,"protein_g":null,"carbs_g":null,"fat_g":null,"fiber_g":null,"sugar_g_max":null,"salt_g_max":null,
 "water_ml":null,"weight_goal_kg":null,"timezone":"Europe/Oslo","updated":null}
```
Mål settes av brukeren. Mangler mål, vis totaler uten sammenligning og tilby å sette mål (f.eks. basert på generelle anbefalinger), uten å presse.

## Statistikk
- **Dagstotal** mot mål: kcal, makroer, fiber, sukker, salt, vann (sum, rest, %).
- **7- og 30-dagers snitt** (kun dager med logging; oppgi antall loggede dager).
- **Makrofordeling** i % av energi (protein og karbo 4 kcal/g, fett 9 kcal/g).
- **Toppmatvarer** etter frekvens og energibidrag.
- **Måltidstiming**: typiske tidspunkt, første/siste måltid, fordeling per måltidstype.
- **Trender**: kcal og vekt over tid (glidende snitt), dager innenfor mål.
- **Streak**: antall sammenhengende dager med logging.
- **Innsikt**: én kort, konkret og vennlig observasjon (f.eks. «Proteininntaket er jevnere på hverdager enn i helgene»).

## Oppsummeringsformat (svar til bruker)
```
🍽️ Lunsj 12:30 – logget
• Grovbrød, 80 g – 190 kcal (Matvaretabellen) [høy]
• ...
Sum: 520 kcal · P 28 g · K 55 g · F 18 g · fiber 8 g (± ~15 %)

I dag: 1 340 / 2 000 kcal · P 82/120 g · vann 1,2/2,0 l
Streak: 6 dager 🔥
💡 <kort innsikt>
Stemmer porsjonene? Svar med korrigering, så oppdaterer jeg.
```
Ukes-/månedsrapport: snitt, makrofordeling, toppmatvarer, måltidstiming, vekttrend, dager innenfor mål, én innsikt.

## Prinsipper
- Estimater er estimater: vis usikkerhet (confidence og omtrentlig ±), særlig for fotologging.
- Oppgi kilde; aldri oppdiktede næringsverdier.
- Ingen medisinske diagnoser eller behandlingsråd; henvis til lege/klinisk ernæringsfysiolog ved spørsmål om sykdom, spiseforstyrrelser eller spesielle behov.
- Ingen skamming eller moralisering av mat («godt/dårlig»). Nøytralt, støttende språk.
- Brukerens data er private; del ikke logg uten at brukeren ber om det.

## Vedlikehold
Skillens eier (en ernæringsassistent) oppdaterer denne skillen når metoder, kilder (f.eks. nye versjoner av Matvaretabellen eller FoodData Central), anbefalinger eller dataformat endres. Endringer i dataformat skal være bakoverkompatible med eksisterende logger.
