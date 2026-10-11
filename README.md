# kosthold – skill for matlogging og ernæringsstatistikk

En generisk agent-skill (SKILL.md) for å logge måltider fra bilder eller tekst, estimere porsjoner med usikkerhet, slå opp næringsverdier (Matvaretabellen.no først, deretter USDA FoodData Central) og gi daglig, ukentlig og månedlig statistikk mot mål: kalorier, makroer, fiber, sukker, salt, vann, vekt og streaks. Støtter matsøk, favoritter og oppskrifter.

Data lagres i en konfigurerbar mappe (standard `/workspace/kosthold/`) som en append-only `log.jsonl` og en `goals.json`.

Estimater er estimater – skillen viser usikkerhet, oppgir kilder, stiller ingen medisinske diagnoser og skammer aldri.

## English summary
A generic agent skill for photo- and text-based food logging with calorie and macro estimates (with confidence levels and cited sources: Norwegian Food Composition Table first, then USDA FoodData Central), daily goals, water, weight, streaks, and weekly/monthly stats. Data is stored as an append-only JSONL log plus a goals file. Written in Norwegian bokmål.

## License
MIT
