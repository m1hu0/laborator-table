# Tablă și piese cel-shaded („2D cu adâncime”)

## De ce
Tabla și piesele arată plat, ca o foaie de hârtie, mai ales când cad peste ele zarurile 3D realiste. Vrem adâncime și o senzație mai autentică și mai interactivă, fără tablă 3D completă (care a fost scoasă pentru că era plină de buguri). Ca referință: jocurile 3D cel-shaded, care au volum, dar arată desenate.

## Obiective
- Aceeași vedere de sus ca acum, aceleași culori de club (postav verde, nuc, alamă, fildeș pentru tine, abanos pentru bot), aceeași atingere și tragere.
- Stilul ales e „A, cel-shaded discret” din mostre:
  - **Contur:** fin și închis (`#140d08`) la ramă, triunghiuri, piese și zaruri.
  - **Umbrire:** culori plate în 2 tonuri, fără degradeuri de umbrire.
  - **Piese:** discuri cu grosime vizibilă (o margine sub fața de sus), o semilună mai închisă pe față și umbră tare, plată, dedesubt.
  - **Triunghiuri:** o jumătate în tonul deschis, cealaltă în tonul închis.
  - **Ramă:** bandă deschisă sus, bandă închisă jos și umbră tare a ramei pe postav (sus și stânga).
- **Interactivitate:**
  - Piesa selectată sau trasă se ridică puțin, iar umbra i se mărește și se estompează.
  - Când o piesă aterizează, se turtește scurt și revine („țoc”, ~120 ms). Momentul coincide cu sunetul existent de așezare.
  - Cu „reduce motion” activ, nu se animă nimic.
- **Peste tot:** joc, quiz, lecții, pip count, raport, deschideri. Toate folosesc aceleași funcții de desen.
- **Zaruri:** tot 3D, cu fizică și aruncare cu mâna, dar cu material „toon” (lumină în 2–3 trepte) și contur închis, ca piesele. Modelul de zar rămâne, deci și mențiunea de licență CC BY 4.0.

## Ce nu intră
- Tablă înclinată, perspectivă sau tablă WebGL.
- Schimbarea culorilor de bază, a layout-ului sau a mărimii tablei.
- Stilul „ilustrat” (contur gros, fibră desenată, textură de postav).

## Constrângeri
- Fără gradiente decorative (CLAUDE.md). Umbrirea în trepte plate respectă regula; degradeurile de umbrire existente pe piese, triunghiuri și ramă dispar.
- `lib/three.min.js` rămâne r128 (fără actualizare). Materialul toon și conturul trebuie să existe în r128.
- Inelele de selecție, de hint și destinațiile luminate (glow) rămân cum sunt și trebuie să se vadă clar peste noul stil.
- Performanță: tabla se redesenează la fiecare mutare și la fiecare pas de tragere. Formele noi nu au voie să facă tragerea să sacadeze pe telefon.

## Cum se face
- Se refac `defs`, `checker` și `drawBoard` din `index.html`: culori plate, contur, grosimea pieselor, umbre tari. Se aplică la fel piesei de pe bară (rază 13) și pieselor scoase din tavă.
- Ridicarea și „țocul”: transformări SVG în codul de tragere din `makeMover` și în `animateMoves`.
- Zarurile: în `DICE3D`, materialul perlat devine `MeshToonMaterial` cu o hartă de trepte. Conturul se face cu un al doilea mesh din aceeași geometrie, ușor mărit, cu fețele întoarse, în culoarea conturului.

## Variante luate în calcul
1. **Aleasă:** desenul SVG existent refăcut, plus material toon pentru zaruri. O singură funcție de desen schimbă toate tablele, iar atingerea rămâne neatinsă.
2. Filtru de posterizare peste tabla de acum: rapid, dar fără control asupra grosimii, umbrelor și conturului; arată murdar.
3. Tablă desenată în WebGL: practic tabla 3D scoasă, cu aceleași riscuri.

## Backup
Designul de acum e salvat local în ramura git `backup-design-vechi` (commit `5cd000e`). După ce aprobi noul design implementat, ramura se șterge.

## Lăsat la alegerea constructorului
- Nuanțele exacte ale tonurilor deschis/închis, pornind de la mostre:
  - fildeș `#f3ead6` / margine `#c9b78f`, abanos `#3a302a` / margine `#120d0a`;
  - triunghiuri `#e3d3ab`/`#bfa977` și `#8e2a2e`/`#651b1f`, postav `#1d4a39`.
- Grosimea conturului la fiecare mărime de tablă, ca să se vadă la fel pe telefon și pe laptop.
- Numărul de trepte de lumină la zaruri (2 sau 3) și grosimea conturului lor.
