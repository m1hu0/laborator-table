# Laboratorul de Table

Aplicație web de antrenament la table (backgammon), în română: joc contra botului cu analiză GNU Backgammon,
quiz de deschideri (inclusiv dubluri), pip count, lecții interactive, glosar, tablă 2D/3D, zaruri 3D, sunete.
Se publică pe GitHub Pages și se instalează ca aplicație (PWA) pe tabletă, telefon și laptop.

## Structură
- `index.html` — toată aplicația (HTML + CSS + JS într-un singur fișier; include modelul de zar și sunetele codate base64).
- `gnubg.wasm` — motorul GNU Backgammon compilat în WebAssembly (pornit într-un Worker din index.html).
- `lib/three.min.js` (r128), `lib/cannon.min.js` (0.6.2) — tabla 3D și fizica zarurilor. Versiunile sunt fixe; nu le actualiza.
- `fonts/` + `fonts.css` — Playfair Display (titluri) și Hanken Grotesk (text), doar subseturile latin și latin-ext.
- `manifest.webmanifest`, `icon-192.png`, `icon-512.png`, `sw.js` — instalare ca aplicație și funcționare offline.

## Rulare locală
```
python -m http.server 8080
```
apoi http://localhost:8080. Nu merge deschis direct ca fișier (file://), pentru că motorul GNU se încarcă prin fetch.

## Publicare (prima dată)
Necesită `git` și `gh` autentificat (`gh auth login`).
```
git init && git add -A && git commit -m "Laboratorul de Table"
gh repo create laborator-table --public --source . --push
gh api -X POST repos/{owner}/laborator-table/pages -f "source[branch]=main" -f "source[path]=/"
```
Linkul final: `https://<user>.github.io/laborator-table/` (devine activ în 1–2 minute).

## Update-uri
După orice modificare: testează local, apoi `git add -A && git commit -m "<ce s-a schimbat>" && git push`.
- `sw.js` ia `index.html` mereu de pe internet, deci update-ul apare la următoarea deschidere a aplicației, la toți.
- Dacă schimbi `gnubg.wasm`, ceva din `lib/` sau `fonts/`, crește versiunea `V` din `sw.js` (ex. `table-v3`), ca să se reîncarce și acestea.
- Dacă adaugi fișiere noi de care aplicația are nevoie offline, adaugă-le în lista `FILES` din `sw.js`.

## Reguli
- Interfața e în română; texte scurte, fără informații inutile.
- Când utilizatorul cere să schimbi sau să scoți ceva, șterge complet codul vechi, fără resturi.
- Păstrează mențiunea de licență CC BY 4.0 pentru modelul de zar 3D („Dice” de juliov.cg), afișată sub tabla de joc.
- Sunetele: zaruri/piese din @3d-dice/dice-box-threejs (MIT), interfață din uisfx „zen” (CC0).
- Stil: „club de table, seara” — postav verde, nuc, alamă, piese fildeș (tu) și abanos (bot). Fără gradiente decorative, fără emoji, fără etichete cu majuscule.
- Antrenorul AI depinde de Claude (window.claude) și nu e disponibil pe GitHub Pages; aplicația îl ascunde singură.
