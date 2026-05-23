<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Editor de Alineaciones - 2 Equipos</title>
  <style>
    *,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
    :root{
      --bg:#0f0f1a;--panel:#16182e;--border:#1e2240;--text:#dde3ed;--muted:#5a6a80;
      --accent:#4cc9f0;--field1:#25703a;--field2:#2a7d41
    }
    body{
      font-family:"Segoe UI",Arial,sans-serif;background:var(--bg);color:var(--text);
      min-height:100vh;display:flex;flex-direction:column;align-items:center;padding:24px 12px 40px
    }
    h1{font-size:20px;font-weight:700;color:#fff;margin-bottom:4px;letter-spacing:.04em;text-align:center}
    .subtitle{font-size:12px;color:#556070;margin-bottom:20px;text-align:center}
    .layout{display:flex;gap:16px;width:100%;max-width:920px;align-items:flex-start;flex-wrap:wrap;justify-content:center}
    .panel{width:200px;flex-shrink:0;display:flex;flex-direction:column;gap:10px}
    .panel-header{
      font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.1em;padding:8px 12px;
      border-radius:8px 8px 0 0;text-align:center
    }
    .panel-header.team1{background:#1c2540;color:#7eb8f7;border-bottom:2px solid #1d6ab5}
    .panel-header.team2{background:#251c1c;color:#f7a07e;border-bottom:2px solid #e63946}
    .ctrl-group{background:var(--panel);border:1px solid var(--border);border-radius:8px;padding:10px 12px}
    .ctrl-label{
      font-size:10px;font-weight:600;color:var(--muted);text-transform:uppercase;
      letter-spacing:.08em;margin-bottom:6px
    }
    select{
      width:100%;font-size:12px;padding:7px 8px;border-radius:6px;border:1px solid #252845;
      background:var(--bg);color:var(--text);cursor:pointer;outline:none;transition:border-color .2s
    }
    select:focus{border-color:var(--accent)}
    .color-row{display:flex;gap:6px;flex-wrap:wrap}
    .color-swatch{
      width:22px;height:22px;border-radius:50%;cursor:pointer;border:2px solid transparent;
      transition:transform .15s;flex-shrink:0
    }
    .color-swatch:hover{transform:scale(1.12)}
    .color-swatch.active{border-color:#fff;box-shadow:0 0 0 1px #fff4}
    .stat-row{display:flex;align-items:center;gap:6px;margin-bottom:5px}
    .stat-row:last-child{margin-bottom:0}
    .stat-name{font-size:9px;color:var(--muted);width:26px;flex-shrink:0;text-transform:uppercase}
    .stat-bar-bg{flex:1;height:6px;background:#1e2240;border-radius:3px;overflow:hidden}
    .stat-bar{height:100%;border-radius:3px;transition:width .35s ease}
    .stat-val{font-size:9px;color:#7a8a9a;width:24px;text-align:right;flex-shrink:0}
    .legend{font-size:11px;color:#6a7a8a;line-height:1.9}
    .dot{
      display:inline-block;width:10px;height:10px;border-radius:50%;margin-right:5px;vertical-align:middle
    }
    .field-wrap{
      flex:1;min-width:280px;max-width:380px;display:flex;flex-direction:column;align-items:center
    }
    svg#field{width:100%;border-radius:10px;display:block;box-shadow:0 14px 35px rgba(0,0,0,.24)}
    .player-circle{cursor:pointer}
    #info-bar{
      margin-top:8px;min-height:20px;text-align:center;font-size:12px;font-weight:500;
      color:var(--accent);letter-spacing:.03em
    }
    #simBtn{
      margin-top:10px;padding:9px 20px;border:none;border-radius:7px;
      background:linear-gradient(135deg,#4cc9f0,#4361ee);color:#fff;font-weight:700;
      font-size:13px;cursor:pointer;letter-spacing:.03em;transition:opacity .2s,transform .1s
    }
    #simBtn:hover{opacity:.92}
    #simBtn:active{transform:scale(.98)}
    #result-panel{
      margin-top:10px;width:100%;background:var(--panel);border:1px solid var(--border);
      border-radius:10px;padding:10px 12px;display:none;flex-direction:column;gap:6px;
      opacity:0;transition:opacity .28s ease
    }
    #result-panel.visible{display:flex;opacity:1}
    .score-line{font-size:20px;font-weight:800;text-align:center;color:#fff;letter-spacing:.05em}
    .winner-line{font-size:12px;font-weight:600;text-align:center;color:var(--accent)}
    .sep{border:none;border-top:1px solid var(--border);margin:2px 0}
    .stat-match-row{
      display:grid;grid-template-columns:28px 1fr 28px 64px;align-items:center;gap:6px;margin-bottom:6px
    }
    .stat-match-row:last-child{margin-bottom:0}
    .stat-match-name{font-size:9px;color:var(--muted);text-align:right;text-transform:uppercase}
    .bar-wrap{display:flex;gap:4px;height:8px;width:100%;align-items:center}
    .bar-left-zone,.bar-right-zone{
      flex:1;background:#1e2240;height:100%;border-radius:999px;overflow:hidden;display:flex;align-items:center
    }
    .bar-left-zone{justify-content:flex-end}
    .bar-right-zone{justify-content:flex-start}
    .bar-left,.bar-right{height:100%;transition:width .4s ease}
    .bar-left{border-radius:999px 0 0 999px}
    .bar-right{border-radius:0 999px 999px 0}
    .stat-match-val,.stat-match-val2{font-size:9px;color:#7a8a9a;text-align:center}
    .narrative{font-size:10px;color:#6a7a8a;text-align:center;line-height:1.5;font-style:italic}
    #toast{
      position:fixed;top:20px;left:50%;transform:translateX(-50%) translateY(-80px);
      background:#7f1d1d;color:#fecaca;border:1px solid #f87171;border-radius:10px;
      padding:12px 22px;font-size:13px;font-weight:600;z-index:999;
      transition:transform .35s cubic-bezier(.22,.68,0,1.2),opacity .3s;opacity:0;
      pointer-events:none;white-space:nowrap
    }
    #toast.show{transform:translateX(-50%) translateY(0);opacity:1}
    @media (max-width:900px){
      .panel{width:min(100%,360px)}
      .field-wrap{order:-1}
    }
  </style>
</head>
<body>
  <h1>⚽ Editor de Alineaciones</h1>
  <p class="subtitle">Configura los dos equipos y simula el partido con táctica real</p>

  <div id="toast"></div>

  <div class="layout">
    <div class="panel">
      <div class="panel-header team1">Equipo Local</div>
      <div class="ctrl-group">
        <div class="ctrl-label">Formación</div>
        <select id="formation1"></select>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Táctica</div>
        <select id="style1"></select>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Color</div>
        <div class="color-row" id="cp1"></div>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Stats del equipo</div>
        <div id="stats1"></div>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Referencia</div>
        <div class="legend">
          <div><span class="dot" id="leg1"></span> Equipo</div>
          <div><span class="dot" style="background:#ffd700"></span> Portero</div>
        </div>
      </div>
    </div>

    <div class="field-wrap">
      <svg id="field" viewBox="0 0 340 510" xmlns="http://www.w3.org/2000/svg"></svg>
      <div id="info-bar">Haz clic sobre un jugador para ver su posición</div>
      <button id="simBtn">Simular Partido ⚽</button>
      <div id="result-panel">
        <div class="score-line" id="score-line"></div>
        <div class="winner-line" id="winner-line"></div>
        <hr class="sep" />
        <div id="match-stats"></div>
        <hr class="sep" />
        <div class="narrative" id="narrative"></div>
      </div>
    </div>

    <div class="panel">
      <div class="panel-header team2">Equipo Visitante</div>
      <div class="ctrl-group">
        <div class="ctrl-label">Formación</div>
        <select id="formation2"></select>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Táctica</div>
        <select id="style2"></select>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Color</div>
        <div class="color-row" id="cp2"></div>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Stats del equipo</div>
        <div id="stats2"></div>
      </div>
      <div class="ctrl-group">
        <div class="ctrl-label">Referencia</div>
        <div class="legend">
          <div><span class="dot" id="leg2"></span> Equipo</div>
          <div><span class="dot" style="background:#ffd700"></span> Portero</div>
        </div>
      </div>
    </div>
  </div>

  <script>
    const COLORS = [
      { name: "Rojo", val: "#e63946" },
      { name: "Azul", val: "#1d6ab5" },
      { name: "Verde", val: "#2d9a4a" },
      { name: "Naranja", val: "#f4a022" },
      { name: "Morado", val: "#7b2d8b" },
      { name: "Cian", val: "#0ea5e9" },
      { name: "Rosa", val: "#e0579a" },
      { name: "Blanco", val: "#eeeeee" },
      { name: "Negro", val: "#333333" },
      { name: "Marrón", val: "#7c3f10" }
    ];

    const FORMATIONS = {
      "442": { label: "4-4-2", atk: 82, def: 82, mid: 80, press: 75, width: 85, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.18, 0.4, 0.6, 0.82], [0.25, 0.75]] },
      "433": { label: "4-3-3", atk: 90, def: 76, mid: 80, press: 88, width: 90, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.25, 0.5, 0.75], [0.18, 0.5, 0.82]] },
      "4231": { label: "4-2-3-1", atk: 85, def: 84, mid: 90, press: 78, width: 80, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.3, 0.7], [0.18, 0.5, 0.82], [0.5]] },
      "352": { label: "3-5-2", atk: 84, def: 80, mid: 88, press: 82, width: 76, lines: [[0.5], [0.25, 0.5, 0.75], [0.1, 0.3, 0.5, 0.7, 0.9], [0.3, 0.7]] },
      "343": { label: "3-4-3", atk: 92, def: 72, mid: 82, press: 85, width: 88, lines: [[0.5], [0.25, 0.5, 0.75], [0.18, 0.4, 0.6, 0.82], [0.18, 0.5, 0.82]] },
      "532": { label: "5-3-2", atk: 74, def: 94, mid: 76, press: 65, width: 72, lines: [[0.5], [0.1, 0.28, 0.5, 0.72, 0.9], [0.25, 0.5, 0.75], [0.3, 0.7]] },
      "4141": { label: "4-1-4-1", atk: 78, def: 90, mid: 84, press: 72, width: 80, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.5], [0.18, 0.4, 0.6, 0.82], [0.5]] },
      "451": { label: "4-5-1", atk: 76, def: 88, mid: 88, press: 76, width: 78, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.1, 0.3, 0.5, 0.7, 0.9], [0.5]] },
      "541": { label: "5-4-1", atk: 70, def: 96, mid: 80, press: 60, width: 74, lines: [[0.5], [0.1, 0.28, 0.5, 0.72, 0.9], [0.18, 0.4, 0.6, 0.82], [0.5]] },
      "4411": { label: "4-4-1-1", atk: 80, def: 85, mid: 82, press: 75, width: 78, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.18, 0.4, 0.6, 0.82], [0.5], [0.5]] },
      "4222": { label: "4-2-2-2", atk: 88, def: 78, mid: 84, press: 82, width: 70, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.3, 0.7], [0.3, 0.7], [0.35, 0.65]] },
      "41212": { label: "4-1-2-1-2", atk: 86, def: 82, mid: 90, press: 80, width: 65, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.5], [0.3, 0.7], [0.5], [0.35, 0.65]] },
      "4312": { label: "4-3-1-2", atk: 85, def: 80, mid: 88, press: 78, width: 68, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.3, 0.5, 0.7], [0.5], [0.35, 0.65]] },
      "3421": { label: "3-4-2-1", atk: 87, def: 78, mid: 85, press: 84, width: 82, lines: [[0.5], [0.25, 0.5, 0.75], [0.18, 0.4, 0.6, 0.82], [0.35, 0.65], [0.5]] },
      "3142": { label: "3-1-4-2", atk: 84, def: 83, mid: 88, press: 80, width: 78, lines: [[0.5], [0.25, 0.5, 0.75], [0.5], [0.18, 0.4, 0.6, 0.82], [0.35, 0.65]] },
      "424": { label: "4-2-4", atk: 95, def: 65, mid: 70, press: 90, width: 92, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.3, 0.7], [0.1, 0.3, 0.7, 0.9]] },
      "325": { label: "3-2-5", atk: 96, def: 60, mid: 82, press: 88, width: 95, lines: [[0.5], [0.25, 0.5, 0.75], [0.3, 0.7], [0.1, 0.3, 0.5, 0.7, 0.9]] },
      "451_flat": { label: "4-5-1 (Plano)", atk: 74, def: 90, mid: 92, press: 70, width: 75, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.1, 0.3, 0.5, 0.7, 0.9], [0.5]] },
      "4141_def": { label: "4-1-4-1 (Defensivo)", atk: 72, def: 94, mid: 85, press: 65, width: 72, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.5], [0.18, 0.4, 0.6, 0.82], [0.5]] },
      "433_false9": { label: "4-3-3 (Falso 9)", atk: 88, def: 75, mid: 92, press: 85, width: 88, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.25, 0.5, 0.75], [0.18, 0.5, 0.82]] },
      "4312_wide": { label: "4-3-1-2 (Amplia)", atk: 86, def: 78, mid: 85, press: 80, width: 82, lines: [[0.5], [0.18, 0.4, 0.6, 0.82], [0.15, 0.5, 0.85], [0.5], [0.35, 0.65]] }
    };

    const TACTICS = {
      equilibrado: { label: "Equilibrado", mod: { atk: 1, def: 1, mid: 1, press: 1, width: 1 }, counters: {} },
      presion: { label: "Presión Alta", mod: { atk: 1.08, def: 0.9, mid: 1.05, press: 1.2, width: 1.05 }, counters: { posesion: -8, contra: 10 } },
      posesion: { label: "Posesión", mod: { atk: 0.95, def: 1.05, mid: 1.15, press: 0.85, width: 0.9 }, counters: { presion: 8, contra: -6, defensivo: -5 } },
      contra: { label: "Contraataque", mod: { atk: 1.12, def: 0.95, mid: 0.9, press: 0.8, width: 1.1 }, counters: { presion: -10, defensivo: 12, posesion: 6 } },
      defensivo: { label: "Bloque Bajo", mod: { atk: 0.8, def: 1.2, mid: 0.9, press: 0.7, width: 0.85 }, counters: { contra: -12, posesion: 5, presion: 8 } },
      ofensivo: { label: "Ataque Total", mod: { atk: 1.18, def: 0.78, mid: 1, press: 1.1, width: 1.15 }, counters: { defensivo: -10, contra: -14, presion: 6 } }
    };

    const POS_NAMES = {
      GK: "Portero", LB: "Lateral Izq.", RB: "Lateral Der.", LCB: "Central Izq.", CB: "Central",
      RCB: "Central Der.", LWB: "Carrilero Izq.", RWB: "Carrilero Der.", CDM: "Med. Defensivo",
      CM: "Centrocampista", LM: "Interior Izq.", RM: "Interior Der.", CAM: "Med. Atacante",
      LW: "Extremo Izq.", RW: "Extremo Der.", ST: "Delantero"
    };

    const state = {
      1: { color: "#1d6ab5", formation: "442", tactic: "equilibrado" },
      2: { color: "#e63946", formation: "442", tactic: "equilibrado" }
    };

    const BAR_COLORS = { atk: "#f87171", def: "#60a5fa", mid: "#a78bfa", press: "#34d399", width: "#fbbf24" };
    const BAR_LABELS = { atk: "ATK", def: "DEF", mid: "MED", press: "PRE", width: "AMP" };
    const W = 340, H = 510, PAD = 22, FW = W - 44, FH = H - 44;
    const NS = "http://www.w3.org/2000/svg";
    let toastTimer = null;

    function showToast(msg) {
      const toast = document.getElementById("toast");
      toast.textContent = "⚠️  " + msg;
      toast.classList.add("show");
      clearTimeout(toastTimer);
      toastTimer = setTimeout(() => toast.classList.remove("show"), 3000);
    }

    function se(tag, attrs) {
      const element = document.createElementNS(NS, tag);
      Object.keys(attrs).forEach((key) => element.setAttribute(key, attrs[key]));
      return element;
    }

    function textColor(hex) {
      const r = parseInt(hex.slice(1, 3), 16);
      const g = parseInt(hex.slice(3, 5), 16);
      const b = parseInt(hex.slice(5, 7), 16);
      return (0.299 * r + 0.587 * g + 0.114 * b) > 155 ? "#111" : "#fff";
    }

    function getLineRole(lineIndex, lines) {
      const rows = lines.length;
      const last = rows - 1;
      if (lineIndex === 0) return "goalkeeper";
      if (lineIndex === 1) return "defense";
      if (lineIndex === last) return "attack";
      if (rows === 4) return "midfield";
      if (rows === 5) {
        if (lineIndex === 2) return lines[lineIndex].length <= 2 ? "holding" : "midfield";
        return lines[lineIndex].length >= 4 ? "midfield" : "attackMid";
      }
      if (rows >= 6) return lineIndex === 2 ? "holding" : (lineIndex === last - 1 ? "attackMid" : "midfield");
      return "midfield";
    }

    function positionsForLine(role, count) {
      if (role === "goalkeeper") return ["GK"];
      if (role === "defense" && count === 3) return ["LCB", "CB", "RCB"];
      if (role === "defense" && count === 4) return ["LB", "LCB", "RCB", "RB"];
      if (role === "defense" && count === 5) return ["LWB", "LCB", "CB", "RCB", "RWB"];
      if (role === "holding" && count === 1) return ["CDM"];
      if (role === "holding" && count === 2) return ["CDM", "CDM"];
      if (role === "midfield" && count === 1) return ["CM"];
      if (role === "midfield" && count === 2) return ["CM", "CM"];
      if (role === "midfield" && count === 3) return ["LM", "CM", "RM"];
      if (role === "midfield" && count === 4) return ["LM", "CM", "CM", "RM"];
      if (role === "midfield" && count === 5) return ["LWB", "LM", "CM", "RM", "RWB"];
      if (role === "attackMid" && count === 1) return ["CAM"];
      if (role === "attackMid" && count === 2) return ["CAM", "CAM"];
      if (role === "attackMid" && count === 3) return ["LW", "CAM", "RW"];
      if (role === "attackMid" && count === 4) return ["LW", "CAM", "CAM", "RW"];
      if (role === "attack" && count === 1) return ["ST"];
      if (role === "attack" && count === 2) return ["ST", "ST"];
      if (role === "attack" && count === 3) return ["LW", "ST", "RW"];
      if (role === "attack" && count === 4) return ["LW", "ST", "ST", "RW"];
      if (role === "attack" && count === 5) return ["LW", "ST", "ST", "ST", "RW"];
      return new Array(count).fill(role === "attack" ? "ST" : "CM");
    }

    function getPlayerInfo(lineIndex, playerIndex, lines) {
      if (lineIndex === 0) return { num: 1, pos: "GK" };
      let num = 2;
      for (let i = 1; i < lineIndex; i += 1) num += lines[i].length;
      num += playerIndex;
      return { num, pos: positionsForLine(getLineRole(lineIndex, lines), lines[lineIndex].length)[playerIndex] || "CM" };
    }

    function resetResult() {
      document.getElementById("result-panel").classList.remove("visible");
      document.getElementById("score-line").textContent = "";
      document.getElementById("winner-line").textContent = "";
      document.getElementById("match-stats").innerHTML = "";
      document.getElementById("narrative").textContent = "";
    }

    function drawField(svg) {
      svg.innerHTML = "";
      svg.appendChild(se("rect", { x: 0, y: 0, width: W, height: H, rx: 8, fill: "#25703a" }));
      for (let i = 0; i < 6; i += 1) {
        svg.appendChild(se("rect", {
          x: PAD, y: PAD + i * (FH / 6), width: FW, height: FH / 6,
          fill: i % 2 === 0 ? "#25703a" : "#2a7d41", opacity: 0.85
        }));
      }
      svg.appendChild(se("rect", {
        x: PAD, y: PAD, width: FW, height: FH, fill: "none",
        stroke: "rgba(255,255,255,0.75)", "stroke-width": "1.5", rx: 2
      }));
      svg.appendChild(se("line", { x1: PAD, y1: H / 2, x2: W - PAD, y2: H / 2, stroke: "rgba(255,255,255,0.7)", "stroke-width": "1" }));
      svg.appendChild(se("circle", { cx: W / 2, cy: H / 2, r: 38, fill: "none", stroke: "rgba(255,255,255,0.65)", "stroke-width": "1" }));
      svg.appendChild(se("circle", { cx: W / 2, cy: H / 2, r: 2.5, fill: "rgba(255,255,255,0.9)" }));

      const areaW = 180, areaH = 80, boxW = 88, boxH = 30, areaX = (W - areaW) / 2, boxX = (W - boxW) / 2;
      const lineStyle = { fill: "none", stroke: "rgba(255,255,255,0.65)", "stroke-width": "1" };
      svg.appendChild(se("rect", { x: areaX, y: PAD, width: areaW, height: areaH, ...lineStyle }));
      svg.appendChild(se("rect", { x: boxX, y: PAD, width: boxW, height: boxH, ...lineStyle }));
      svg.appendChild(se("rect", { x: areaX, y: H - PAD - areaH, width: areaW, height: areaH, ...lineStyle }));
      svg.appendChild(se("rect", { x: boxX, y: H - PAD - boxH, width: boxW, height: boxH, ...lineStyle }));
      svg.appendChild(se("circle", { cx: W / 2, cy: PAD + 62, r: 2, fill: "rgba(255,255,255,0.8)" }));
      svg.appendChild(se("circle", { cx: W / 2, cy: H - PAD - 62, r: 2, fill: "rgba(255,255,255,0.8)" }));

      const goalW = 60, goalH = 12, goalX = (W - goalW) / 2;
      const goalStyle = { fill: "rgba(255,255,255,0.1)", stroke: "rgba(255,255,255,0.5)", "stroke-width": "1.2", rx: 1 };
      svg.appendChild(se("rect", { x: goalX, y: PAD - goalH, width: goalW, height: goalH, ...goalStyle }));
      svg.appendChild(se("rect", { x: goalX, y: H - PAD, width: goalW, height: goalH, ...goalStyle }));

      const labelStyle = {
        x: PAD + 6, "font-family": "Segoe UI, Arial, sans-serif", "font-size": "9", "font-weight": "700",
        fill: "rgba(255,255,255,0.3)", "dominant-baseline": "middle"
      };
      const homeLabel = se("text", { ...labelStyle, y: H / 2 - 11 });
      const awayLabel = se("text", { ...labelStyle, y: H / 2 + 11 });
      homeLabel.textContent = "LOCAL";
      awayLabel.textContent = "VISIT.";
      svg.appendChild(homeLabel);
      svg.appendChild(awayLabel);
    }

    function drawTeam(svg, teamId) {
      const { color, formation } = state[teamId];
      const lines = FORMATIONS[formation].lines;
      const radius = 14;
      const rows = lines.length;
      const margin = 18;
      const gkY = teamId === 1 ? H - PAD - 16 : PAD + 16;
      const attackY = teamId === 1 ? H / 2 + margin : H / 2 - margin;
      const step = (attackY - gkY) / (rows - 1);

      lines.forEach((xFracs, lineIndex) => {
        const cy = Math.round(gkY + step * lineIndex);
        xFracs.forEach((xFrac, playerIndex) => {
          const cx = Math.round(PAD + FW * xFrac);
          const isGK = lineIndex === 0;
          const fill = isGK ? "#ffd700" : color;
          const { num, pos } = getPlayerInfo(lineIndex, playerIndex, lines);
          const group = se("g", { class: "player-circle", "data-team": teamId, "data-num": num, "data-pos": pos });
          group.appendChild(se("circle", { cx: cx + 1.5, cy: cy + 1.5, r: radius, fill: "rgba(0,0,0,0.4)" }));
          group.appendChild(se("circle", {
            cx, cy, r: radius, fill, "stroke-width": "1.5",
            stroke: isGK ? "rgba(0,0,0,0.3)" : "rgba(255,255,255,0.8)"
          }));

          const numberText = se("text", {
            x: cx, y: cy + 1, "text-anchor": "middle", "dominant-baseline": "middle",
            "font-size": "12", "font-weight": "700", fill: isGK ? "#111" : textColor(fill), "font-family": "Segoe UI, Arial, sans-serif"
          });
          numberText.textContent = num;
          group.appendChild(numberText);

          const posText = se("text", {
            x: cx, y: cy + radius + 9, "text-anchor": "middle", "dominant-baseline": "middle",
            "font-size": "8", fill: "rgba(255,255,255,0.72)", "font-family": "Segoe UI, Arial, sans-serif"
          });
          posText.textContent = pos;
          group.appendChild(posText);

          group.addEventListener("click", () => {
            document.getElementById("info-bar").textContent =
              (teamId === 1 ? "Local" : "Visitante") + " — #" + num + " · " + (POS_NAMES[pos] || pos);
          });

          svg.appendChild(group);
        });
      });
    }

    function getEffectiveStats(teamId) {
      const formation = FORMATIONS[state[teamId].formation];
      const tactic = TACTICS[state[teamId].tactic];
      return {
        atk: Math.round(formation.atk * tactic.mod.atk),
        def: Math.round(formation.def * tactic.mod.def),
        mid: Math.round(formation.mid * tactic.mod.mid),
        press: Math.round(formation.press * tactic.mod.press),
        width: Math.round(formation.width * tactic.mod.width)
      };
    }

    function updateStatBars(teamId) {
      const stats = getEffectiveStats(teamId);
      const container = document.getElementById("stats" + teamId);
      container.innerHTML = "";
      ["atk", "def", "mid", "press", "width"].forEach((key) => {
        const val = Math.min(stats[key], 100);
        const row = document.createElement("div");
        row.className = "stat-row";
        row.innerHTML =
          "<span class=\"stat-name\">" + BAR_LABELS[key] + "</span>" +
          "<div class=\"stat-bar-bg\"><div class=\"stat-bar\" style=\"width:" + val + "%;background:" + BAR_COLORS[key] + "\"></div></div>" +
          "<span class=\"stat-val\">" + val + "</span>";
        container.appendChild(row);
      });
    }

    function render() {
      const svg = document.getElementById("field");
      drawField(svg);
      drawTeam(svg, 1);
      drawTeam(svg, 2);
      document.getElementById("leg1").style.background = state[1].color;
      document.getElementById("leg2").style.background = state[2].color;
      updateStatBars(1);
      updateStatBars(2);
    }

    function chanceCount(expected) {
      const base = Math.floor(expected);
      const decimal = expected - base;
      return base + (Math.random() < decimal ? 1 : 0);
    }

    function randomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function clamp(value, min, max) {
      return Math.max(min, Math.min(max, value));
    }

    function conversionRate(atk, opponentDef) {
      const base = 0.32;
      const delta = (atk - opponentDef) / 200;
      return Math.max(0.08, Math.min(0.65, base + delta));
    }

    function scoreGoals(chances, conv) {
      let goals = 0;
      const attempts = chanceCount(chances);
      for (let i = 0; i < attempts; i += 1) {
        const noisyConversion = conv + (Math.random() - 0.5) * 0.1;
        if (Math.random() < noisyConversion) goals += 1;
      }
      return goals;
    }

    function simulate() {
      const s1 = getEffectiveStats(1), s2 = getEffectiveStats(2);
      const tac1 = state[1].tactic, tac2 = state[2].tactic;
      const cnt1 = TACTICS[tac1].counters[tac2] || 0;
      const cnt2 = TACTICS[tac2].counters[tac1] || 0;

      const eff1 = { atk: s1.atk + cnt1, def: s1.def - cnt2, mid: s1.mid };
      const eff2 = { atk: s2.atk + cnt2, def: s2.def - cnt1, mid: s2.mid };
      const midBattle1 = eff1.mid + s1.press * 0.3 - s2.press * 0.2;
      const midBattle2 = eff2.mid + s2.press * 0.3 - s1.press * 0.2;
      const total = midBattle1 + midBattle2;
      const poss1 = total > 0 ? midBattle1 / total : 0.5;
      const poss2 = 1 - poss1;

      const chancesBase = 6;
      const ch1 = chancesBase * (poss1 * 1.6 + 0.2) * (s1.width / 80);
      const ch2 = chancesBase * (poss2 * 1.6 + 0.2) * (s2.width / 80);
      const conv1 = conversionRate(eff1.atk, eff2.def);
      const conv2 = conversionRate(eff2.atk, eff1.def);

      const basePoss1 = Math.round(poss1 * 100);
      const possDelta = randomInt(-5, 5);
      const finalPoss1 = clamp(basePoss1 + possDelta, 1, 99);
      const finalPoss2 = 100 - finalPoss1;

      const finalCh1 = Math.max(0, Math.round(ch1) + randomInt(-1, 1));
      const finalCh2 = Math.max(0, Math.round(ch2) + randomInt(-1, 1));
      const finalConv1 = clamp(Math.round(conv1 * 100) + randomInt(-2, 2), 1, 100);
      const finalConv2 = clamp(Math.round(conv2 * 100) + randomInt(-2, 2), 1, 100);

      return {
        g1: scoreGoals(finalCh1, finalConv1 / 100),
        g2: scoreGoals(finalCh2, finalConv2 / 100),
        poss1: finalPoss1,
        poss2: finalPoss2,
        ch1: finalCh1,
        ch2: finalCh2,
        conv1: finalConv1,
        conv2: finalConv2,
        cnt1, cnt2
      };
    }

    function generateNarrative(res) {
      const tac1 = TACTICS[state[1].tactic].label;
      const tac2 = TACTICS[state[2].tactic].label;
      const form1 = FORMATIONS[state[1].formation].label;
      const form2 = FORMATIONS[state[2].formation].label;
      const lines = [];

      if (res.poss1 > 60) lines.push("El local dominó la posesión con su " + form1 + ".");
      else if (res.poss2 > 60) lines.push("El visitante controló el juego con su " + form2 + ".");
      else lines.push("Partido equilibrado en cuanto a posesión.");

      if (res.cnt1 > 5) lines.push("La táctica \"" + tac1 + "\" neutralizó bien al rival.");
      else if (res.cnt2 > 5) lines.push("\"" + tac2 + "\" del visitante fue la clave táctica del partido.");
      else if (res.cnt1 < -5) lines.push("El local sufrió ante la táctica rival.");
      else if (res.cnt2 < -5) lines.push("El visitante no pudo adaptarse tácticamente.");

      if (res.g1 > res.g2 + 2) lines.push("Victoria contundente del local.");
      else if (res.g2 > res.g1 + 2) lines.push("Goleada del visitante.");
      else if (res.g1 === res.g2) lines.push("Las defensas respondieron bien. Empate justo.");

      return lines.join(" ");
    }

    function comparisonWidths(v1, v2) {
      const total = Math.max(v1 + v2, 1);
      return { left: Math.round(v1 / total * 100), right: Math.round(v2 / total * 100) };
    }

    function matchStatRow(label, v1, v2, leftColor, rightColor) {
      const widths = comparisonWidths(v1, v2);
      return (
        "<div class=\"stat-match-row\">" +
          "<span class=\"stat-match-val2\">" + v1 + "</span>" +
          "<div class=\"bar-wrap\">" +
            "<div class=\"bar-left-zone\"><div class=\"bar-left\" style=\"width:" + widths.left + "%;background:" + leftColor + "\"></div></div>" +
            "<div class=\"bar-right-zone\"><div class=\"bar-right\" style=\"width:" + widths.right + "%;background:" + rightColor + "\"></div></div>" +
          "</div>" +
          "<span class=\"stat-match-val\">" + v2 + "</span>" +
          "<span class=\"stat-match-name\">" + label + "</span>" +
        "</div>"
      );
    }

    function renderResult(res) {
      document.getElementById("score-line").textContent = "Local  " + res.g1 + " — " + res.g2 + "  Visitante";
      let winnerLine = "🤝 Empate";
      if (res.g1 > res.g2) winnerLine = "🏆 Gana el Local";
      else if (res.g2 > res.g1) winnerLine = "🏆 Gana el Visitante";

      document.getElementById("winner-line").textContent = winnerLine;
      document.getElementById("match-stats").innerHTML =
        matchStatRow("Posesión", res.poss1, res.poss2, state[1].color, state[2].color) +
        matchStatRow("Ocasiones", res.ch1, res.ch2, state[1].color, state[2].color) +
        matchStatRow("Conversión%", res.conv1, res.conv2, state[1].color, state[2].color);
      document.getElementById("narrative").textContent = generateNarrative(res);
      document.getElementById("result-panel").classList.add("visible");
    }

    function populateSelects() {
      ["formation1", "formation2"].forEach((id) => {
        const select = document.getElementById(id);
        Object.entries(FORMATIONS).forEach(([key, value]) => {
          const option = document.createElement("option");
          option.value = key;
          option.textContent = value.label;
          select.appendChild(option);
        });
      });

      ["style1", "style2"].forEach((id) => {
        const select = document.getElementById(id);
        Object.entries(TACTICS).forEach(([key, value]) => {
          const option = document.createElement("option");
          option.value = key;
          option.textContent = value.label;
          select.appendChild(option);
        });
      });
    }

    function buildPicker(containerId, teamId) {
      const container = document.getElementById(containerId);
      COLORS.forEach((colorItem) => {
        const swatch = document.createElement("div");
        swatch.className = "color-swatch" + (colorItem.val === state[teamId].color ? " active" : "");
        swatch.style.background = colorItem.val;
        if (colorItem.val === "#eeeeee") swatch.style.outline = "1px solid #444";
        swatch.title = colorItem.name;

        swatch.addEventListener("click", () => {
          const otherTeam = teamId === 1 ? 2 : 1;
          if (state[otherTeam].color === colorItem.val) {
            showToast("Los dos equipos no pueden tener el mismo color");
            return;
          }
          state[teamId].color = colorItem.val;
          container.querySelectorAll(".color-swatch").forEach((item) => item.classList.remove("active"));
          swatch.classList.add("active");
          resetResult();
          render();
        });

        container.appendChild(swatch);
      });
    }

    function handleSetupChange(teamId, key, value) {
      state[teamId][key] = value;
      resetResult();
      render();
    }

    document.getElementById("formation1").addEventListener("change", (e) => handleSetupChange(1, "formation", e.target.value));
    document.getElementById("formation2").addEventListener("change", (e) => handleSetupChange(2, "formation", e.target.value));
    document.getElementById("style1").addEventListener("change", (e) => handleSetupChange(1, "tactic", e.target.value));
    document.getElementById("style2").addEventListener("change", (e) => handleSetupChange(2, "tactic", e.target.value));
    document.getElementById("simBtn").addEventListener("click", () => renderResult(simulate()));

    populateSelects();
    document.getElementById("formation1").value = state[1].formation;
    document.getElementById("formation2").value = state[2].formation;
    document.getElementById("style1").value = state[1].tactic;
    document.getElementById("style2").value = state[2].tactic;
    buildPicker("cp1", 1);
    buildPicker("cp2", 2);
    render();
  </script>
</body>
</html>
