# Tabla nouă (varianta B, texturi foto), pip count și zarurile pe tablă

## De ce
Tabla arăta plată și fără material. Piesele albe aveau un arc gri care le strica, iar forma tablei nu plăcea. Zarurile aruncate apăreau mici, sus în stânga, deasupra tablei, și nu se vedeau firesc. Utilizatorul vrea o tablă care să pară un obiect real, liniștit, în stilul „club de table, seara”. Totul trebuie să fie uniform și continuu.

## Aspectul aprobat (mostra finală: `.superpowers/brainstorm/…/content/tex/tabla.html`)

### O singură suprafață de piele
Rama, bara și marginea tăvii sunt aceeași piele maro. Câmpurile și cele două buzunare ale tăvii sunt adâncituri în ea:
- toate adânciturile au aceeași rotunjire (12) și aceeași margine închisă;
- au aceeași umbră interioară, lină, pe toată marginea, făcută din 10 straturi fine;
- triunghiurile sunt tăiate pe forma câmpului, ca să nu iasă peste colțuri.

### Geometrie uniformă
Rama are 18 pe toate laturile: sus, jos, stânga, dreapta, bara și pielea dintre câmp și tavă. Pe lățime, dinspre stânga:

| ramă | câmp | bară | câmp | ramă | tavă | ramă |
|---|---|---|---|---|---|---|
| 18 | 6 puncte | 18 | 6 puncte | 18 | 40 | 18 |

- Lățimea unui punct iese ≈ 35,8. Tabla rămâne 552×424.
- `TOP` = 18, `BOT` = 406.

### Colțuri concentrice
Adânciturile au rotunjirea 12. Marginea exterioară are 12 + 18 = 30, deci rama are aceeași grosime și în colțuri.

### Tava
- Sunt două buzunare separate de piele, sus pentru adversar și jos pentru jucător, la mijlocul ramei din dreapta.
- Între buzunare rămâne piele de 18.
- Tava are lățimea 40: cât o piesă (diametru 34) plus 3 de fiecare parte.
- Piesele scoase stau pe muchie, la mărimea reală a pieselor de pe tablă (34 lungime, 8 grosime). Arată ca un cilindru: mai luminoase la mijloc, mai închise spre capete, cu inelul teșit vizibil lângă capete, o muchie fină de lumină și o umbră pe capetele din stânga și dreapta, nu dedesubt, ca să nu intre peste piesa următoare (neagră și intensă: opacitate .9 la albe, .55 la negre; capetele albelor se închid până la #5f553f). Încap 15 într-un buzunar.

### Texturi foto
- Sursa pentru piele: două texturi CC0 de pe ambientCG (Leather037, Leather039). Postavul și tava nu mai folosesc fotografia Fabric034, care avea arcuri subțiri, curbate, vizibile pe tablă; sunt generate din zgomot fin filtrat pe bandă și luminat ca relief.
- Relieful e luat din harta de normale, luminat din stânga-sus și nuanțat cu culorile tablei.
- Rezultă 5 plăci de 1024 px, desenate la 200 de unități (aproape 1:1 pe ecran, ca să rămână clare); relieful trece printr-un filtru care scoate petele mari (rotunde): `tex/rail.jpg` (ramă), `tex/cream.jpg`, `tex/red.jpg`, `tex/felt.jpg`, `tex/tray.jpg`.
- Plăcile se generează o singură dată și intră în proiect; arhivele originale nu.
- Se adaugă în `FILES` din `sw.js`, iar `V` crește.

### Culori stinse
| element | culoare |
|---|---|
| piele | `#3a2a20` |
| postav | `#21423a` |
| crem | `#bdb093` |
| vișiniu | `#5c2c2a` |
| fața pieselor albe | `#ded5c0` → `#c9bda1` |
| muchia pieselor albe | `#b0a385` |
| zaruri | `#ddd3bd` |

Nimic nu are luciu și nu există cusături.

### Piesele
- Fața e plată, cu o lumină foarte moale. Muchia are aceeași culoare.
- Fibră de lemn foarte discretă pe față (`tex/grain.png` închisă pe albe, `tex/grain-w.png` deschisă pe negre, opacitate .022, pattern 96 unități rotit 8°).
- Au un inel teșit.
- Nu au arc gri și nici luciu.

### Stivele
- Între piesele de pe același punct rămâne un rost (pas 37,5), deci până la 5 piese nu se suprapun.
- Peste 5 se strâng, ca acum.

### Bara
- Bara are 18, deci e mai îngustă decât o piesă.
- Piesele lovite stau pe mijlocul barei, în dreptul spațiului gol dintre vârfurile triunghiurilor, la 80% din mărimea unei piese.
- Ale adversarului urcă de la mijloc în sus, ale jucătorului coboară de la mijloc în jos.

### Numerele punctelor
- Font: Playfair Display cursiv, 500, cam 10 px, cu cifre de aceeași înălțime și lățime.
- Sunt gravate: mai închise decât pielea, cu o muchie de lumină foarte slabă dedesubt.
- Stau pe mijlocul ramei, centrate după înălțimea cifrelor.
- Nu există nicio linie de lumină deasupra lor.

## Funcții noi pe tabla de joc
1. **Pip count:**
   - stă în capetele barei: jos al jucătorului de jos, sus al celui de sus;
   - are același stil ca numerele, puțin mai mic, ca să încapă trei cifre;
   - comutatorul „Pip count pe tablă” stă în Setări → Joc și e pornit implicit;
   - apare doar pe tabla de joc, niciodată la exercițiul de pip count și nici pe alte table.
2. **Zarurile pe tablă:**
   - după aruncare stau în jumătatea din dreapta pentru jucătorul la mutare, în cea din stânga pentru bot;
   - zarul folosit se estompează;
   - la dublă apar patru zaruri mai mici, care se sting pe rând;
   - la aruncarea de deschidere, fiecare zar stă pe partea jucătorului lui;
   - zarurile din bara de deasupra tablei de joc dispar.

## Ce nu se schimbă
- Regulile, botul, analiza, hint-ul și modul în doi (cu tabla fixă sau întoarsă).
- Animațiile de final; doar coordonatele tăvii lor se actualizează.
- Atingerea și tragerea pieselor funcționează la fel. Coordonatele se recalculează din noile constante.
- Toate celelalte table din aplicație (quiz, lecții, deschideri, raport, pip count) primesc automat noul aspect.

## Abordări luate în calcul
- **Texturi generate din cod:** respinsă. Arăta fals și neclar, după două încercări.
- **Culori plate:** comparată direct cu varianta foto. Utilizatorul a ales texturile foto.
- **Texturi foto, cu relieful din harta de normale:** aleasă. Arată natural, iar fișierele sunt mici.

## Lăsat constructorului
- Mărimea și unghiurile zarurilor de pe tablă, plus aranjarea a patru zaruri.
- Poziția pip count-ului în bară, ca să nu stea peste piesele lovite.
- Desenarea zarurilor 3D după aruncare: acum se trec pe tablă ca zaruri 2D.
- Piesele scoase negre sunt desenate cu 1,2 mai subțiri (6,8) decât cele albe, pentru că umbra lor se contopește cu piesa; pasul rămâne același.
