# Tablă și piese cel-shaded — plan de implementare

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Tabla, piesele și zarurile primesc stilul „cel-shaded discret”: contur închis, umbrire în trepte plate, piese cu grosime și umbră tare, ridicare la selecție/tragere și „țoc” la aterizare. Vederea de sus și atingerea rămân aceleași.

**Architecture:** Toate tablele din aplicație se desenează prin `defs`, `checker` și `drawBoard` din `index.html` (SVG). Le refacem pe ele, deci stilul apare peste tot. „Țocul” se face cu o clasă CSS (`land`) pe piesa de sus a punctului de destinație, pusă prin opțiunea nouă `opt.land` a lui `drawBoard`. Ridicarea se face printr-un parametru nou al lui `checker`. La zaruri (`DICE3D`, three r128), materialul perlat devine `MeshToonMaterial`, iar conturul e un mesh „inverted hull”.

**Tech Stack:** SVG + CSS + JavaScript simplu, three.js r128 (fără actualizare). Nu există framework de teste: verificările se fac în browser pe `http://localhost:8080` (`python -m http.server 8080`), cu fragmente JS rulate prin javascript_tool, plus capturi de ecran.

**Spec:** `docs/superpowers/specs/2026-11-13-cel-shaded-design.md`

## Global Constraints
- Vederea de sus, layout-ul, mărimea tablei, culorile de bază și atingerea/tragerea rămân neschimbate.
- Contur `#140d08`. Tonuri de pornire:
  - fildeș `#f3ead6` / margine `#c9b78f` / semilună `#e2d5b6`;
  - abanos `#3a302a` / margine `#120d0a` / semilună `#2a221d`;
  - triunghiuri `#e3d3ab`/`#bfa977` și `#8e2a2e`/`#651b1f`;
  - postav `#1d4a39`.
- Fără gradiente decorative. Degradeurile de umbrire (`you`, `opp`, `youEdge`, `oppEdge`, `wood`, `felt`, `triA/B/Ab/Bb`, `vig`) și filtrul `sh` dispar complet, fără resturi. Rămân doar `glow` și markerul `ah`.
- Inelele de selecție/hint și destinațiile luminate rămân vizibile peste noul stil.
- `prefers-reduced-motion`: nicio animație (regula CSS globală existentă de la linia ≈468 acoperă clasa `land`).
- `lib/three.min.js` rămâne r128. Modelul de zar și mențiunea CC BY 4.0 rămân.
- Ramura `backup-design-vechi` NU se șterge până când utilizatorul aprobă explicit noul design implementat.
- După implementare: test local, apoi commit și push pe `main` (CLAUDE.md).

## Review Focus
1. Tragerea pe telefon nu sacadează: `drawBoard` se cheamă la fiecare pas de tragere, deci timpul pe un desen nu are voie să crească față de acum (măsurat în Task 1).
2. Stive de peste 5 piese (cifra de pe piesa de sus) și stive suprapuse: cifra rămâne lizibilă pe noua față, iar grosimea pieselor nu acoperă piesa de dedesubt complet.
3. Piese pe bară (rază 13) și în tavă (dreptunghiuri subțiri): același stil, lizibile la mărime mică.
4. Inelul de hint (`hintring`), inelul de selecție și triunghiurile destinație se văd clar peste contur și umbre.
5. Zarurile toon păstrează fețele lizibile (punctele) pentru ambele culori (tu/bot) și „passive kept” (zarurile rămase pe tablă) arată la fel.

---

### Task 1: Tablă și piese statice în stil cel-shaded

**Files:**
- Modify: `index.html`: CSS (lângă `.boardwrap`, ≈150), `function defs` (≈781), `function checker` (≈803), `function drawBoard` (≈808–880, inclusiv bara și tava), `function miniDie` (≈880).

**Interfaces:**
- Produces:
  - `checker(parent,svgId,x,y,who,r=R-.5,extra={},lift=0)` → `<g class="ck">` care conține, în ordine:
    - `ellipse.ck-sh` (umbră tare, `fill:#000`, opacitate `.38`; dacă `lift>0`, deplasată cu `lift` și cu opacitate `.22`);
    - `ellipse.ck-side` (grosimea, deplasată în jos cu `r*.2`);
    - `circle.ck-face`;
    - `path.ck-band` (semiluna);
    - un inel interior.

    Cu `lift>0`, corpul se desenează ridicat cu `lift` px.
  - `drawBoard` desenează piesa de sus selectată (`opt.selected`) cu `lift=6`.

- [ ] **Step 1: Măsoară performanța de acum (baseline)**

```js
const svg=document.getElementById("qBoard");const t0=performance.now();
for(let i=0;i<200;i++)drawBoard(svg,START,{});const base=(performance.now()-t0)/200;base
```
Notează valoarea în ledger (`Task 1: baseline drawBoard = X ms`).

- [ ] **Step 2: Scrie verificarea care eșuează**

```js
const svg=document.getElementById("qBoard");drawBoard(svg,START,{selected:13});
const ids=[...svg.querySelectorAll("defs [id]")].map(e=>e.id.replace(svg.id,""));
console.assert(!ids.some(i=>/^(you|opp|youEdge|oppEdge|wood|felt|triA|triB|triAb|triBb|vig|sh)$/.test(i)),"old gradients gone");
console.assert(!svg.innerHTML.includes("Gradient"),"no gradients in board");
const ck=svg.querySelector("g.ck");
console.assert(ck&&ck.querySelector(".ck-sh")&&ck.querySelector(".ck-side")&&ck.querySelector(".ck-face")&&ck.querySelector(".ck-band"),"checker parts");
console.assert(ck.querySelector(".ck-face").getAttribute("stroke")==="#140d08","ink outline");
console.assert(svg.querySelectorAll("g.ck").length===30,"15+15 checkers");
```

- [ ] **Step 3: Rulează și confirmă că pică** — aserțiunile „old gradients gone” și „checker parts” eșuează.

- [ ] **Step 4: Implementează stilul.**
  - **`defs`:** rămân doar `glow` și `ah`.
  - **Rama:** bandă exterioară `#3b2819`, bandă sus `#4a3220`, bandă jos `#33221a`, totul cu contur.
  - **Buza câmpurilor:** `#20150d` cu contur.
  - **Postavul:** `#1d4a39` plat, plus umbra tare a ramei (`#000`, opacitate `.28`, ~9px sus și stânga, în fiecare câmp).
  - **Triunghiurile:** tonul deschis plus jumătatea dreaptă în tonul închis, cu contur `stroke-width` ≈1.5.
  - **Bara:** benzi de nuc plate, cu contur.
  - **Tava:** piesele scoase sunt dreptunghiuri plate cu contur și o bandă mai închisă jos.
  - **Cifra de pe stivele mari:** `#4a3d27` pe fildeș și `#efe7d4` pe abanos, ca acum.
  - **`miniDie`:** fundal plat cu contur, fără degradeu.
  - **`.boardwrap`:** `drop-shadow` rămâne (umbra tablei pe pagină).

- [ ] **Step 5: Rulează verificarea Step 2** — trece.

- [ ] **Step 6: Măsoară din nou (Step 1)** — `X_nou ≤ 1.2 × baseline`. Dacă e mai mare, simplifică formele (de exemplu, inelul interior) până trece.

- [ ] **Step 7: Verificare vizuală.** Capturi de ecran:
  - `#joc` cu o stivă de 6 piese (`G.pos.p[6]=6`);
  - o piesă pe bară și piese în tavă;
  - selecție plus hint (`useHint`);
  - `#quiz`, `#pip`, o lecție, `#raport`.

  Verifică punctele 2–4 din Review Focus pe capturi.

- [ ] **Step 8: Commit** — `git commit -am "Stil cel-shaded: tablă și piese"`

### Task 2: Ridicare la tragere și „țoc” la aterizare

**Files:**
- Modify: `index.html`: CSS (`@keyframes land`, `.ck.land`), `drawBoard` (opțiunea `land`), `makeMover` (ghost-ul de tragere cu `lift`, `S.lastLand` în `doStep`), `animateMoves` (≈1295), demonstrația din lecții (≈895), `tween` (≈1291), dacă desenează piesa lovită.

**Interfaces:**
- Consumes: `checker(...,lift)` și `g.ck` din Task 1.
- Produces:
  - `drawBoard(svg,pos,opt)` acceptă `opt.land:number[]`: piesa de sus a fiecărui punct primește clasa `land` (pentru `25`: ultima piesă de pe bară; pentru `0`: ultima piesă din tavă, dacă e desenată ca `g.ck`; altfel se ignoră).
  - CSS:

```css
@keyframes land{0%{transform:scale(1.1,.88)}60%{transform:scale(.97,1.03)}100%{transform:none}}
.ck.land{transform-box:fill-box;transform-origin:50% 80%;animation:land .14s ease-out}
```

- [ ] **Step 1: Scrie verificarea care eșuează** (în `#joc`, faza `move`, ca în testele de hint: `DICE3D.clear();G.busy=false;G.phase='wait';G.pos=clone(START);startYourTurn([6,4])`)

```js
JM.doStep(24,18,false);
console.assert(document.querySelector("#jBoard g.ck.land"),"land class after drop");
// ridicare: ghost-ul de tragere are umbra deplasată
const svg=document.getElementById("jBoard");const g=checker(svg,svg.id,0,0,"you",R+1.5,{},9);
const sh=g.querySelector(".ck-sh"),face=g.querySelector(".ck-face");
console.assert(+face.getAttribute("cy")<0&&+sh.getAttribute("cy")>0,"lifted body above, shadow below");g.remove();
// animație: după o mutare animată a botului, ultima destinație primește land
const p=clone(START);let landed=false;animateMoves(svg,p,[["o",1,7,6]],{},()=>{landed=!!svg.querySelector("g.ck.land");});
await new Promise(r=>setTimeout(r,900));console.assert(landed,"land after animated move");
```

- [ ] **Step 2: Rulează și confirmă că pică** — „land class after drop” eșuează.

- [ ] **Step 3: Implementează.**
  - `drawBoard` aplică `opt.land`.
  - `makeMover.doStep` pune `S.lastLand=to` înainte de `fin`.
  - `draw()` îl trece ca `land:[S.lastLand]` și apoi îl golește (o singură redesenare).
  - Ghost-ul de tragere (`checker(...,R+1.5,{...},9)`) se desenează ridicat.
  - `animateMoves` ține destinația ultimei aterizări și o trece ca `land` la următorul `drawBoard` și la `fin`.
  - Același lucru în demonstrația din lecții.
  - Piesele care zboară (`g` din `animateMoves`/demo) folosesc `lift=9` cât sunt în aer.

- [ ] **Step 4: Rulează verificarea Step 1** — trece. Verifică și că, cu `matchMedia("(prefers-reduced-motion: reduce)")` emulat, regula globală anulează animația (`getComputedStyle(el).animationName==="none"`).

- [ ] **Step 5: Verificare manuală:** o tură jucată cu tragere și cu dublu-tap. Captură în timpul tragerii (piesa ridicată), fără erori în consolă.

- [ ] **Step 6: Commit** — `git commit -am "Stil cel-shaded: ridicare și țoc la aterizare"`

### Task 3: Zaruri toon

**Files:**
- Modify: `index.html`: `DICE3D` (`makeDie`, `setTint`, `build`: lumini, umbre, tone mapping), plus ștergerea completă a codului devenit nefolosit: `STY`, `vnoise`, `marbleTex`, `marbleCache`, `texCache` (dacă e nefolosit), `makeEnv`, `env`.

**Interfaces:**
- Produces: `makeDie()` → `Group` cu `userData:{outer,inner,line}`.
  - `outer` e `MeshToonMaterial` cu `gradientMap` (`DataTexture` cu 3 trepte, `NearestFilter`).
  - `line` e un mesh din `GEO_O` cu `MeshBasicMaterial({color:0x140d08,side:THREE.BackSide})`, scalat ~1.06.
  - `setTint(i,side)` schimbă `outer.material.color` (tu `#f3ead6`, bot `#3a302a`) și culoarea punctelor ca acum.

- [ ] **Step 1: Scrie verificarea care eșuează** (în `#joc`, după o aruncare 3D: `await DICE3D.throwDice({side:"you",auto:true})`)

```js
console.assert(typeof marbleTex==="undefined"&&typeof makeEnv==="undefined","old dice code removed");
// prin scena expusă pentru test: DICE3D._dbg() → {outerType, hasLine}
const d=DICE3D._dbg();console.assert(d.outerType==="MeshToonMaterial"&&d.hasLine,"toon + outline");
```
Ruling permis: dacă nu vrei un `_dbg` permanent, verifică prin `renderer.info`/captură. Documentează în ledger.

- [ ] **Step 2: Rulează și confirmă că pică.**

- [ ] **Step 3: Implementează materialul toon și conturul.**
  - Umbrele devin tari: `renderer.shadowMap.type=THREE.BasicShadowMap`, iar umbra de pe podea are opacitate ~`.38`.
  - `toneMapping` devine `THREE.NoToneMapping`, ca tonurile plate să rămână plate.
  - Șterge complet codul de marmură și `makeEnv`.

- [ ] **Step 4: Rulează verificarea Step 1** — trece. Captură cu zarurile în aer și pe tablă (ambele culori), deschiderea cu un zar pentru fiecare (`openingRoll`) și zarurile rămase („kept”). Verifică punctul 5 din Review Focus.

- [ ] **Step 5: Commit** — `git commit -am "Stil cel-shaded: zaruri toon"`

### Task 4: Aprobare vizuală și publicare
- [ ] **Step 1:** Rulează toate verificările din Task 1–3, plus testele de hint și Înapoi/Refă de dinainte. Fără erori în consolă.
- [ ] **Step 2:** Arată utilizatorului capturi din joc, quiz și lecție. **Nu șterge `backup-design-vechi`.**
- [ ] **Step 3:** Doar după ce utilizatorul spune că-i place cum arată în aplicație: `git push`, apoi verifică dacă build-ul Pages a ajuns pe noul commit (`gh api repos/m1hu0/laborator-table/pages/builds/latest`). Până atunci, commit-urile rămân locale.
