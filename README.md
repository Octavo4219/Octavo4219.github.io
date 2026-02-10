<!doctype html>
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
    .app{
      width:min(1120px, 96vw);
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
      grid-template-rows:auto auto 1fr auto;
      gap:12px;
    }
    .topRow{
      display:flex;
      gap:10px;
      align-items:center;
      justify-content:space-between;
      flex-wrap:wrap;
    }
    .counter{ display:flex; gap:10px; align-items:center; }
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
      border-radius: 12px;
      padding:10px 12px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(0,0,0,.25);
      color:var(--text);
      cursor:pointer;
      box-shadow: var(--btnshadow);
      transition: transform .08s ease, filter .12s ease, background .12s ease;
      font-weight:700;
      letter-spacing:.02em;
      user-select:none;
      display:inline-flex;
      align-items:center;
      gap:8px;
      min-height:40px;
    }
    .btn:active{ transform: translateY(1px) scale(.99); }
    .btn.green{ background: rgba(34,197,94,.18); border-color: rgba(34,197,94,.35); }
    .btn.green:hover{ filter: brightness(1.08); }
    .btn.red{ background: rgba(239,68,68,.18); border-color: rgba(239,68,68,.35); }
    .btn.red:hover{ filter: brightness(1.08); }

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
      font-weight:800;
      letter-spacing:.02em;
    }
    .dieLabel{ font-size:12px; color:var(--muted); font-weight:700; }

    .rollBar{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      flex-wrap:wrap;
      padding:10px 12px;
      border-radius: var(--radius);
      background: linear-gradient(90deg, rgba(124,58,237,.16), rgba(255,106,0,.12));
      border:1px solid rgba(255,255,255,.10);
    }
    .resultStrip{ display:flex; align-items:center; gap:6px; flex-wrap:wrap; }

    .chip{
      font-family: var(--mono);
      font-weight:800;
      font-size:13px;
      padding:6px 8px;
      border-radius: 10px;
      border:1px solid rgba(255,255,255,.10);
      background: rgba(0,0,0,.22);
    }
    .chip.big{ font-size:16px; padding:7px 10px; }
    .chip.red{ background: rgba(239,68,68,.20); border-color: rgba(239,68,68,.35); }
    .chip.purple{ background: rgba(124,58,237,.18); border-color: rgba(124,58,237,.35); }

    .toggleRow{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:12px;
      flex-wrap:wrap;
      padding:10px 12px;
      border-radius: var(--radius);
      border: 1px solid var(--stroke);
      background: rgba(0,0,0,.18);
      color: var(--muted);
      font-size:12px;
    }
    .toggle{ display:flex; align-items:center; gap:10px; font-weight:700; letter-spacing:.02em; }
    .switch{
      width:54px;height:30px;
      border-radius:999px;
      border:1px solid rgba(255,255,255,.12);
      background: rgba(0,0,0,.30);
      position:relative;
      cursor:pointer;
      box-shadow: var(--btnshadow);
    }
    .knob{
      position:absolute;
      top:3px; left:3px;
      width:24px;height:24px;
      border-radius:999px;
      background: rgba(255,255,255,.85);
      transition: left .16s ease, background .16s ease;
    }
    .switch.on{
      background: rgba(255,106,0,.20);
      border-color: rgba(255,106,0,.35);
    }
    .switch.on .knob{ left:27px; background: rgba(255,255,255,.92); }

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
    .rightHeader .title{ font-weight:900; letter-spacing:.02em; }
    .rightHeader .subtitle{ font-size:12px; color:var(--muted); margin-top:2px; }

    .viewer{
      position:relative;
      min-height:340px;
      background: radial-gradient(650px 280px at 50% 40%, rgba(255,255,255,.06), transparent 60%),
                  linear-gradient(180deg, rgba(0,0,0,.12), rgba(0,0,0,.30));
    }
    canvas#gl{ width:100%; height:100%; display:block; }

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
      font-weight:1000;
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
    }
  </style>
</head>
<body>
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
        </div>

        <div style="display:flex; gap:10px; align-items:center;">
          <button class="btn green" id="resetBtn" title="Reset">RESET</button>
        </div>
      </div>

      <div class="diceGrid" id="diceGrid"></div>

      <div class="rollBar">
        <div style="display:flex; align-items:center; gap:10px; flex-wrap:wrap;">
          <button class="btn red" id="rollBtn">ROLL</button>
          <div class="resultStrip">
            <span class="chip purple" id="exprChip">1d20 + 0</span>
            <span class="chip big red" id="totalChip">—</span>
          </div>
        </div>
        <button class="btn" id="copyBtn" title="Copy last result to clipboard">Copy</button>
      </div>

      <div class="toggleRow">
        <div>Toggle to skip animation.</div>
        <div class="toggle">
          <div class="switch on" id="animSwitch" aria-label="Animation toggle" role="switch" aria-checked="true"><div class="knob"></div></div>
          <div>ANIMATION <span style="opacity:.55;">/ INSTANT ROLL</span></div>
        </div>
      </div>
    </section>

    <section class="panel right">
      <div class="rightHeader">
        <div>
          <div class="title">Roll Result</div>
          <div class="subtitle" id="rightSub">Pick a die and roll.</div>
        </div>
        <div class="chip" id="dieChip">d20</div>
      </div>

      <div class="viewer">
        <canvas id="gl"></canvas>
      </div>

      <div class="history">
        <h4>History</h4>
        <div id="histList"></div>
      </div>
    </section>
  </div>

  <script type="module">
    import * as THREE from "https://unpkg.com/three@0.160.0/build/three.module.js";

    // ---------------------------
    // Dice definitions
    // ---------------------------
    const dice = [
      { key:"d2",  sides:2,   geom:"coin",    tint:0xffc857, numbered:true,  labels:[1,2] },
      { key:"d4",  sides:4,   geom:"tetra",   tint:0xa855f7, numbered:true },
      { key:"d6",  sides:6,   geom:"box",     tint:0xe5e7eb, numbered:true,  // common pip mapping isn't required; just 1..6
        labels:[1,2,3,4,5,6]
      },
      { key:"d8",  sides:8,   geom:"octa",    tint:0xef4444, numbered:true },
      { key:"d10", sides:10,  geom:"d10",     tint:0x60a5fa, numbered:true,  labels:[0,1,2,3,4,5,6,7,8,9] },
      // d100 is TWO d10s in the viewer: ones (0-9) + tens (00-90)
      { key:"d100",sides:100, geom:"d100pair",tint:0x22c55e, numbered:true },
      { key:"d12", sides:12,  geom:"d12",     tint:0x34d399, numbered:true,  labels:[1,2,3,4,5,6,7,8,9,10,11,12] },
      { key:"d20", sides:20,  geom:"icosa",   tint:0xf59e0b, numbered:true },
      { key:"custom", sides:16, geom:"icosa", tint:0xff6a00, custom:true, numbered:false }
    ];

    let selected = dice.find(d => d.key === "d20");
    let count = 1;
    let mod = 0;
    let animateRoll = true;
    let lastResultText = "";

    // ---------------------------
    // UI
    // ---------------------------
    const diceGrid = document.getElementById("diceGrid");
    const countVal = document.getElementById("countVal");
    const modVal   = document.getElementById("modVal");
    const exprChip = document.getElementById("exprChip");
    const totalChip = document.getElementById("totalChip");
    const dieChip = document.getElementById("dieChip");
    const rightSub = document.getElementById("rightSub");
    const histList = document.getElementById("histList");

    document.getElementById("countMinus").onclick = () => setCount(count - 1);
    document.getElementById("countPlus").onclick  = () => setCount(count + 1);
    document.getElementById("modMinus").onclick   = () => setMod(mod - 1);
    document.getElementById("modPlus").onclick    = () => setMod(mod + 1);

    const animSwitch = document.getElementById("animSwitch");
    animSwitch.onclick = () => {
      animateRoll = !animateRoll;
      animSwitch.classList.toggle("on", animateRoll);
      animSwitch.setAttribute("aria-checked", String(animateRoll));
    };

    document.getElementById("resetBtn").onclick = () => {
      selected = dice.find(d => d.key === "d20");
      count = 1; mod = 0; animateRoll = true;
      animSwitch.classList.add("on");
      animSwitch.setAttribute("aria-checked","true");
      totalChip.textContent = "—";
      rightSub.textContent = "Pick a die and roll.";
      lastResultText = "";
      renderDiceCards();
      syncUI();
      buildSceneDice();
    };

    document.getElementById("copyBtn").onclick = async () => {
      if(!lastResultText) return;
      try{
        await navigator.clipboard.writeText(lastResultText);
        rightSub.textContent = "Copied to clipboard.";
        setTimeout(()=> rightSub.textContent = "Ready.", 900);
      }catch{
        rightSub.textContent = "Clipboard blocked by browser.";
        setTimeout(()=> rightSub.textContent = "Ready.", 1200);
      }
    };

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
            if(Number.isFinite(n) && n >= 2 && n <= 999){
              d.sides = Math.floor(n);
            }
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

    // ---------------------------
    // RNG
    // ---------------------------
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

    // ---------------------------
    // History UI
    // ---------------------------
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

    // ---------------------------
    // THREE.js scene
    // ---------------------------
    const canvas = document.getElementById("gl");
    const renderer = new THREE.WebGLRenderer({ canvas, antialias:true, alpha:true });
    renderer.setPixelRatio(Math.min(2, window.devicePixelRatio || 1));

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(40, 1, 0.1, 100);
    camera.position.set(0, 0.95, 4.6);

    const LOOK_AT = new THREE.Vector3(0, 0.28, 0);

    const lightA = new THREE.DirectionalLight(0xffffff, 1.05);
    lightA.position.set(4, 7, 4);
    scene.add(lightA);

    const lightB = new THREE.DirectionalLight(0xffffff, 0.45);
    lightB.position.set(-4, -2, 3);
    scene.add(lightB);

    scene.add(new THREE.AmbientLight(0xffffff, 0.25));

    const ground = new THREE.Mesh(
      new THREE.CircleGeometry(1.55, 64),
      new THREE.MeshStandardMaterial({ color:0xffffff, roughness:1, metalness:0, transparent:true, opacity:0.05 })
    );
    ground.rotation.x = -Math.PI/2;
    ground.position.y = -1.25;
    scene.add(ground);

    // We support either one die (most) or a pair (d100).
    let rig = null;

    function makeMaterialBase(tintHex){
      return new THREE.MeshStandardMaterial({
        color: tintHex,
        roughness: 0.28,
        metalness: 0.22,
        emissive: new THREE.Color(tintHex).multiplyScalar(0.08),
      });
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

    // ---- Number textures: NO BOX, big number, face-filling
    function makeNumberTexture(label){
      const size = 512;
      const c = document.createElement("canvas");
      c.width = size; c.height = size;
      const ctx = c.getContext("2d");

      const s = String(label);

      // big font; adjust for 2 digits
      const fontSize = s.length >= 2 ? 320 : 360;

      // soft shadow for readability, no plate
      ctx.clearRect(0,0,size,size);
      ctx.font = `900 ${fontSize}px system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial`;
      ctx.textAlign = "center";
      ctx.textBaseline = "middle";

      // shadow
      ctx.fillStyle = "rgba(0,0,0,0.55)";
      ctx.fillText(s, size/2 + 10, size/2 + 18);

      // main
      ctx.fillStyle = "rgba(255,255,255,0.95)";
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
        opacity: 0.98
      });
      const geom = new THREE.PlaneGeometry(size, size);
      return new THREE.Mesh(geom, mat);
    }

    // ---------------------------
    // Geometries
    // ---------------------------
    function makeD10Geometry(){
      const h = 1.12;
      const r = 0.86;

      const verts = [];
      verts.push(0, h, 0);   // 0 top
      verts.push(0,-h, 0);   // 1 bottom

      for(let i=0;i<10;i++){
        const ang = (i/10) * Math.PI * 2;
        const y = (i%2===0) ? 0.42 : -0.42;
        verts.push(Math.cos(ang)*r, y, Math.sin(ang)*r);
      }

      const indices = [];
      const kiteFaces = [];
      const v = (i)=> 2+i;

      for(let k=0;k<10;k++){
        const a = v(k);
        const b = v((k+1)%10);
        const useTop = (k%2===0);
        const pole = useTop ? 0 : 1;
        const otherPole = useTop ? 1 : 0;

        const p0 = pole, p1 = a, p2 = otherPole, p3 = b;
        const i0 = [p0,p1,p3];
        const i1 = [p2,p3,p1];
        const baseIndex = indices.length;
        indices.push(...i0, ...i1);
        kiteFaces.push({ triIndexStart: baseIndex, verts:[p0,p1,p2,p3] });
      }

      const g = new THREE.BufferGeometry();
      g.setAttribute("position", new THREE.Float32BufferAttribute(verts, 3));
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
      for(const p of V){
        verts.push(p[0]*scale, p[1]*scale, p[2]*scale);
      }

      const indices = [];
      const pentFaces = [];
      for(let f=0; f<F.length; f++){
        const face = F[f];
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
      g.rotateX(Math.PI/2); // axis points along Z
      return g;
    }

    function makeDieGeometry(kind){
      switch(kind){
        case "box":   return new THREE.BoxGeometry(1.25,1.25,1.25, 1,1,1);
        case "tetra": return new THREE.TetrahedronGeometry(1.15, 0);
        case "octa":  return new THREE.OctahedronGeometry(1.18, 0);
        case "icosa": return new THREE.IcosahedronGeometry(1.18, 0);
        case "d10":   return makeD10Geometry();
        case "d12":   return makeD12Geometry();
        case "coin":  return makeCoinGeometry();
        default:      return new THREE.IcosahedronGeometry(1.18, 0);
      }
    }

    // ---------------------------
    // Decals and landing normals
    // ---------------------------
    const UP = new THREE.Vector3(0,1,0);
    const ZP = new THREE.Vector3(0,0,1);

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

    function addDecalsBox(geom, labels, parent, planeSize, offset){
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
        const n = normals[i], c = centers[i];
        plane.position.copy(c).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function addDecalsD10(geom, labels, parent, planeSize, offset){
      const pos = geom.attributes.position;
      const idx = geom.index;
      const tmp = new THREE.Vector3();
      const a = new THREE.Vector3(), b = new THREE.Vector3(), c = new THREE.Vector3();
      const ab = new THREE.Vector3(), ac = new THREE.Vector3(), n = new THREE.Vector3(), center = new THREE.Vector3();

      const normals = [];
      for(let fi=0; fi<10; fi++){
        const face = geom.userData.kiteFaces[fi];
        const i0 = idx.getX(face.triIndexStart + 0);
        const i1 = idx.getX(face.triIndexStart + 1);
        const i2 = idx.getX(face.triIndexStart + 2);

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
        center.multiplyScalar(1/face.verts.length);

        normals.push(n.clone());

        const plane = makeDecalPlane(labels?.[fi] ?? (fi), planeSize);
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
        center.multiplyScalar(1/face.verts.length);

        normals.push(n.clone());

        const plane = makeDecalPlane(labels?.[fi] ?? (fi+1), planeSize);
        plane.position.copy(center).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function addDecalsCoin(geom, labels, parent, planeSize, offset){
      // coin axis is Z after rotation; faces are +Z and -Z
      const normals = [ new THREE.Vector3(0,0,1), new THREE.Vector3(0,0,-1) ];
      const centers = [ new THREE.Vector3(0,0, 0.13), new THREE.Vector3(0,0,-0.13) ];
      for(let i=0;i<2;i++){
        const plane = makeDecalPlane(labels?.[i] ?? (i+1), planeSize);
        const n = normals[i], c = centers[i];
        plane.position.copy(c).addScaledVector(n, offset);
        plane.quaternion.setFromUnitVectors(ZP, n);
        parent.add(plane);
      }
      return normals;
    }

    function targetQuaternionFromNormals(value, normals, faceCount){
      const idx = Math.max(0, Math.min(faceCount-1, value));
      const n = normals[idx].clone().normalize();
      const qAlign = new THREE.Quaternion().setFromUnitVectors(n, UP);
      const yaw = (Math.random()*2 - 1) * Math.PI;
      const qYaw = new THREE.Quaternion().setFromAxisAngle(UP, yaw);
      return qYaw.multiply(qAlign);
    }

    // ---------------------------
    // Build rig: single die OR d100 pair
    // ---------------------------
    function buildSingleDieRig(def, xOffset=0, tintOverride=null, labelsOverride=null){
      const geom = makeDieGeometry(def.geom);
      const body = new THREE.Mesh(geom, makeMaterialBase(tintOverride ?? def.tint));

      const edges = new THREE.LineSegments(
        new THREE.EdgesGeometry(geom, 18),
        new THREE.LineBasicMaterial({ color: 0xffffff, transparent:true, opacity: 0.18 })
      );
      body.add(edges);

      const decals = new THREE.Group();

      // Place die in center region
      body.position.set(xOffset, 0.22, 0);
      body.rotation.set(-0.45, 0.85, 0.20);
      decals.position.copy(body.position);

      // Decals & normals
      let normals = null;
      if(def.numbered && !def.custom){
        const labels = labelsOverride ?? def.labels ?? Array.from({length:def.sides}, (_,i)=> i+1);

        if(def.geom === "box"){
          normals = addDecalsBox(geom, labels, decals, 0.92, 0.06);
        } else if(def.geom === "coin"){
          normals = addDecalsCoin(geom, labels, decals, 1.10, 0.06);
        } else if(def.geom === "d10"){
          normals = addDecalsD10(geom, labels, decals, 0.78, 0.055);
        } else if(def.geom === "d12"){
          normals = addDecalsD12(geom, labels, decals, 0.78, 0.06);
        } else {
          normals = addDecalsTriangleFaces(geom, labels, decals, 0.70, 0.055);
        }
      }

      return { body, decals, normals, geomKind: def.geom };
    }

    function clearRig(){
      if(!rig) return;
      for(const part of rig.parts){
        scene.remove(part.body);
        scene.remove(part.decals);
        disposeObj(part.body);
        disposeObj(part.decals);
      }
      if(rig.text){
        scene.remove(rig.text);
        disposeObj(rig.text);
      }
      rig = null;
    }

    function buildSceneDice(){
      clearRig();

      if(selected.key === "d100"){
        // Two d10s: ones 0-9 and tens 00-90
        const d10Def = dice.find(d => d.key === "d10");

        const onesLabels = [0,1,2,3,4,5,6,7,8,9];
        const tensLabels = ["00","10","20","30","40","50","60","70","80","90"];

        const left = buildSingleDieRig({ ...d10Def, geom:"d10", numbered:true, custom:false }, -0.95, selected.tint, onesLabels);
        const right = buildSingleDieRig({ ...d10Def, geom:"d10", numbered:true, custom:false },  0.95, selected.tint, tensLabels);

        scene.add(left.body); scene.add(left.decals);
        scene.add(right.body); scene.add(right.decals);

        const text = makeFloatingText("—");
        text.position.set(0, 0.72, 0);
        scene.add(text);

        rig = { parts:[left, right], text, rolling:false };

      } else {
        const single = buildSingleDieRig(selected, 0, null, null);
        scene.add(single.body);
        scene.add(single.decals);

        const text = makeFloatingText("—");
        text.position.set(0, 0.72, 0);
        scene.add(text);

        rig = { parts:[single], text, rolling:false };
      }
    }

    // Floating label
    function makeFloatingText(text){
      const c = document.createElement("canvas");
      c.width = 512; c.height = 256;
      const ctx = c.getContext("2d");
      drawFloating(ctx, text);
      const tex = new THREE.CanvasTexture(c);
      tex.anisotropy = 8;
      tex.needsUpdate = true;

      const mat = new THREE.MeshBasicMaterial({ map: tex, transparent:true, opacity:0.92 });
      const geom = new THREE.PlaneGeometry(1.8, 0.9);
      const m = new THREE.Mesh(geom, mat);
      m.userData.ctx = ctx;
      m.userData.tex = tex;
      return m;
    }

    function drawFloating(ctx, text){
      const w = ctx.canvas.width, h = ctx.canvas.height;
      ctx.clearRect(0,0,w,h);

      // subtle only (no "box behind dice" in the viewer itself)
      ctx.font = "900 140px system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial";
      ctx.textAlign = "center";
      ctx.textBaseline = "middle";

      ctx.fillStyle = "rgba(0,0,0,0.55)";
      ctx.fillText(String(text), w/2 + 10, h/2 + 14);

      ctx.fillStyle = "rgba(255,255,255,0.92)";
      ctx.fillText(String(text), w/2, h/2 + 6);
    }

    function setRigText(text){
      if(!rig?.text) return;
      const ctx = rig.text.userData.ctx;
      drawFloating(ctx, text);
      rig.text.userData.tex.needsUpdate = true;
    }

    // ---------------------------
    // Resize
    // ---------------------------
    function resize(){
      const rect = canvas.getBoundingClientRect();
      const w = Math.max(1, Math.floor(rect.width));
      const h = Math.max(1, Math.floor(rect.height));
      renderer.setSize(w, h, false);
      camera.aspect = w / h;
      camera.updateProjectionMatrix();
    }
    window.addEventListener("resize", resize);

    // ---------------------------
    // Animation helpers
    // ---------------------------
    function pop(part){
      const start = performance.now();
      const base = part.body.scale.clone();
      const dur = 180;
      function step(){
        const p = (performance.now() - start) / dur;
        const k = p < 1 ? (1 - Math.pow(1-p, 3)) : 1;
        const s = 1 + Math.sin(k*Math.PI) * 0.08;
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
      const q = targetQuaternionFromNormals(faceIndex, part.normals, part.normals.length);
      part.body.quaternion.copy(q);
      part.decals.quaternion.copy(q);
      pop(part);
    }

    async function animatePartSpinAndLand(part, faceIndex, durationMs){
      if(!part.normals){
        pop(part);
        return;
      }
      const start = performance.now();
      const startQ = part.body.quaternion.clone();
      const targetQ = targetQuaternionFromNormals(faceIndex, part.normals, part.normals.length);

      const spinAxis = new THREE.Vector3(Math.random()*2-1, Math.random()*2-1, Math.random()*2-1).normalize();
      const spinAmount = (Math.random()*9 + 9) * Math.PI;
      const qSpin = new THREE.Quaternion().setFromAxisAngle(spinAxis, spinAmount);
      const midQ = startQ.clone().multiply(qSpin);

      await new Promise(resolve => {
        function step(){
          const p = (performance.now() - start) / durationMs;
          const eased = p < 1 ? (1 - Math.pow(1-p, 3)) : 1;

          let q;
          if(eased < 0.62){
            const a = eased / 0.62;
            q = startQ.clone().slerp(midQ, a);
          }else{
            const b = (eased - 0.62) / 0.38;
            q = midQ.clone().slerp(targetQ, b);
          }

          part.body.quaternion.copy(q);
          part.decals.quaternion.copy(q);

          part.body.position.y = 0.22 + Math.sin(eased*Math.PI) * 0.18;
          part.decals.position.copy(part.body.position);

          if(p < 1) requestAnimationFrame(step);
          else resolve();
        }
        requestAnimationFrame(step);
      });

      part.body.quaternion.copy(targetQ);
      part.decals.quaternion.copy(targetQ);
      part.body.position.y = 0.22;
      part.decals.position.copy(part.body.position);
      pop(part);
    }

    // ---------------------------
    // Roll logic (including d100 pair)
    // ---------------------------
    async function roll(){
      const expr = exprChip.textContent;

      if(selected.key === "d100"){
        // Each "count" roll produces one percentile number.
        const results = [];
        const detail = [];

        for(let i=0;i<count;i++){
          const ones = randInt(0,9);
          const tens = randInt(0,9); // 0..9 mapping to 00..90

          const val0_99 = tens*10 + ones;
          const val = (val0_99 === 0) ? 100 : val0_99; // common convention

          results.push(val);
          detail.push(`${tens*10}`.padStart(2,"0") + ` + ${ones}`); // human readable
        }

        const sum = results.reduce((a,b)=>a+b,0);
        const total = sum + mod;

        totalChip.textContent = String(total);
        lastResultText = `${expr} = ${total} (percentiles: ${results.join(", ")})`;
        pushHistory(expr, `percentiles: ${results.join(", ")}`, total);

        rightSub.textContent = animateRoll ? "Rolling…" : "Instant roll.";
        setRigText("…");

        // Animate last roll only (pair dice)
        const last = results[results.length-1];
        const ones = (last === 100) ? 0 : (last % 10);
        const tens = (last === 100) ? 0 : Math.floor(last / 10);

        // Our d10 face labels are 0..9 in order; faceIndex = value
        if(animateRoll){
          rig.rolling = true;
          await Promise.all([
            animatePartSpinAndLand(rig.parts[0], ones, 1100), // ones die
            animatePartSpinAndLand(rig.parts[1], tens, 1100), // tens die
          ]);
          rig.rolling = false;
        }else{
          landPartInstant(rig.parts[0], ones);
          landPartInstant(rig.parts[1], tens);
        }

        setRigText(String(last));
        rightSub.textContent = "Ready.";
        return;
      }

      // normal dice
      const sides = selected.custom ? selected.sides : selected.sides;
      const rolls = Array.from({length: count}, () => randInt(1, sides));
      const sum = rolls.reduce((a,b)=>a+b,0);
      const total = sum + mod;

      totalChip.textContent = String(total);
      lastResultText = `${expr} = ${total} (rolls: ${rolls.join(", ")})`;
      pushHistory(expr, `rolls: ${rolls.join(", ")}`, total);

      const lastRoll = rolls[rolls.length - 1];
      rightSub.textContent = animateRoll ? "Rolling…" : "Instant roll.";
      setRigText("…");

      const part = rig.parts[0];

      // If this die has normals, faceIndex should match label index:
      // - For d10 labels 0..9, we stored in labels array in order; roll is 1..10 for d10? Actually d10 uses 0..9; but we treat d10 as 1..10? No, we set d10 sides=10 but for roll() we used 1..10. Fix by special-case:
      let faceIndex;
      if(selected.key === "d10"){
        // Convert 1..10 roll into 0..9 with 10 -> 0
        const v = (lastRoll === 10) ? 0 : lastRoll;
        faceIndex = v;
      }else if(selected.key === "d2"){
        // roll 1..2 maps to faceIndex 0..1
        faceIndex = lastRoll - 1;
      }else{
        faceIndex = lastRoll - 1;
      }

      if(animateRoll){
        rig.rolling = true;
        await animatePartSpinAndLand(part, faceIndex, 1100);
        rig.rolling = false;
      }else{
        landPartInstant(part, faceIndex);
      }

      setRigText(String(lastRoll));
      rightSub.textContent = "Ready.";
    }

    // ---------------------------
    // Idle render loop
    // ---------------------------
    let t0 = performance.now();
    function tick(now){
      const dt = Math.min(0.05, (now - t0) / 1000);
      t0 = now;

      camera.lookAt(LOOK_AT);

      if(rig && !rig.rolling){
        for(const part of rig.parts){
          part.body.rotation.y += dt * 0.55;
          part.body.rotation.x += dt * 0.20;
          part.decals.rotation.copy(part.body.rotation);
        }
      }

      if(rig?.text){
        rig.text.lookAt(camera.position);
        rig.text.position.y = 0.72 + Math.sin(now/650) * 0.03;
      }

      renderer.render(scene, camera);
      requestAnimationFrame(tick);
    }

    // ---------------------------
    // Init
    // ---------------------------
    renderDiceCards();
    syncUI();
    buildSceneDice();
    resize();
    requestAnimationFrame(tick);
  </script>
</body>
</html>
