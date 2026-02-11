
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Dice Roller</title>
  <style>
    :root{
      --bg0:#0b0f14;
      --bg1:#121826;
      --stroke:rgba(255,255,255,.08);
      --text:#e7eefc;
      --muted:rgba(231,238,252,.65);
      --shadow: 0 18px 60px rgba(0,0,0,.55);
      --radius: 14px;
      --radius2: 18px;
      --btnshadow: 0 10px 22px rgba(0,0,0,.38);
      --mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
      --sans: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji";
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family:var(--sans);
      color:var(--text);
      background:
        radial-gradient(900px 500px at 75% 30%, rgba(124,58,237,.18), transparent 55%),
        radial-gradient(900px 500px at 25% 60%, rgba(255,106,0,.14), transparent 55%),
        linear-gradient(180deg, var(--bg1), var(--bg0));
      min-height:100vh;
      display:grid;
      place-items:center;
      padding:22px;
    }

    .wrap{
      width:min(1120px, 96vw);
      display:flex;
      flex-direction:column;
      gap:14px;
    }

    .pageTitle{
      display:flex;
      align-items:center;
      justify-content:space-between;
      padding:4px 2px;
    }
    .pageTitle h1{
      margin:0;
      font-size:22px;
      letter-spacing:.02em;
      font-weight:1000;
      text-shadow: 0 12px 35px rgba(0,0,0,.40);
    }
    .pageTitle .hint{
      font-size:12px;
      color:var(--muted);
    }

    .app{
      width:100%;
      display:grid;
      grid-template-columns: 1.15fr .85fr;
      gap:18px;
      align-items:stretch;
    }
    .panel{
      background: linear-gradient(180deg, rgba(255,255,255,.04), rgba(255,255,255,.02));
      border:1px solid var(--stroke);
      border-radius: var(--radius2);
      box-shadow: var(--shadow);
      overflow:hidden;
    }
    .left{
      padding:16px;
      display:grid;
      grid-template-rows:auto 1fr auto;
      gap:12px;
    }
    .topRow{
      display:flex;
      gap:10px;
      align-items:center;
      justify-content:space-between;
      flex-wrap:wrap;
    }
    .counter{ display:flex; gap:10px; align-items:center; flex-wrap:wrap; }
    .pill{
      background: rgba(0,0,0,.25);
      border:1px solid var(--stroke);
      border-radius: 999px;
      padding:6px 10px;
      display:flex;
      align-items:center;
      gap:8px;
      min-height:40px;
    }
    .pill label{ font-size:12px; color:var(--muted); letter-spacing:.02em; }
    .pill .value{
      font-family:var(--mono);
      font-size:14px;
      padding:0 6px;
      min-width:42px;
      text-align:center;
    }
    .btnSquare{
      width:32px;height:32px;
      border-radius:10px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(0,0,0,.28);
      color:var(--text);
      box-shadow: var(--btnshadow);
      cursor:pointer;
      display:grid;
      place-items:center;
      transition: transform .08s ease, filter .12s ease, background .12s ease;
      user-select:none;
    }
    .btnSquare:active{ transform: translateY(1px) scale(.98); }
    .btnSquare.orange{ background: rgba(255,106,0,.22); border-color: rgba(255,106,0,.35); }
    .btnSquare.orange:hover{ filter: brightness(1.08); }

    .btn{
      border-radius: 14px;
      padding:10px 12px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(0,0,0,.25);
      color:var(--text);
      cursor:pointer;
      box-shadow: var(--btnshadow);
      transition: transform .08s ease, filter .12s ease, background .12s ease;
      font-weight:900;
      letter-spacing:.02em;
      user-select:none;
      display:inline-flex;
      align-items:center;
      gap:8px;
      min-height:40px;
    }
    .btn:active{ transform: translateY(1px) scale(.99); }
    .btn:hover{ filter: brightness(1.06); }
    .btn.red{ background: rgba(239,68,68,.18); border-color: rgba(239,68,68,.35); }
    .btn.red:hover{ filter: brightness(1.10); }

    .diceGrid{
      background: rgba(0,0,0,.22);
      border:1px solid var(--stroke);
      border-radius: var(--radius);
      padding:12px;
      display:grid;
      grid-template-columns: repeat(5, minmax(0, 1fr));
      gap:10px;
    }
    .dieCard{
      border-radius: 14px;
      padding:10px 10px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(255,255,255,.03);
      display:flex;
      flex-direction:column;
      gap:6px;
      align-items:center;
      justify-content:center;
      cursor:pointer;
      transition: transform .10s ease, filter .12s ease, background .12s ease, border-color .12s ease;
      user-select:none;
      position:relative;
      overflow:hidden;
      min-height:76px;
    }
    .dieCard:hover{ filter: brightness(1.06); transform: translateY(-1px); }
    .dieCard.active{
      border-color: rgba(255,106,0,.55);
      background: rgba(255,106,0,.10);
    }
    .dieIcon{
      width:32px; height:32px;
      border-radius:10px;
      background: rgba(0,0,0,.22);
      border:1px solid rgba(255,255,255,.10);
      display:grid;
      place-items:center;
      font-family:var(--mono);
      font-weight:1000;
      letter-spacing:.02em;
    }
    .dieLabel{ font-size:12px; color:var(--muted); font-weight:900; }

    /* Bigger roll section (≈3× presence) */
    .rollSection{
      border-radius: var(--radius);
      border:1px solid rgba(255,255,255,.12);
      background: linear-gradient(90deg, rgba(124,58,237,.16), rgba(255,106,0,.12));
      padding:18px;
      display:grid;
      grid-template-columns: 1fr;
      gap:14px;
      align-items:center;
    }
    .rollRow{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:14px;
      flex-wrap:wrap;
    }
    .rollBtnHuge{
      padding:18px 22px;
      border-radius: 18px;
      font-size:18px;
      font-weight:1100;
      letter-spacing:.10em;
      min-height:62px;
    }
    .readout{
      display:flex;
      align-items:center;
      gap:10px;
      flex-wrap:wrap;
      justify-content:flex-end;
      flex:1;
      min-width: 280px;
    }
    .chip{
      font-family: var(--mono);
      font-weight:1000;
      font-size:16px;
      padding:10px 12px;
      border-radius: 14px;
      border:1px solid rgba(255,255,255,.12);
      background: rgba(0,0,0,.22);
    }
    .chip.big{
      font-size:22px;
      padding:12px 14px;
      border-radius: 16px;
      min-width: 86px;
      text-align:center;
    }
    .chip.red{ background: rgba(239,68,68,.20); border-color: rgba(239,68,68,.35); }
    .chip.purple{ background: rgba(124,58,237,.18); border-color: rgba(124,58,237,.35); }

    /* Segmented control */
    .seg{
      display:inline-flex;
      border:1px solid rgba(255,255,255,.12);
      border-radius: 999px;
      overflow:hidden;
      box-shadow: var(--btnshadow);
      background: rgba(0,0,0,.26);
    }
    .seg button{
      appearance:none;
      border:0;
      margin:0;
      padding:8px 12px;
      font: 1000 12px var(--sans);
      letter-spacing:.02em;
      color: rgba(231,238,252,.78);
      background: transparent;
      cursor:pointer;
    }
    .seg button + button{ border-left:1px solid rgba(255,255,255,.10); }
    .seg button.active{
      background: rgba(255,106,0,.22);
      color: rgba(255,255,255,.92);
    }

    .right{ display:grid; grid-template-rows: auto 1fr auto; }
    .rightHeader{
      padding:12px 14px;
      border-bottom:1px solid var(--stroke);
      background: rgba(0,0,0,.18);
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
    }
    .rightHeader .titleRow{
      display:flex;
      align-items:baseline;
      gap:12px;
      flex-wrap:wrap;
    }
    .rightHeader .title{ font-weight:1100; letter-spacing:.02em; }
    .rightHeader .subtitle{ font-size:12px; color:var(--muted); margin-top:2px; }
    .headerResult{
      font-family: var(--mono);
      font-weight:1200;
      font-size:22px;
      letter-spacing:.02em;
      color: rgba(255,255,255,.92);
      text-shadow: 0 10px 30px rgba(0,0,0,.40);
    }

    .viewer{
      position:relative;
      min-height:340px;
      background: radial-gradient(650px 280px at 50% 40%, rgba(255,255,255,.06), transparent 60%),
                  linear-gradient(180deg, rgba(0,0,0,.12), rgba(0,0,0,.30));
    }
    canvas#gl{ width:100%; height:100%; display:block; cursor:pointer; }

    .history{
      padding:12px 14px;
      border-top:1px solid var(--stroke);
      background: rgba(0,0,0,.16);
      max-height:170px;
      overflow:auto;
    }
    .history h4{
      margin:0 0 8px 0;
      font-size:12px;
      color:var(--muted);
      letter-spacing:.03em;
      text-transform:uppercase;
    }
    .histItem{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      padding:8px 10px;
      border:1px solid rgba(255,255,255,.08);
      border-radius: 12px;
      background: rgba(255,255,255,.03);
      margin-bottom:8px;
    }
    .histItem .leftSide{ display:flex; flex-direction:column; gap:2px; min-width:0; }
    .histItem .expr,.histItem .rolls{
      font-family:var(--mono);
      font-size:12px;
      overflow:hidden;
      text-overflow:ellipsis;
      white-space:nowrap;
      max-width: 360px;
    }
    .histItem .expr{ color:rgba(231,238,252,.7); }
    .histItem .rolls{ color:rgba(231,238,252,.45); }
    .histItem .tot{
      font-family:var(--mono);
      font-weight:1100;
      font-size:16px;
      padding:6px 10px;
      border-radius: 12px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(0,0,0,.22);
      min-width:64px;
      text-align:center;
    }

    @media (max-width: 980px){
      .app{ grid-template-columns: 1fr; }
      .viewer{ min-height:300px; }
      .diceGrid{ grid-template-columns: repeat(4, minmax(0, 1fr)); }
      .readout{ justify-content:flex-start; }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="pageTitle">
      <h1>Dice Roller</h1>
      <div class="hint">Tip: click the die to roll</div>
    </div>

    <div class="app">
      <section class="panel left">
        <div class="topRow">
          <div class="counter">
            <div class="pill">
              <button class="btnSquare orange" id="countMinus" title="Decrease dice count">−</button>
              <div><label>Count</label><div class="value" id="countVal">1</div></div>
              <button class="btnSquare orange" id="countPlus" title="Increase dice count">+</button>
            </div>

            <div class="pill">
              <button class="btnSquare orange" id="modMinus" title="Decrease modifier">−</button>
              <div><label>Modifier</label><div class="value" id="modVal">+0</div></div>
              <button class="btnSquare orange" id="modPlus" title="Increase modifier">+</button>
            </div>

            <div class="pill" title="Animation speed">
              <label style="margin-right:4px;">Anim</label>
              <div class="seg" role="tablist" aria-label="Animation mode">
                <button id="animShort" class="active" data-mode="short" role="tab" aria-selected="true">Short</button>
                <button id="animInst"  class=""      data-mode="instant" role="tab" aria-selected="false">Instant</button>
              </div>
            </div>
          </div>
        </div>

        <div class="diceGrid" id="diceGrid"></div>

        <div class="rollSection">
          <div class="rollRow">
            <button class="btn red rollBtnHuge" id="rollBtn">ROLL</button>
            <div class="readout">
              <span class="chip purple" id="exprChip">1d20 + 0</span>
              <span class="chip big red" id="totalChip">—</span>
            </div>
          </div>
        </div>
      </section>

      <section class="panel right">
        <div class="rightHeader">
          <div>
            <div class="titleRow">
              <div class="title">Roll Result</div>
              <div class="headerResult" id="headerResult">—</div>
            </div>
            <div class="subtitle" id="rightSub">Pick a die and roll (or click the die).</div>
          </div>
          <div class="chip" id="dieChip">d20</div>
        </div>

        <div class="viewer">
          <canvas id="gl" title="Click the die to roll"></canvas>
        </div>

        <div class="history">
          <h4>History</h4>
          <div id="histList"></div>
        </div>
      </section>
    </div>
  </div>

  <script type="module">
    import * as THREE from "https://unpkg.com/three@0.160.0/build/three.module.js";

    const dice = [
      { key:"d2",  sides:2,   geom:"coin",  tint:0xffc857, numbered:true,  labels:[1,2] },
      { key:"d4",  sides:4,   geom:"tetra", tint:0xa855f7, numbered:true },
      { key:"d6",  sides:6,   geom:"box",   tint:0xe5e7eb, numbered:true,  labels:[1,2,3,4,5,6] },
      { key:"d8",  sides:8,   geom:"octa",  tint:0xef4444, numbered:true },

      // Remade
      { key:"d10", sides:10,  geom:"d10",   tint:0x60a5fa, numbered:true,  labels:[0,1,2,3,4,5,6,7,8,9] },
      { key:"d100",sides:100, geom:"d100pair",tint:0x22c55e, numbered:true },

      { key:"d12", sides:12,  geom:"d12",   tint:0x34d399, numbered:true,  labels:[1,2,3,4,5,6,7,8,9,10,11,12] },
      { key:"d20", sides:20,  geom:"icosa", tint:0xf59e0b, numbered:true },
      { key:"custom", sides:16, geom:"icosa", tint:0xff6a00, custom:true, numbered:false }
    ];

    let selected = dice.find(d => d.key === "d20");
    let count = 1;
    let mod = 0;

    let animMode = "short";
    const DURATIONS = { short: 1100, instant: 0 };

    let lastResultText = "";

    const diceGrid = document.getElementById("diceGrid");
    const countVal = document.getElementById("countVal");
    const modVal   = document.getElementById("modVal");
    const exprChip = document.getElementById("exprChip");
    const totalChip = document.getElementById("totalChip");
    const dieChip = document.getElementById("dieChip");
    const rightSub = document.getElementById("rightSub");
    const histList = document.getElementById("histList");
    const headerResult = document.getElementById("headerResult");

    document.getElementById("countMinus").onclick = () => setCount(count - 1);
    document.getElementById("countPlus").onclick  = () => setCount(count + 1);
    document.getElementById("modMinus").onclick   = () => setMod(mod - 1);
    document.getElementById("modPlus").onclick    = () => setMod(mod + 1);

    const animBtns = [ document.getElementById("animShort"), document.getElementById("animInst") ];
    animBtns.forEach(btn => {
      btn.onclick = () => {
        animMode = btn.dataset.mode;
        animBtns.forEach(b => {
          const active = b === btn;
          b.classList.toggle("active", active);
          b.setAttribute("aria-selected", active ? "true" : "false");
        });
      };
    });

    document.getElementById("rollBtn").onclick = () => roll();

    function renderDiceCards(){
      diceGrid.innerHTML = "";
      for(const d of dice){
        const card = document.createElement("div");
        card.className = "dieCard" + (d.key === selected.key ? " active" : "");
        const icon = document.createElement("div");
        icon.className = "dieIcon";
        icon.textContent = d.custom ? "★" : d.key.toUpperCase();
        const label = document.createElement("div");
        label.className = "dieLabel";
        label.textContent = d.custom ? "CUSTOM" : d.key.toUpperCase();
        card.append(icon, label);

        card.onclick = () => {
          if(d.custom){
            const v = prompt("Custom die sides (2–999):", String(selected.key==="custom"? selected.sides : 16));
            const n = Number(v);
            if(Number.isFinite(n) && n >= 2 && n <= 999) d.sides = Math.floor(n);
          }
          selected = d;
          renderDiceCards();
          syncUI();
          buildSceneDice();
        };

        diceGrid.appendChild(card);
      }
    }

    function setCount(n){ count = Math.max(1, Math.min(50, n)); syncUI(); }
    function setMod(n){ mod = Math.max(-999, Math.min(999, n)); syncUI(); }

    function syncUI(){
      countVal.textContent = String(count);
      modVal.textContent = (mod >= 0 ? `+${mod}` : `${mod}`);
      const expr = `${count}${selected.key} ${mod>=0?"+":"-"} ${Math.abs(mod)}`;
      exprChip.textContent = expr.replace("custom","d"+selected.sides);
      dieChip.textContent = selected.custom ? `d${selected.sides}` : selected.key;
    }

    function randInt(min, maxInclusive){
      const range = maxInclusive - min + 1;
      if(range <= 0) return min;
      if(window.crypto && crypto.getRandomValues){
        const maxUint = 0xFFFFFFFF;
        const bucket = Math.floor(maxUint / range) * range;
        const arr = new Uint32Array(1);
        let x;
        do{ crypto.getRandomValues(arr); x = arr[0]; }while(x >= bucket);
        return min + (x % range);
      }
      return min + Math.floor(Math.random()*range);
    }

    function pushHistory(expr, rollsText, total){
      const item = document.createElement("div");
      item.className = "histItem";
      const left = document.createElement("div");
      left.className = "leftSide";

      const e = document.createElement("div");
      e.className = "expr";
      e.textContent = expr;

      const r = document.createElement("div");
      r.className = "rolls";
      r.textContent = rollsText;

      left.append(e,r);

      const tot = document.createElement("div");
      tot.className = "tot";
      tot.textContent = String(total);

      item.append(left, tot);
      histList.prepend(item);

      const items = histList.querySelectorAll(".histItem");
      if(items.length > 12) items[items.length-1].remove();
    }

    // THREE
    const canvas = document.getElementById("gl");
    const renderer = new THREE.WebGLRenderer({ canvas, antialias:true, alpha:true });
    renderer.setPixelRatio(Math.min(2, window.devicePixelRatio || 1));

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(40, 1, 0.1, 100);
    camera.position.set(0, 0.95, 4.6);

    const LOOK_AT = new THREE.Vector3(0, 0.28, 0);

    // Matte lighting
    const lightA = new THREE.DirectionalLight(0xffffff, 0.78);
    lightA.position.set(4, 7, 4);
    scene.add(lightA);

    const lightB = new THREE.DirectionalLight(0xffffff, 0.28);
    lightB.position.set(-4, -2, 3);
    scene.add(lightB);

    scene.add(new THREE.AmbientLight(0xffffff, 0.48));

    const ground = new THREE.Mesh(
      new THREE.CircleGeometry(1.55, 64),
      new THREE.MeshLambertMaterial({ color:0xffffff, transparent:true, opacity:0.04 })
    );
    ground.rotation.x = -Math.PI/2;
    ground.position.y = -1.25;
    scene.add(ground);

    let rig = null;

    function makeMaterialBase(tintHex){
      // Opaque. No translucency.
      return new THREE.MeshLambertMaterial({ color: tintHex, transparent:false, opacity:1.0, side:THREE.FrontSide });
    }

    function disposeObj(obj){
      if(!obj) return;
      obj.traverse?.(n => {
        if(n.geometry) n.geometry.dispose?.();
        if(n.material){
          if(Array.isArray(n.material)){
            n.material.forEach(m => {
              if(m.map) m.map.dispose?.();
              m.dispose?.();
            });
          } else {
            if(n.material.map) n.material.map.dispose?.();
            n.material.dispose?.();
          }
        }
      });
    }

    // Big number texture (no box)
    function makeNumberTexture(label){
      const size = 512;
      const c = document.createElement("canvas");
      c.width = size; c.height = size;
      const ctx = c.getContext("2d");

      const s = String(label);
      const fontSize = s.length >= 2 ? 320 : 360;

      ctx.clearRect(0,0,size,size);
      ctx.font = `900 ${fontSize}px system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial`;
      ctx.textAlign = "center";
      ctx.textBaseline = "middle";

      ctx.fillStyle = "rgba(0,0,0,0.62)";
      ctx.fillText(s, size/2 + 10, size/2 + 18);

      ctx.fillStyle = "rgba(255,255,255,0.96)";
      ctx.fillText(s, size/2, size/2 + 8);

      const tex = new THREE.CanvasTexture(c);
      tex.anisotropy = 8;
      tex.colorSpace = THREE.SRGBColorSpace;
      tex.needsUpdate = true;
      return tex;
    }

    function makeDecalPlane(label, size){
      const tex = makeNumberTexture(label);
      const mat = new THREE.MeshBasicMaterial({
        map: tex,
        transparent:true,
        depthWrite:false,
        depthTest:true,
        opacity: 0.98,
        side: THREE.DoubleSide
      });
      const geom = new THREE.PlaneGeometry(size, size);
      return new THREE.Mesh(geom, mat);
    }

    const ZP = new THREE.Vector3(0,0,1);

    function outwardNormal(center, n){
      if(center.dot(n) < 0) n.multiplyScalar(-1);
      return n;
    }

    // ---------- D10 REMAKE (stable trapezohedron, auto-outward winding) ----------
    function makeD10GeometryRemade(){
      // vertices: top, bottom, 10 around ring (alternating height)
      const n = 5;
      const ringR = 0.92;
      const ringZ = 0.46;
      const poleZ = 1.18;

      const v = [];
      v.push(new THREE.Vector3(0,0,poleZ));   // 0 top
      v.push(new THREE.Vector3(0,0,-poleZ));  // 1 bottom

      for(let i=0;i<10;i++){
        const ang = i * (Math.PI/5);
        const z = (i%2===0) ? ringZ : -ringZ;
        v.push(new THREE.Vector3(Math.cos(ang)*ringR, Math.sin(ang)*ringR, z)); // 2..11
      }

      // helper to ensure triangle winding is outward
      const indices = [];
      function addTri(i0,i1,i2){
        const a = v[i0], b = v[i1], c = v[i2];
        const center = new THREE.Vector3().addVectors(a,b).add(c).multiplyScalar(1/3);
        const nrm = new THREE.Vector3().subVectors(b,a).cross(new THREE.Vector3().subVectors(c,a)).normalize();
        // outward means dot(center, normal) > 0 for this origin-centered poly
        if(center.dot(nrm) < 0){
          indices.push(i0,i2,i1);
        }else{
          indices.push(i0,i1,i2);
        }
      }

      const kiteFaces = [];
      const TOP = 0, BOT = 1;

      for(let i=0;i<10;i++){
        const a = 2 + i;
        const b = 2 + ((i+1)%10);

        // Each face is a kite: TOP-a-b and BOT-b-a
        addTri(TOP, a, b);
        addTri(BOT, b, a);

        kiteFaces.push({ verts:[TOP, a, BOT, b] });
      }

      const pos = new Float32Array(v.length*3);
      for(let i=0;i<v.length;i++){
        pos[i*3+0]=v[i].x;
        pos[i*3+1]=v[i].y;
        pos[i*3+2]=v[i].z;
      }

      const g = new THREE.BufferGeometry();
      g.setAttribute("position", new THREE.BufferAttribute(pos, 3));
      g.setIndex(indices);
      g.computeVertexNormals();
      g.computeBoundingSphere();
      g.userData.kiteFaces = kiteFaces;
      return g;
    }

    function makeD12Geometry(){
      const t = (1 + Math.sqrt(5)) / 2;
      const s = 1 / t;
      const V = [
        [-1, -1, -1], [-1, -1,  1], [-1,  1, -1], [-1,  1,  1],
        [ 1, -1, -1], [ 1, -1,  1], [ 1,  1, -1], [ 1,  1,  1],
        [ 0, -s, -t], [ 0, -s,  t], [ 0,  s, -t], [ 0,  s,  t],
        [-s, -t,  0], [-s,  t,  0], [ s, -t,  0], [ s,  t,  0],
        [-t,  0, -s], [ t,  0, -s], [-t,  0,  s], [ t,  0,  s],
      ];
      const F = [
        [0, 8,10, 2,16],
        [0,16,18, 1,12],
        [0,12,14, 4, 8],
        [8, 4,17, 6,10],
        [10,6,15,13, 2],
        [2,13, 3,18,16],
        [1,18, 3,11, 9],
        [1, 9, 5,14,12],
        [4,14, 5,19,17],
        [6,17,19, 7,15],
        [3,13,15, 7,11],
        [5, 9,11, 7,19],
      ];

      const scale = 0.85;
      const verts = [];
      for(const p of V) verts.push(p[0]*scale, p[1]*scale, p[2]*scale);

      const indices = [];
      const pentFaces = [];
      for(const face of F){
        const baseIndex = indices.length;
        indices.push(face[0], face[1], face[2]);
        indices.push(face[0], face[2], face[3]);
        indices.push(face[0], face[3], face[4]);
        pentFaces.push({ indexStart: baseIndex, verts: face.slice() });
      }

      const g = new THREE.BufferGeometry();
      g.setAttribute("position", new THREE.Float32BufferAttribute(verts, 3));
      g.setIndex(indices);
      g.computeVertexNormals();
      g.computeBoundingSphere();
      g.userData.pentFaces = pentFaces;
      return g;
    }

    function makeCoinGeometry(){
      const g = new THREE.CylinderGeometry(1.02, 1.02, 0.26, 48, 1, false);
      g.rotateX(Math.PI/2);
      return g;
    }

    function makeDieGeometry(kind){
      switch(kind){
        case "box":   return new THREE.BoxGeometry(1.25,1.25,1.25, 1,1,1);
        case "tetra": return new THREE.TetrahedronGeometry(1.15, 0);
        case "octa":  return new THREE.OctahedronGeometry(1.18, 0);
        case "icosa": return new THREE.IcosahedronGeometry(1.18, 0);
        case "d10":   return makeD10GeometryRemade();
        case "d12":   return makeD12Geometry();
        case "coin":  return makeCoinGeometry();
        default:      return new THREE.IcosahedronGeometry(1.18, 0);
      }
    }

    // --- D12 lines: only draw FRONT-facing boundary edges each frame (prevents seeing opposite-side edges) ---
    function buildD12EdgeIndex(geom){
      const pos = geom.attributes.position;
      const faces = geom.userData.pentFaces;

      const edgeSet = new Set();
      const edges = []; // [i0,i1] pairs unique

      function key(i,j){
        const lo = Math.min(i,j), hi = Math.max(i,j);
        return lo + "_" + hi;
      }

      for(const f of faces){
        const vs = f.verts;
        for(let k=0;k<vs.length;k++){
          const i0 = vs[k];
          const i1 = vs[(k+1)%vs.length];
          const kk = key(i0,i1);
          if(edgeSet.has(kk)) continue;
          edgeSet.add(kk);
          edges.push([i0,i1]);
        }
      }

      // also store vertex positions for quick normal-ish tests
      const v = [];
      for(let i=0;i<pos.count;i++){
        v.push(new THREE.Vector3().fromBufferAttribute(pos,i));
      }

      return { edges, v };
    }

    function makeD12FrontEdgesLine(geom){
      const cache = buildD12EdgeIndex(geom);

      const lineGeom = new THREE.BufferGeometry();
      const maxSeg = cache.edges.length;
      const buf = new Float32Array(maxSeg * 2 * 3);
      lineGeom.setAttribute("position", new THREE.BufferAttribute(buf, 3));
      lineGeom.setDrawRange(0, maxSeg*2);

      const mat = new THREE.LineBasicMaterial({
        color: 0xffffff,
        transparent: true,
        opacity: 0.32,
        depthTest: true,
        depthWrite: false
      });

      const line = new THREE.LineSegments(lineGeom, mat);
      line.frustumCulled = false;

      // updater: keep only edges whose midpoint normal points toward camera
      line.userData.update = (worldQuat) => {
        const viewDir = new THREE.Vector3(0,0,1);
        // camera looks down -Z in camera space; easiest: compute in world using camera position
        // We'll use: face "outward" approx = normalized midpoint (since centered poly)
        let w = 0;
        const camPos = camera.position;

        for(const [i0,i1] of cache.edges){
          const a = cache.v[i0];
          const b = cache.v[i1];
          const mid = new THREE.Vector3().addVectors(a,b).multiplyScalar(0.5);

          // approximate outward direction in LOCAL, rotate to world
          const outward = mid.clone().normalize().applyQuaternion(worldQuat);

          // toward camera?
          const toCam = new THREE.Vector3().subVectors(camPos, mid.clone().applyQuaternion(worldQuat)).normalize();
          const facing = outward.dot(toCam) > 0.05; // threshold

          if(!facing) continue;

          const aw = a.clone().applyQuaternion(worldQuat);
          const bw = b.clone().applyQuaternion(worldQuat);

          buf[w++] = aw.x; buf[w++] = aw.y; buf[w++] = aw.z;
          buf[w++] = bw.x; buf[w++] = bw.y; buf[w++] = bw.z;
        }

        lineGeom.attributes.position.needsUpdate = true;
        lineGeom.setDrawRange(0, (w/3));
      };

      return line;
    }

    function addDecalsTriangleFaces(geom, labels, parent, planeSize, offset){
      const non = geom.toNonIndexed();
      const pos = non.attributes.position;

      const A = new THREE.Vector3(), B = new THREE.Vector3(), C = new THREE.Vector3();
      const ab = new THREE.Vector3(), ac = new THREE.Vector3(), n = new THREE.Vector3(), center = new THREE.Vector3();

      const normals = [];
      const faceCount = pos.count / 3;

      for(let fi=0; fi<faceCount; fi++){
        const i = fi*3;
        A.fromBufferAttribute(pos, i);
        B.fromBufferAttribute(pos, i+1);
        C.fromBufferAttribute(pos, i+2);

        center.copy(A).add(B).add(C).multiplyScalar(1/3);

        ab.subVectors(B, A);
        ac.subVectors(C, A);
        n.crossVectors(ab, ac).normalize();
        outwardNormal(center, n);

        normals.push(n.clone());

        const label = labels?.[fi] ?? (fi+1);
        const plane = makeDecalPlane(label, planeSize);
        plane.position.copy(center).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }

      non.dispose?.();
      return normals;
    }

    function addDecalsBox(labels, parent, planeSize, offset){
      const normals = [
        new THREE.Vector3( 1, 0, 0),
        new THREE.Vector3(-1, 0, 0),
        new THREE.Vector3( 0, 1, 0),
        new THREE.Vector3( 0,-1, 0),
        new THREE.Vector3( 0, 0, 1),
        new THREE.Vector3( 0, 0,-1),
      ];
      const h = 1.25/2;
      const centers = [
        new THREE.Vector3( h,0,0),
        new THREE.Vector3(-h,0,0),
        new THREE.Vector3(0, h,0),
        new THREE.Vector3(0,-h,0),
        new THREE.Vector3(0,0, h),
        new THREE.Vector3(0,0,-h),
      ];
      for(let i=0;i<6;i++){
        const plane = makeDecalPlane(labels?.[i] ?? (i+1), planeSize);
        const n = normals[i].clone(), c = centers[i].clone();
        plane.position.copy(c).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function addDecalsCoin(labels, parent, planeSize, offset){
      const normals = [ new THREE.Vector3(0,0,1), new THREE.Vector3(0,0,-1) ];
      const centers = [ new THREE.Vector3(0,0, 0.13), new THREE.Vector3(0,0,-0.13) ];
      for(let i=0;i<2;i++){
        const plane = makeDecalPlane(labels?.[i] ?? (i+1), planeSize);
        const n = normals[i].clone(), c = centers[i].clone();
        plane.position.copy(c).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function addDecalsD10(geom, labels, parent, planeSize, offset){
      const pos = geom.attributes.position;
      const tmp = new THREE.Vector3();
      const u = new THREE.Vector3(), v = new THREE.Vector3(), n = new THREE.Vector3(), center = new THREE.Vector3();
      const p0 = new THREE.Vector3(), p1 = new THREE.Vector3(), p3 = new THREE.Vector3();

      const normals = [];
      for(let fi=0; fi<10; fi++){
        const face = geom.userData.kiteFaces[fi];

        center.set(0,0,0);
        for(const vi of face.verts){
          tmp.fromBufferAttribute(pos, vi);
          center.add(tmp);
        }
        center.multiplyScalar(1/4);

        p0.fromBufferAttribute(pos, face.verts[0]);
        p1.fromBufferAttribute(pos, face.verts[1]);
        p3.fromBufferAttribute(pos, face.verts[3]);

        u.subVectors(p1, p0);
        v.subVectors(p3, p0);
        n.crossVectors(u, v).normalize();
        outwardNormal(center, n);

        normals.push(n.clone());

        const plane = makeDecalPlane(labels?.[fi] ?? fi, planeSize);
        plane.position.copy(center).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function addDecalsD12(geom, labels, parent, planeSize, offset){
      const pos = geom.attributes.position;
      const idx = geom.index;

      const tmp = new THREE.Vector3();
      const a = new THREE.Vector3(), b = new THREE.Vector3(), c = new THREE.Vector3();
      const ab = new THREE.Vector3(), ac = new THREE.Vector3(), n = new THREE.Vector3(), center = new THREE.Vector3();

      const normals = [];
      for(let fi=0; fi<12; fi++){
        const face = geom.userData.pentFaces[fi];
        const i0 = idx.getX(face.indexStart + 0);
        const i1 = idx.getX(face.indexStart + 1);
        const i2 = idx.getX(face.indexStart + 2);

        a.fromBufferAttribute(pos, i0);
        b.fromBufferAttribute(pos, i1);
        c.fromBufferAttribute(pos, i2);

        ab.subVectors(b,a);
        ac.subVectors(c,a);
        n.crossVectors(ab, ac).normalize();

        center.set(0,0,0);
        for(const vi of face.verts){
          tmp.fromBufferAttribute(pos, vi);
          center.add(tmp);
        }
        center.multiplyScalar(1/5);

        outwardNormal(center, n);
        normals.push(n.clone());

        const plane = makeDecalPlane(labels?.[fi] ?? (fi+1), planeSize);
        plane.position.copy(center).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function viewDirFor(part){
      return new THREE.Vector3().subVectors(camera.position, part.body.position).normalize();
    }

    function targetQuaternionFaceToCamera(faceIndex, normals, part){
      const idx = Math.max(0, Math.min(normals.length-1, faceIndex));
      const n = normals[idx].clone().normalize();
      const vdir = viewDirFor(part);
      const qAlign = new THREE.Quaternion().setFromUnitVectors(n, vdir);

      const spin = (Math.random()*2 - 1) * 0.55;
      const qSpin = new THREE.Quaternion().setFromAxisAngle(vdir, spin);
      return qSpin.multiply(qAlign);
    }

    function buildSingleDieRig(def, xOffset=0, tintOverride=null, labelsOverride=null){
      const geom = makeDieGeometry(def.geom);

      const body = new THREE.Mesh(geom, makeMaterialBase(tintOverride ?? def.tint));
      // slight polygon offset to avoid any z-fighting with decals/lines
      body.material.polygonOffset = true;
      body.material.polygonOffsetFactor = 1;
      body.material.polygonOffsetUnits = 1;

      let d12Line = null;

      if(def.geom === "d12"){
        // Special: front-facing edges only, updated every frame
        d12Line = makeD12FrontEdgesLine(geom);
        body.add(d12Line);
      }else{
        const edges = new THREE.LineSegments(
          new THREE.EdgesGeometry(geom, 8),
          new THREE.LineBasicMaterial({ color: 0xffffff, transparent:true, opacity: 0.24, depthTest:true, depthWrite:false })
        );
        body.add(edges);
      }

      const decals = new THREE.Group();
      const home = { x: xOffset, y: 0.22, z: 0 };

      body.position.set(home.x, home.y, home.z);
      body.rotation.set(-0.45, 0.85, 0.20);
      decals.position.copy(body.position);

      let normals = null;
      if(def.numbered && !def.custom){
        const labels = labelsOverride ?? def.labels ?? Array.from({length:def.sides}, (_,i)=> i+1);

        if(def.geom === "box"){
          normals = addDecalsBox(labels, decals, 0.92, 0.06);
        } else if(def.geom === "coin"){
          normals = addDecalsCoin(labels, decals, 1.10, 0.06);
        } else if(def.geom === "d10"){
          normals = addDecalsD10(geom, labels, decals, 0.78, 0.055);
        } else if(def.geom === "d12"){
          normals = addDecalsD12(geom, labels, decals, 0.88, 0.06);
        } else {
          normals = addDecalsTriangleFaces(geom, labels, decals, 0.78, 0.055);
        }
      }

      const phys = { angVel: new THREE.Vector3() };
      return { body, decals, normals, def, phys, home, d12Line };
    }

    function clearRig(){
      if(!rig) return;
      for(const part of rig.parts){
        scene.remove(part.body);
        scene.remove(part.decals);
        disposeObj(part.body);
        disposeObj(part.decals);
      }
      rig = null;
    }

    function buildSceneDice(){
      clearRig();

      if(selected.key === "d100"){
        const d10Def = dice.find(d => d.key === "d10");
        const onesLabels = [0,1,2,3,4,5,6,7,8,9];
        const tensLabels = ["00","10","20","30","40","50","60","70","80","90"];

        const left  = buildSingleDieRig({ ...d10Def, geom:"d10", numbered:true, custom:false }, -0.95, selected.tint, onesLabels);
        const right = buildSingleDieRig({ ...d10Def, geom:"d10", numbered:true, custom:false },  0.95, selected.tint, tensLabels);

        scene.add(left.body);  scene.add(left.decals);
        scene.add(right.body); scene.add(right.decals);

        rig = { parts:[left, right], rolling:false };
      } else {
        const single = buildSingleDieRig(selected, 0, null, null);
        scene.add(single.body);
        scene.add(single.decals);
        rig = { parts:[single], rolling:false };
      }
    }

    function resize(){
      const rect = canvas.getBoundingClientRect();
      const w = Math.max(1, Math.floor(rect.width));
      const h = Math.max(1, Math.floor(rect.height));
      renderer.setSize(w, h, false);
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
    }
    window.addEventListener("resize", resize);

    canvas.addEventListener("pointerdown", () => {
      if(rig?.rolling) return;
      roll();
    });

    function pop(part){
      const start = performance.now();
      const base = part.body.scale.clone();
      const dur = 220;
      function step(){
        const p = (performance.now() - start) / dur;
        const k = p < 1 ? (1 - Math.pow(1-p, 3)) : 1;
        const s = 1 + Math.sin(k*Math.PI) * 0.075;
        part.body.scale.set(base.x*s, base.y*s, base.z*s);
        part.decals.scale.copy(part.body.scale);
        if(p < 1) requestAnimationFrame(step);
        else {
          part.body.scale.copy(base);
          part.decals.scale.copy(base);
        }
      }
      step();
    }

    function landPartInstant(part, faceIndex){
      if(!part.normals) return;
      const q = targetQuaternionFaceToCamera(faceIndex, part.normals, part);
      part.body.position.set(part.home.x, part.home.y, part.home.z);
      part.decals.position.copy(part.body.position);
      part.body.quaternion.copy(q);
      part.decals.quaternion.copy(q);
      pop(part);
    }

    function randSigned(){ return (Math.random()*2 - 1); }

    async function animatePartPhysicsRoll(part, faceIndex, durationMs){
      if(!part.normals){
        pop(part);
        return;
      }

      const targetQ = targetQuaternionFaceToCamera(faceIndex, part.normals, part);
      const angVel = part.phys.angVel;
      angVel.set(randSigned()*11.5, randSigned()*13.5, randSigned()*10.5);

      const lin = {
        x: part.home.x,
        z: part.home.z,
        y: 1.55,
        vx: randSigned() * 0.80,
        vz: randSigned() * 0.75,
        vy: 2.25
      };

      const start = performance.now();
      const end = start + durationMs;

      const damp = 0.972;
      const settleStart = 0.62;
      const groundY = part.home.y;

      await new Promise(resolve => {
        let prev = performance.now();
        function step(now){
          const dt = Math.min(0.033, (now - prev) / 1000);
          prev = now;

          const t = (now - start) / durationMs;
          const tClamped = Math.max(0, Math.min(1, t));

          // rotation integrate
          const omega = angVel.clone();
          const angle = omega.length() * dt;
          if(angle > 1e-6){
            const axis = omega.normalize();
            const dq = new THREE.Quaternion().setFromAxisAngle(axis, angle);
            part.body.quaternion.multiply(dq);
            part.decals.quaternion.copy(part.body.quaternion);
          }

          angVel.multiplyScalar(Math.pow(damp, dt*60));

          // gravity + integrate
          lin.vy -= 9.8 * dt;
          lin.x += lin.vx * dt;
          lin.z += lin.vz * dt;
          lin.y += lin.vy * dt;

          // bounce & friction
          if(lin.y < groundY){
            lin.y = groundY;
            lin.vy = Math.abs(lin.vy) * 0.42;

            const friction = 0.83;
            lin.vx *= Math.pow(friction, dt*60);
            lin.vz *= Math.pow(friction, dt*60);

            angVel.addScaledVector(new THREE.Vector3(randSigned(), randSigned(), randSigned()).normalize(), 0.9);
            if(Math.abs(lin.vy) < 0.35) lin.vy = 0;
          }

          // keep motion near home
          const clamp = 0.55;
          lin.x = part.home.x + Math.max(-clamp, Math.min(clamp, lin.x - part.home.x));
          lin.z = part.home.z + Math.max(-clamp, Math.min(clamp, lin.z - part.home.z));

          part.body.position.set(lin.x, lin.y, lin.z);
          part.decals.position.copy(part.body.position);

          // late steering
          if(tClamped > settleStart){
            const s = (tClamped - settleStart) / (1 - settleStart);
            const steer = s*s*(3-2*s);

            part.body.quaternion.slerp(targetQ, 0.05 + steer*0.24);
            part.decals.quaternion.copy(part.body.quaternion);

            angVel.multiplyScalar(1 - steer*0.32);

            // pull toward center
            lin.x += (part.home.x - lin.x) * (0.10 * steer);
            lin.z += (part.home.z - lin.z) * (0.10 * steer);
          }

          if(now < end){
            requestAnimationFrame(step);
          }else{
            part.body.position.set(part.home.x, part.home.y, part.home.z);
            part.decals.position.copy(part.body.position);

            part.body.quaternion.slerp(targetQ, 0.90);
            part.body.quaternion.copy(targetQ);
            part.decals.quaternion.copy(part.body.quaternion);

            pop(part);
            resolve();
          }
        }
        requestAnimationFrame(step);
      });
    }

    async function roll(){
      const expr = exprChip.textContent;
      const durationMs = DURATIONS[animMode] ?? 1100;

      if(rig?.rolling) return;

      if(selected.key === "d100"){
        const results = [];
        for(let i=0;i<count;i++){
          const ones = randInt(0,9);
          const tens = randInt(0,9);
          const val0_99 = tens*10 + ones;
          const val = (val0_99 === 0) ? 100 : val0_99;
          results.push(val);
        }

        const sum = results.reduce((a,b)=>a+b,0);
        const total = sum + mod;

        totalChip.textContent = String(total);
        headerResult.textContent = String(total);
        lastResultText = `${expr} = ${total} (percentiles: ${results.join(", ")})`;
        pushHistory(expr, `percentiles: ${results.join(", ")}`, total);

        rightSub.textContent = durationMs === 0 ? "Instant roll." : "Rolling…";

        const last = results[results.length-1];
        const ones = (last === 100) ? 0 : (last % 10);
        const tens = (last === 100) ? 0 : Math.floor(last / 10);

        if(durationMs === 0){
          landPartInstant(rig.parts[0], ones);
          landPartInstant(rig.parts[1], tens);
        }else{
          rig.rolling = true;
          await Promise.all([
            animatePartPhysicsRoll(rig.parts[0], ones, durationMs),
            animatePartPhysicsRoll(rig.parts[1], tens, durationMs),
          ]);
          rig.rolling = false;
        }

        rightSub.textContent = "Ready.";
        return;
      }

      const sides = selected.custom ? selected.sides : selected.sides;
      const rolls = Array.from({length: count}, () => randInt(1, sides));
      const sum = rolls.reduce((a,b)=>a+b,0);
      const total = sum + mod;

      totalChip.textContent = String(total);
      headerResult.textContent = String(total);
      lastResultText = `${expr} = ${total} (rolls: ${rolls.join(", ")})`;
      pushHistory(expr, `rolls: ${rolls.join(", ")}`, total);

      const lastRoll = rolls[rolls.length - 1];
      rightSub.textContent = durationMs === 0 ? "Instant roll." : "Rolling…";

      const part = rig.parts[0];

      let faceIndex;
      if(selected.key === "d10"){
        const v = (lastRoll === 10) ? 0 : lastRoll;
        faceIndex = v;
      }else{
        faceIndex = lastRoll - 1;
      }

      if(durationMs === 0){
        landPartInstant(part, faceIndex);
      }else{
        rig.rolling = true;
        await animatePartPhysicsRoll(part, faceIndex, durationMs);
        rig.rolling = false;
      }

      rightSub.textContent = "Ready.";
    }

    function tick(){
      camera.lookAt(LOOK_AT);

      if(rig){
        for(const part of rig.parts){
          // keep centered when idle
          if(!rig.rolling){
            part.body.position.set(part.home.x, part.home.y, part.home.z);
            part.decals.position.copy(part.body.position);

            const dt = 0.016;
            part.body.rotation.y += dt * 0.26;
            part.body.rotation.x += dt * 0.08;
            part.decals.rotation.copy(part.body.rotation);
          }

          // Update D12 front-edge lines each frame
          if(part.d12Line?.userData?.update){
            part.d12Line.userData.update(part.body.quaternion);
          }
        }
      }

      renderer.render(scene, camera);
      requestAnimationFrame(tick);
    }

    // init
    renderDiceCards();
    syncUI();
    buildSceneDice();
    resize();
    requestAnimationFrame(tick);
  </script>
</body>
</html>
