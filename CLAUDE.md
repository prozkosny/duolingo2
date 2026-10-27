# CLAUDE.md — Angličtina krok za krokem

Interaktivní webová aplikace pro výuku angličtiny pro česky mluvící uživatele.
Uživatel si zvolí pokročilost (CEFR A1–C1) a procvičuje v pěti režimech.

## Architektura
- **Jediný soubor `index.html`** (HTML + CSS + vanilla JS, bez buildu a závislostí).
  Název `index.html` je povinný kvůli GitHub Pages — nepřejmenovávat.
- Hosting: GitHub Pages z větve `main`, kořen repa.
- Stav uživatele v `localStorage` pod klíčem `english-app-v1`
  (`level`, `xp`, `streak`, `lastDay`, `best`). Při změně struktury zvyš verzi klíče.
- Výslovnost přes Web Speech API (`speechSynthesis`, `en-GB`) — **jen na kliknutí** (tlačítko 🔊),
  nikdy automaticky (požadavek uživatele). Při absenci API tiše vynechá.

## Struktura kódu (`<script>` v index.html)
- `LEVELS` — seznam úrovní (id, název, popis).
- `CONTENT[level]` — obsah po úrovních:
  - `vocab`: `[anglicky, česky]`
  - `sentences`: `[anglická věta bez interpunkce, český překlad]`
  - `gaps`: `[věta s "___", správně, [2 chybné], česká nápověda]`
- `MODES` — režimy: `flash` (kartičky), `quiz` (výběr překladu), `write` (psaní),
  `build` (skládání vět), `gap` (doplňování).
- Round engine: `startRound` → `renderQuestion` → `render<Mode>` → `answer` → `nextQuestion` → `showResult`.
- Kolo má `ROUND_SIZE` otázek; správná odpověď +10 XP (kartička +5 XP).
- Výsledek ≥ 90 % nabídne posun na vyšší úroveň.

## Jak přidat obsah / režim
- Nové slovíčko/větu: přidat do `CONTENT.<úroveň>` ve správném formátu. Kolo má
  min(`ROUND_SIZE`, počet položek) otázek; každá úroveň potřebuje ≥ 4 slovíčka (distraktory v kvízu).
- Nový režim: položka v `MODES` + funkce `render<Mode>()` + záznam v mapě v `renderQuestion()`
  + případně větev v `buildQuestions()`.

## Konvence
- UI texty česky, kód a komentáře anglicky.
- Uživatelský text vkládaný do HTML vždy přes `esc()`.
- Barvy jen přes CSS proměnné v `:root` (světlý i tmavý režim).
- Mobil first: musí fungovat na šířce 375 px.
- EOL = LF (viz `.gitattributes`).

## Spuštění / ověření
- Lokálně: otevřít `index.html` v prohlížeči (žádný server není potřeba).
- Produkce: `https://prozkosny.github.io/duolingo2/` (po zapnutí GitHub Pages).

## Nápady na rozvoj
- Více obsahu na úroveň, tematické sady (cestování, práce…).
- Opakování chybných odpovědí (spaced repetition).
- Vstupní rozřazovací test pro automatické určení úrovně.
- Poslechový režim (diktát přes speechSynthesis).
