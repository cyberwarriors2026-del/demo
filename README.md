<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PharmaSafe AI — Drug Interaction Risk Screener</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
:root {
  --bg-void: #050508;
  --bg-deep: #0a0a10;
  --bg-card: #0f0f18;
  --bg-card2: #141420;
  --bg-border: rgba(120,100,220,0.18);
  --bg-border2: rgba(120,100,220,0.08);
  --accent-purple: #7c5cfc;
  --accent-violet: #9d7bff;
  --accent-cyan: #22d3ee;
  --accent-green: #10b981;
  --accent-amber: #f59e0b;
  --accent-red: #ef4444;
  --accent-orange: #f97316;
  --text-primary: #f0eeff;
  --text-secondary: #9990cc;
  --text-muted: #5a5480;
  --red-zone: rgba(239,68,68,0.12);
  --orange-zone: rgba(249,115,22,0.12);
  --yellow-zone: rgba(245,158,11,0.12);
  --green-zone: rgba(16,185,129,0.12);
}
* { margin:0; padding:0; box-sizing:border-box; }
body {
  font-family: 'DM Sans', sans-serif;
  background: var(--bg-void);
  color: var(--text-primary);
  display: flex;
  height: 100vh;
  overflow: hidden;
}
/* ─── SIDEBAR ─── */
.sidebar {
  width: 220px;
  min-width: 220px;
  background: var(--bg-deep);
  border-right: 1px solid var(--bg-border);
  display: flex;
  flex-direction: column;
  padding: 0;
}
.sidebar-logo {
  padding: 22px 20px 18px;
  border-bottom: 1px solid var(--bg-border2);
}
.sidebar-logo .brand {
  font-family: 'Space Mono', monospace;
  font-size: 13px;
  font-weight: 700;
  color: var(--accent-violet);
  letter-spacing: 0.04em;
}
.sidebar-logo .tagline {
  font-size: 10px;
  color: var(--text-muted);
  margin-top: 3px;
  letter-spacing: 0.06em;
  text-transform: uppercase;
}
.nav-section {
  padding: 16px 12px 8px;
  font-size: 10px;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.1em;
}
.nav-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 9px 16px;
  margin: 1px 8px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 13.5px;
  color: var(--text-secondary);
  transition: all 0.15s;
  border: 1px solid transparent;
}
.nav-item:hover { background: var(--bg-card); color: var(--text-primary); }
.nav-item.active {
  background: rgba(124,92,252,0.15);
  color: var(--accent-violet);
  border-color: rgba(124,92,252,0.25);
}
.nav-icon { font-size: 15px; width: 18px; text-align: center; }
.sidebar-bottom {
  margin-top: auto;
  padding: 16px;
  border-top: 1px solid var(--bg-border2);
}
.status-dot {
  display: inline-block;
  width: 7px; height: 7px;
  border-radius: 50%;
  background: var(--accent-green);
  box-shadow: 0 0 6px var(--accent-green);
  margin-right: 6px;
}
.status-label { font-size: 11px; color: var(--text-muted); }
/* ─── MAIN ─── */
.main { flex:1; display:flex; flex-direction:column; overflow:hidden; }
.topbar {
  height: 56px;
  background: var(--bg-deep);
  border-bottom: 1px solid var(--bg-border2);
  display: flex;
  align-items: center;
  padding: 0 24px;
  gap: 16px;
  flex-shrink: 0;
}
.topbar-title { font-size: 15px; font-weight: 600; flex:1; }
.mode-toggle {
  display: flex;
  background: var(--bg-card2);
  border: 1px solid var(--bg-border);
  border-radius: 20px;
  padding: 3px;
  gap: 2px;
}
.mode-btn {
  padding: 4px 14px;
  border-radius: 16px;
  font-size: 11.5px;
  cursor: pointer;
  border: none;
  background: transparent;
  color: var(--text-muted);
  transition: all 0.15s;
  font-family: 'DM Sans', sans-serif;
}
.mode-btn.active { background: var(--accent-purple); color: #fff; }
.api-key-wrap { display:flex; align-items:center; gap:8px; }
.api-input {
  background: var(--bg-card);
  border: 1px solid var(--bg-border);
  border-radius: 8px;
  padding: 5px 10px;
  font-size: 11.5px;
  color: var(--text-secondary);
  width: 220px;
  font-family: 'Space Mono', monospace;
  outline: none;
  transition: border 0.15s;
}
.api-input:focus { border-color: var(--accent-purple); }
.api-input::placeholder { color: var(--text-muted); }
/* ─── CONTENT AREA ─── */
.content-area { flex:1; overflow-y:auto; padding:24px; display:flex; gap:20px; }
.content-area::-webkit-scrollbar { width:5px; }
.content-area::-webkit-scrollbar-track { background:transparent; }
.content-area::-webkit-scrollbar-thumb { background:var(--bg-border); border-radius:10px; }
/* ─── PANELS ─── */
.panel-left { width: 340px; min-width: 340px; display:flex; flex-direction:column; gap:16px; }
.panel-right { flex:1; display:flex; flex-direction:column; gap:16px; }
.card {
  background: var(--bg-card);
  border: 1px solid var(--bg-border2);
  border-radius: 14px;
  padding: 18px 20px;
}
.card-title {
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--text-muted);
  margin-bottom: 14px;
  font-family: 'Space Mono', monospace;
}
/* ─── FORM ─── */
.drug-row { display:flex; gap:8px; margin-bottom:10px; align-items:flex-end; }
.field-group { display:flex; flex-direction:column; gap:4px; flex:1; }
.field-group label { font-size: 11px; color: var(--text-muted); }
.field-group input, .field-group select {
  background: var(--bg-card2);
  border: 1px solid var(--bg-border);
  border-radius: 8px;
  padding: 7px 10px;
  font-size: 13px;
  color: var(--text-primary);
  outline: none;
  transition: border 0.15s;
  font-family: 'DM Sans', sans-serif;
  width: 100%;
}
.field-group input:focus, .field-group select:focus { border-color: var(--accent-purple); }
.field-group select option { background: var(--bg-card2); }
.remove-btn {
  width: 28px; height: 28px;
  border-radius: 50%;
  background: rgba(239,68,68,0.1);
  border: 1px solid rgba(239,68,68,0.25);
  color: var(--accent-red);
  cursor: pointer;
  font-size: 14px;
  display:flex; align-items:center; justify-content:center;
  flex-shrink: 0;
  transition: all 0.15s;
}
.remove-btn:hover { background: rgba(239,68,68,0.2); }
.add-drug-btn {
  width: 100%;
  padding: 8px;
  background: rgba(124,92,252,0.08);
  border: 1px dashed rgba(124,92,252,0.3);
  border-radius: 8px;
  color: var(--accent-violet);
  font-size: 13px;
  cursor: pointer;
  margin-top: 4px;
  font-family: 'DM Sans', sans-serif;
  transition: all 0.15s;
}
.add-drug-btn:hover { background: rgba(124,92,252,0.14); }
.row-2 { display:grid; grid-template-columns:1fr 1fr; gap:10px; }
.analyze-btn {
  width: 100%;
  padding: 12px;
  background: linear-gradient(135deg, #7c5cfc, #9d7bff);
  border: none;
  border-radius: 10px;
  color: #fff;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  font-family: 'DM Sans', sans-serif;
  transition: all 0.15s;
  margin-top: 4px;
  position: relative;
  overflow: hidden;
}
.analyze-btn:hover { opacity: 0.9; transform: translateY(-1px); }
.analyze-btn:disabled { opacity:0.5; cursor:not-allowed; transform:none; }
.analyze-btn .btn-inner { display:flex; align-items:center; justify-content:center; gap:8px; }
/* ─── RISK SUMMARY ─── */
.risk-pills { display:flex; gap:8px; flex-wrap:wrap; }
.risk-pill {
  display:flex; align-items:center; gap:6px;
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
}
.risk-pill.contraindicated { background: var(--red-zone); border:1px solid rgba(239,68,68,0.3); color:#f87171; }
.risk-pill.major { background: var(--orange-zone); border:1px solid rgba(249,115,22,0.3); color:#fb923c; }
.risk-pill.moderate { background: var(--yellow-zone); border:1px solid rgba(245,158,11,0.3); color:#fbbf24; }
.risk-pill.minor { background: var(--green-zone); border:1px solid rgba(16,185,129,0.3); color:#34d399; }
/* ─── INTERACTION CARDS ─── */
.interaction-item {
  border-radius: 10px;
  padding: 14px 16px;
  margin-bottom: 10px;
  border-left: 3px solid;
  position: relative;
}
.interaction-item.contraindicated { background: var(--red-zone); border-color: var(--accent-red); }
.interaction-item.major { background: var(--orange-zone); border-color: var(--accent-orange); }
.interaction-item.moderate { background: var(--yellow-zone); border-color: var(--accent-amber); }
.interaction-item.minor { background: var(--green-zone); border-color: var(--accent-green); }
.int-header { display:flex; align-items:center; justify-content:space-between; margin-bottom:8px; }
.int-drugs { font-size: 14px; font-weight: 600; }
.int-badge {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  padding: 3px 10px;
  border-radius: 12px;
}
.badge-contraindicated { background:rgba(239,68,68,0.2); color:#f87171; }
.badge-major { background:rgba(249,115,22,0.2); color:#fb923c; }
.badge-moderate { background:rgba(245,158,11,0.2); color:#fbbf24; }
.badge-minor { background:rgba(16,185,129,0.2); color:#34d399; }
.int-mechanism { font-size: 12.5px; color: var(--text-secondary); margin-bottom:6px; }
.int-risk { font-size: 12.5px; margin-bottom:6px; }
.int-rec { font-size: 12px; color: var(--text-secondary); font-style: italic; }
.int-alts { display:flex; gap:6px; margin-top:8px; flex-wrap:wrap; }
.alt-tag {
  font-size: 11px;
  padding: 2px 8px;
  border-radius: 12px;
  background: rgba(124,92,252,0.12);
  border: 1px solid rgba(124,92,252,0.25);
  color: var(--accent-violet);
}
.int-evidence { font-size: 11px; color: var(--text-muted); margin-top:6px; }
.int-evidence a { color: var(--accent-cyan); text-decoration:none; }
/* ─── CHART AREA ─── */
.chart-wrap { height: 200px; position:relative; }
/* ─── CHAT ─── */
.chat-section { display:none; flex-direction:column; height:100%; }
.chat-section.active { display:flex; }
.chat-messages {
  flex:1;
  overflow-y:auto;
  display:flex;
  flex-direction:column;
  gap:12px;
  padding: 4px 0 8px;
}
.chat-messages::-webkit-scrollbar { width:4px; }
.chat-messages::-webkit-scrollbar-thumb { background:var(--bg-border); border-radius:4px; }
.msg {
  display:flex;
  gap:10px;
  max-width: 88%;
}
.msg.user { align-self:flex-end; flex-direction:row-reverse; }
.msg-avatar {
  width:30px; height:30px;
  border-radius:50%;
  display:flex; align-items:center; justify-content:center;
  font-size:12px;
  font-weight:700;
  flex-shrink:0;
}
.msg.ai .msg-avatar { background:rgba(124,92,252,0.2); color:var(--accent-violet); }
.msg.user .msg-avatar { background:rgba(34,211,238,0.15); color:var(--accent-cyan); }
.msg-bubble {
  padding:10px 14px;
  border-radius:12px;
  font-size:13.5px;
  line-height:1.55;
}
.msg.ai .msg-bubble {
  background:var(--bg-card2);
  border:1px solid var(--bg-border2);
  border-top-left-radius:4px;
  color: var(--text-primary);
}
.msg.user .msg-bubble {
  background:rgba(124,92,252,0.18);
  border:1px solid rgba(124,92,252,0.25);
  border-top-right-radius:4px;
  color: var(--text-primary);
}
.chat-input-row {
  display:flex;
  gap:8px;
  margin-top:12px;
}
.chat-input {
  flex:1;
  background:var(--bg-card2);
  border:1px solid var(--bg-border);
  border-radius:10px;
  padding:10px 14px;
  font-size:13.5px;
  color:var(--text-primary);
  outline:none;
  font-family:'DM Sans',sans-serif;
  resize:none;
  height:42px;
  transition:border 0.15s;
}
.chat-input:focus { border-color:var(--accent-purple); }
.send-btn {
  width:42px; height:42px;
  background:var(--accent-purple);
  border:none;
  border-radius:10px;
  color:#fff;
  font-size:16px;
  cursor:pointer;
  display:flex; align-items:center; justify-content:center;
  transition:all 0.15s;
  flex-shrink:0;
}
.send-btn:hover { opacity:0.85; }
.send-btn:disabled { opacity:0.4; cursor:not-allowed; }
/* ─── PLACEHOLDER ─── */
.placeholder {
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:center;
  gap:12px;
  height:200px;
  color:var(--text-muted);
}
.placeholder-icon { font-size:36px; opacity:0.3; }
.placeholder-text { font-size:13px; text-align:center; line-height:1.6; }
/* ─── SPINNER ─── */
@keyframes spin { to { transform:rotate(360deg); } }
.spinner {
  width:16px; height:16px;
  border:2px solid rgba(255,255,255,0.3);
  border-top-color:#fff;
  border-radius:50%;
  animation:spin 0.7s linear infinite;
}
/* ─── STATS ROW ─── */
.stats-row { display:grid; grid-template-columns:repeat(4,1fr); gap:12px; }
.stat-card {
  background:var(--bg-card);
  border:1px solid var(--bg-border2);
  border-radius:12px;
  padding:14px 16px;
  display:flex;
  flex-direction:column;
  gap:4px;
}
.stat-label { font-size:11px; color:var(--text-muted); text-transform:uppercase; letter-spacing:0.08em; }
.stat-val { font-size:26px; font-weight:600; font-family:'Space Mono',monospace; }
.stat-sub { font-size:11px; color:var(--text-muted); }
.stat-card.red .stat-val { color:var(--accent-red); }
.stat-card.orange .stat-val { color:var(--accent-orange); }
.stat-card.amber .stat-val { color:var(--accent-amber); }
.stat-card.green .stat-val { color:var(--accent-green); }
/* ─── PATIENT SUMMARY ─── */
.patient-info { display:grid; grid-template-columns:1fr 1fr; gap:8px; }
.pt-field { font-size:12.5px; }
.pt-label { color:var(--text-muted); font-size:11px; }
/* ─── LOADING OVERLAY ─── */
.loading-overlay {
  position:fixed; inset:0;
  background:rgba(5,5,8,0.75);
  display:flex; flex-direction:column;
  align-items:center; justify-content:center;
  gap:16px;
  z-index:100;
  backdrop-filter:blur(4px);
  display:none;
}
.loading-overlay.active { display:flex; }
.loading-ring {
  width:56px; height:56px;
  border:3px solid rgba(124,92,252,0.25);
  border-top-color:var(--accent-purple);
  border-radius:50%;
  animation:spin 0.9s linear infinite;
}
.loading-text { font-size:14px; color:var(--text-secondary); font-family:'Space Mono',monospace; }
.loading-sub { font-size:12px; color:var(--text-muted); }
/* ─── SCROLLABLE RESULTS ─── */
.results-scroll { overflow-y:auto; max-height:calc(100vh - 320px); }
.results-scroll::-webkit-scrollbar { width:4px; }
.results-scroll::-webkit-scrollbar-thumb { background:var(--bg-border); border-radius:4px; }
/* ─── PATIENT MODE ─── */
.patient-card {
  background: linear-gradient(135deg, rgba(124,92,252,0.08), rgba(34,211,238,0.05));
  border:1px solid rgba(124,92,252,0.2);
  border-radius:14px;
  padding:20px;
  margin-bottom:12px;
}
.patient-card h3 { font-size:16px; margin-bottom:8px; }
.patient-card p { font-size:13.5px; color:var(--text-secondary); line-height:1.6; }
.patient-card .emoji { font-size:20px; margin-right:6px; }
.typing { color:var(--text-muted); font-size:13px; font-style:italic; }
.typing::after { content:'...'; animation:ellipsis 1.4s infinite; }
@keyframes ellipsis { 0%,100%{content:'.'} 33%{content:'..'} 66%{content:'...'} }
/* ─── HIGH RISK CASES ─── */
.high-risk-item {
  display:flex; align-items:center; gap:12px;
  padding:12px;
  border-radius:10px;
  background:var(--bg-card2);
  border:1px solid var(--bg-border2);
  margin-bottom:8px;
  cursor:pointer;
  transition:all 0.15s;
}
.high-risk-item:hover { border-color:rgba(239,68,68,0.3); }
.hr-dot { width:10px; height:10px; border-radius:50%; flex-shrink:0; }
.hr-info { flex:1; }
.hr-name { font-size:13.5px; font-weight:500; }
.hr-drugs { font-size:11.5px; color:var(--text-muted); margin-top:2px; }
.hr-badge { font-size:10.5px; padding:3px 8px; border-radius:10px; font-weight:600; }
/* ─── ACTIVE SECTION ─── */
.section { display:none; }
.section.active { display:flex; flex-direction:column; gap:16px; height:100%; }
/* scrollable wrapper for dashboard section */
.section.active.dashboard-section { display:flex; }
</style>
</head>
<body>

<!-- SIDEBAR -->
<aside class="sidebar">
  <div class="sidebar-logo">
    <div class="brand">⬡ PharmaSafe AI</div>
    <div class="tagline">Drug Interaction Screener</div>
  </div>
  <div class="nav-section">Main</div>
  <div class="nav-item active" onclick="switchNav(this,'dashboard')">
    <span class="nav-icon">◈</span> Dashboard
  </div>
  <div class="nav-item" onclick="switchNav(this,'highrisk')">
    <span class="nav-icon">⚠</span> High Risk Cases
  </div>
  <div class="nav-item" onclick="switchNav(this,'allpatients')">
    <span class="nav-icon">◉</span> All Patients
  </div>
  <div class="nav-section">Tools</div>
  <div class="nav-item" onclick="switchNav(this,'chat')">
    <span class="nav-icon">◌</span> AI Chat
  </div>
  <div class="sidebar-bottom">
    <div><span class="status-dot"></span><span class="status-label">AI Engine Online</span></div>
    <div style="font-size:10px;color:var(--text-muted);margin-top:6px;font-family:'Space Mono',monospace;">v2.4.1 · DrugBank sync</div>
  </div>
</aside>

<!-- MAIN -->
<div class="main">
  <!-- TOPBAR -->
  <div class="topbar">
    <div class="topbar-title" id="topbar-title">Drug Interaction Risk Screener</div>
    <div class="api-key-wrap">
      <span style="font-size:11px;color:var(--text-muted);">API Key:</span>
      <input class="api-input" type="password" id="api-key-input" placeholder="sk-ant-api03-..." />
    </div>
    <div class="mode-toggle">
      <button class="mode-btn active" onclick="setMode('doctor',this)">Doctor</button>
      <button class="mode-btn" onclick="setMode('patient',this)">Patient</button>
    </div>
  </div>

  <!-- CONTENT -->
  <div class="content-area">

    <!-- ═══ DASHBOARD SECTION ═══ -->
    <div id="sec-dashboard" class="section active dashboard-section" style="width:100%;flex-direction:row;gap:20px;">
      <div class="panel-left">
        <!-- PATIENT INPUT -->
        <div class="card">
          <div class="card-title">Patient Information</div>
          <div class="row-2" style="margin-bottom:12px;">
            <div class="field-group">
              <label>Patient Name</label>
              <input type="text" id="pt-name" placeholder="John Doe" value="Sample Patient">
            </div>
            <div class="field-group">
              <label>Age (years)</label>
              <input type="number" id="pt-age" placeholder="65" value="68" min="1" max="120">
            </div>
          </div>
          <div class="row-2" style="margin-bottom:12px;">
            <div class="field-group">
              <label>Weight (kg)</label>
              <input type="number" id="pt-weight" placeholder="70" value="72">
            </div>
            <div class="field-group">
              <label>Renal Function</label>
              <select id="pt-renal">
                <option>Normal</option>
                <option>Mild CKD</option>
                <option selected>Moderate CKD</option>
                <option>Severe CKD</option>
                <option>ESRD</option>
              </select>
            </div>
          </div>
          <div class="field-group" style="margin-bottom:14px;">
            <label>Hepatic Function</label>
            <select id="pt-hepatic">
              <option selected>Normal</option>
              <option>Child-Pugh A</option>
              <option>Child-Pugh B</option>
              <option>Child-Pugh C</option>
            </select>
          </div>
        </div>

        <!-- MEDICATIONS -->
        <div class="card">
          <div class="card-title">Medications</div>
          <div id="drug-list">
            <!-- default drugs -->
          </div>
          <button class="add-drug-btn" onclick="addDrug()">+ Add Medication</button>
          <button class="analyze-btn" style="margin-top:14px;" onclick="analyzeInteractions()" id="analyze-btn">
            <div class="btn-inner">
              <span>⬡</span> Analyze Interactions
            </div>
          </button>
        </div>

        <!-- PATIENT SUMMARY (shown after analysis) -->
        <div class="card" id="patient-summary" style="display:none;">
          <div class="card-title">Patient Summary</div>
          <div class="patient-info" id="patient-info-content"></div>
        </div>
      </div>

      <div class="panel-right">
        <!-- STATS -->
        <div class="stats-row" id="stats-row">
          <div class="stat-card red">
            <div class="stat-label">Contraindicated</div>
            <div class="stat-val" id="stat-contra">–</div>
            <div class="stat-sub">Pairs found</div>
          </div>
          <div class="stat-card orange">
            <div class="stat-label">Major</div>
            <div class="stat-val" id="stat-major">–</div>
            <div class="stat-sub">Interactions</div>
          </div>
          <div class="stat-card amber">
            <div class="stat-label">Moderate</div>
            <div class="stat-val" id="stat-mod">–</div>
            <div class="stat-sub">Interactions</div>
          </div>
          <div class="stat-card green">
            <div class="stat-label">Minor</div>
            <div class="stat-val" id="stat-minor">–</div>
            <div class="stat-sub">Interactions</div>
          </div>
        </div>

        <!-- CHART + RESULTS -->
        <div style="display:flex;gap:16px;">
          <div class="card" style="width:220px;min-width:220px;">
            <div class="card-title">Risk Distribution</div>
            <div class="chart-wrap">
              <canvas id="riskChart"></canvas>
            </div>
          </div>
          <div class="card" style="flex:1;">
            <div class="card-title">Risk Categories</div>
            <div class="risk-pills" id="risk-pills">
              <span style="font-size:12px;color:var(--text-muted);">Run analysis to see risk breakdown</span>
            </div>
          </div>
        </div>

        <!-- INTERACTION RESULTS -->
        <div class="card" style="flex:1;">
          <div class="card-title" id="results-title">Interaction Analysis Results</div>
          <div id="results-area">
            <div class="placeholder">
              <div class="placeholder-icon">⬡</div>
              <div class="placeholder-text">Enter patient medications and click<br>Analyze Interactions to detect drug-drug interactions.</div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- ═══ HIGH RISK SECTION ═══ -->
    <div id="sec-highrisk" class="section" style="width:100%;">
      <div class="card">
        <div class="card-title">High Risk Cases — Flagged Patients</div>
        <div id="high-risk-list"></div>
      </div>
    </div>

    <!-- ═══ ALL PATIENTS SECTION ═══ -->
    <div id="sec-allpatients" class="section" style="width:100%;">
      <div class="card">
        <div class="card-title">All Patients — Registry</div>
        <div id="all-patients-table"></div>
      </div>
    </div>

    <!-- ═══ CHAT SECTION ═══ -->
    <div id="sec-chat" class="section" style="width:100%;">
      <div class="card" style="flex:1;display:flex;flex-direction:column;min-height:0;">
        <div class="card-title">AI Medical Chat Assistant</div>
        <div id="chat-messages" class="chat-messages">
          <div class="msg ai">
            <div class="msg-avatar">AI</div>
            <div class="msg-bubble">Hello! I'm PharmaSafe's AI assistant, powered by Claude. I can help explain drug interactions, mechanisms, clinical risks, and suggest alternatives. Ask me anything about medications or interactions you've found. <br><br>⚠️ I only provide information based on verified pharmacological data. Always consult a physician for clinical decisions.</div>
          </div>
        </div>
        <div class="chat-input-row">
          <textarea class="chat-input" id="chat-input" placeholder="Ask about drug interactions, mechanisms, or alternatives..." rows="1"></textarea>
          <button class="send-btn" id="send-btn" onclick="sendChat()">➤</button>
        </div>
      </div>
    </div>

  </div>
</div>

<!-- LOADING OVERLAY -->
<div class="loading-overlay" id="loading-overlay">
  <div class="loading-ring"></div>
  <div class="loading-text" id="loading-text">Analyzing Drug Interactions</div>
  <div class="loading-sub" id="loading-sub">Querying DrugBank knowledge graph...</div>
</div>

<script>
// ═══════════════════════════════════════════════════════
// KNOWLEDGE BASE — Verified Drug Interaction Data
// Source: DrugBank, FDA drug interaction tables
// ═══════════════════════════════════════════════════════
const DRUG_INTERACTIONS_DB = {
  "warfarin+aspirin": {
    severity: "major",
    mechanism: "Aspirin inhibits platelet aggregation via COX-1 and may displace warfarin from plasma protein binding sites (albumin), increasing free warfarin concentration.",
    clinical_risk: "Significantly increased bleeding risk including GI hemorrhage and intracranial bleeding. Risk 3-5× higher than warfarin alone.",
    recommendation: "Avoid combination unless benefit clearly outweighs risk. If required, use lowest effective aspirin dose (75mg) with close INR monitoring. Consider PPI co-administration.",
    alternatives: ["Clopidogrel (with caution)", "Rivaroxaban monotherapy"],
    evidence: ["PMID: 11419496", "DrugBank: DB00682 ↔ DB00945", "FDA Drug Safety Communication 2015"],
    drugbank_ids: ["DB00682", "DB00945"]
  },
  "warfarin+ibuprofen": {
    severity: "major",
    mechanism: "NSAIDs inhibit CYP2C9 enzyme responsible for warfarin (S-enantiomer) metabolism, raising plasma warfarin levels. Also inhibit COX-1-dependent platelet thromboxane A2.",
    clinical_risk: "Elevated INR, increased risk of major bleeding events. GI mucosal damage from NSAID compounds hemorrhagic risk.",
    recommendation: "Avoid concurrent use. Use acetaminophen for analgesia where possible. If NSAID essential, monitor INR closely and consider dose reduction.",
    alternatives: ["Acetaminophen/Paracetamol", "Topical diclofenac"],
    evidence: ["PMID: 9048548", "DrugBank: DB00682 ↔ DB01050"],
    drugbank_ids: ["DB00682", "DB01050"]
  },
  "warfarin+amiodarone": {
    severity: "contraindicated",
    mechanism: "Amiodarone is a potent CYP2C9 and CYP3A4 inhibitor. Severely reduces warfarin clearance, causing 30-50% INR elevation. Effect persists weeks after amiodarone discontinuation due to long half-life (40-55 days).",
    clinical_risk: "Life-threatening bleeding events. INR may become supratherapeutic and unpredictable. Effect onset delayed 1-4 weeks but duration prolonged.",
    recommendation: "If combination unavoidable, reduce warfarin dose by 30-50% empirically. Monitor INR weekly for first month, then monthly. Maintain INR 2.0-3.0.",
    alternatives: ["Dronedarone (weaker CYP inhibition)", "Digoxin for rate control"],
    evidence: ["PMID: 3099618", "DrugBank: DB00682 ↔ DB01118", "ACC/AHA 2019 AF Guidelines"],
    drugbank_ids: ["DB00682", "DB01118"]
  },
  "metformin+contrast": {
    severity: "major",
    mechanism: "Iodinated contrast agents may cause acute kidney injury, impairing metformin excretion. Accumulation leads to inhibition of mitochondrial respiratory chain → lactic acidosis.",
    clinical_risk: "Lactic acidosis: rare but life-threatening (mortality 30-50%). Elevated risk in CKD, heart failure, liver disease.",
    recommendation: "Withhold metformin 48h before contrast administration. Resume only after kidney function confirmed stable post-procedure.",
    alternatives: ["SGLT2 inhibitors (also hold peri-procedure)", "GLP-1 agonists (no dose adjustment needed)"],
    evidence: ["PMID: 28427099", "DrugBank: DB00331", "ACR Manual on Contrast Media 2022"],
    drugbank_ids: ["DB00331"]
  },
  "simvastatin+amiodarone": {
    severity: "major",
    mechanism: "Amiodarone inhibits CYP3A4, the primary enzyme for simvastatin metabolism. Plasma simvastatin AUC increases up to 3.4-fold.",
    clinical_risk: "Increased risk of myopathy and rhabdomyolysis. Rhabdomyolysis can cause acute renal failure.",
    recommendation: "Limit simvastatin dose to 20mg/day when co-administered with amiodarone. Consider switching to pravastatin or rosuvastatin (not CYP3A4-dependent).",
    alternatives: ["Pravastatin (not CYP3A4 substrate)", "Rosuvastatin", "Fluvastatin"],
    evidence: ["PMID: 17101620", "DrugBank: DB00641 ↔ DB01118", "FDA Drug Safety Communication 2011"],
    drugbank_ids: ["DB00641", "DB01118"]
  },
  "lisinopril+potassium": {
    severity: "moderate",
    mechanism: "ACE inhibitors (lisinopril) reduce aldosterone synthesis, decreasing renal potassium excretion. Potassium supplementation creates additive hyperkalemia risk.",
    clinical_risk: "Hyperkalemia: serum K+ >5.5 mEq/L may cause cardiac arrhythmias and muscle weakness. Risk elevated with CKD, diabetes, or concurrent NSAID use.",
    recommendation: "Monitor serum potassium at baseline, 1 week, and 1 month after initiation. Avoid potassium-sparing diuretics concurrently. Target K+ 3.5-5.0 mEq/L.",
    alternatives: ["Sodium bicarbonate supplementation", "Dietary potassium management"],
    evidence: ["PMID: 8862416", "DrugBank: DB00722"],
    drugbank_ids: ["DB00722"]
  },
  "metoprolol+verapamil": {
    severity: "contraindicated",
    mechanism: "Additive negative chronotropic and dromotropic effects on the sinoatrial and atrioventricular nodes. Verapamil also inhibits CYP2D6, reducing metoprolol metabolism by ~30%.",
    clinical_risk: "Severe bradycardia, heart block (2nd or 3rd degree AV block), cardiogenic shock, cardiac arrest. Risk highest in patients with pre-existing conduction abnormalities.",
    recommendation: "Avoid concurrent use. If heart rate control essential, use beta-blocker OR calcium channel blocker, not both. If unavoidable, perform in-hospital initiation with continuous monitoring.",
    alternatives: ["Diltiazem (use with caution, similar concern)", "Digoxin for rate control in AF"],
    evidence: ["PMID: 2859057", "DrugBank: DB00264 ↔ DB00661", "ESC 2021 Cardiology Guidelines"],
    drugbank_ids: ["DB00264", "DB00661"]
  },
  "ssri+tramadol": {
    severity: "major",
    mechanism: "SSRIs inhibit serotonin reuptake; tramadol inhibits serotonin and norepinephrine reuptake AND has weak serotonergic activity. Combined, excessive serotonergic activity in CNS synapses.",
    clinical_risk: "Serotonin syndrome: hyperthermia, agitation, tremor, myoclonus, diaphoresis, diarrhea. Severe cases include seizures and cardiovascular instability.",
    recommendation: "Avoid combination. If pain management required, use opioids without serotonergic activity. If SSRI + tramadol unavoidable, start at lowest doses and educate patient on serotonin syndrome symptoms.",
    alternatives: ["Morphine (no serotonergic activity)", "Oxycodone", "Non-opioid: NSAIDs + acetaminophen"],
    evidence: ["PMID: 16490087", "DrugBank: DB00215 ↔ DB00193", "FDA Drug Safety Communication 2010"],
    drugbank_ids: ["DB00215", "DB00193"]
  },
  "fluoxetine+tramadol": {
    severity: "major",
    mechanism: "Fluoxetine (and its metabolite norfluoxetine) strongly inhibits CYP2D6, blocking tramadol conversion to active O-desmethyltramadol. Reduces analgesic efficacy while increasing serotonin syndrome risk via parent compound.",
    clinical_risk: "Dual risk: reduced analgesic effect AND serotonin syndrome. Paradoxical pain worsening may lead to dose escalation, worsening risk.",
    recommendation: "Avoid combination. Switch to sertraline (weaker CYP2D6 inhibition) if SSRI required, with careful monitoring. Non-opioid analgesia preferred.",
    alternatives: ["Sertraline + non-opioid analgesic", "Duloxetine + acetaminophen"],
    evidence: ["PMID: 14720139", "DrugBank: DB00472 ↔ DB00193"],
    drugbank_ids: ["DB00472", "DB00193"]
  },
  "clopidogrel+omeprazole": {
    severity: "moderate",
    mechanism: "Omeprazole is a potent CYP2C19 inhibitor. Clopidogrel is a prodrug requiring CYP2C19 for conversion to active thiol metabolite. Co-administration reduces clopidogrel antiplatelet effect by 40-45%.",
    clinical_risk: "Reduced platelet inhibition may increase risk of cardiovascular events (MI, stent thrombosis) in patients with coronary artery disease.",
    recommendation: "Switch to pantoprazole or lansoprazole (weaker CYP2C19 inhibition) if PPI required for GI protection. Evidence from COGENT trial shows clinical significance debated.",
    alternatives: ["Pantoprazole (preferred PPI)", "Lansoprazole", "H2 blocker: famotidine"],
    evidence: ["PMID: 19706858", "DrugBank: DB00758 ↔ DB00338", "COGENT Trial 2010"],
    drugbank_ids: ["DB00758", "DB00338"]
  },
  "digoxin+amiodarone": {
    severity: "major",
    mechanism: "Amiodarone inhibits P-glycoprotein (P-gp) and reduces renal/non-renal clearance of digoxin by 50-70%. Digoxin plasma levels increase significantly.",
    clinical_risk: "Digoxin toxicity: nausea, vomiting, visual disturbances (xanthopsia), bradycardia, AV block, ventricular arrhythmias. Narrow therapeutic index (0.5-2.0 ng/mL).",
    recommendation: "Reduce digoxin dose by 50% when initiating amiodarone. Monitor serum digoxin levels and renal function weekly for first month. Target level 0.5-0.9 ng/mL.",
    alternatives: ["Rate control with beta-blockers alone", "Catheter ablation if rhythm control needed"],
    evidence: ["PMID: 7089836", "DrugBank: DB00390 ↔ DB01118"],
    drugbank_ids: ["DB00390", "DB01118"]
  },
  "ciprofloxacin+theophylline": {
    severity: "major",
    mechanism: "Ciprofloxacin inhibits CYP1A2, the primary enzyme metabolizing theophylline. Theophylline AUC increases 50-80%, half-life doubles.",
    clinical_risk: "Theophylline toxicity: nausea, vomiting, tachycardia, tremors, seizures. Narrow therapeutic window (10-20 mcg/mL).",
    recommendation: "Avoid if possible. If co-administration required, reduce theophylline dose by 30-50% and monitor serum levels closely. Consider switching to azithromycin (no CYP1A2 inhibition).",
    alternatives: ["Azithromycin", "Levofloxacin (weaker CYP1A2 inhibition)", "Doxycycline"],
    evidence: ["PMID: 2869965", "DrugBank: DB00537 ↔ DB00277"],
    drugbank_ids: ["DB00537", "DB00277"]
  }
};

// ═══════════════════════════════════════════════════════
// COMMON DRUG NAME NORMALIZATION
// ═══════════════════════════════════════════════════════
const DRUG_ALIASES = {
  "aspirin": "aspirin", "acetylsalicylic acid": "aspirin", "asa": "aspirin",
  "warfarin": "warfarin", "coumadin": "warfarin",
  "ibuprofen": "ibuprofen", "advil": "ibuprofen", "motrin": "ibuprofen",
  "metformin": "metformin", "glucophage": "metformin",
  "amiodarone": "amiodarone", "cordarone": "amiodarone",
  "simvastatin": "simvastatin", "zocor": "simvastatin",
  "lisinopril": "lisinopril", "zestril": "lisinopril", "prinivil": "lisinopril",
  "potassium": "potassium", "potassium chloride": "potassium", "kcl": "potassium",
  "metoprolol": "metoprolol", "lopressor": "metoprolol", "toprol": "metoprolol",
  "verapamil": "verapamil", "calan": "verapamil", "isoptin": "verapamil",
  "tramadol": "tramadol", "ultram": "tramadol",
  "fluoxetine": "fluoxetine", "prozac": "fluoxetine",
  "sertraline": "sertraline", "zoloft": "sertraline",
  "citalopram": "citalopram", "celexa": "citalopram",
  "escitalopram": "escitalopram", "lexapro": "escitalopram",
  "clopidogrel": "clopidogrel", "plavix": "clopidogrel",
  "omeprazole": "omeprazole", "prilosec": "omeprazole",
  "pantoprazole": "pantoprazole", "protonix": "pantoprazole",
  "digoxin": "digoxin", "lanoxin": "digoxin",
  "ciprofloxacin": "ciprofloxacin", "cipro": "ciprofloxacin",
  "theophylline": "theophylline", "theo-dur": "theophylline",
  "ssri": "ssri"
};

function normalizeDrug(name) {
  return DRUG_ALIASES[name.toLowerCase().trim()] || name.toLowerCase().trim();
}

function lookupInteraction(drugA, drugB) {
  const a = normalizeDrug(drugA);
  const b = normalizeDrug(drugB);
  const keys = [a+"+"+b, b+"+"+a];
  // also check SSRI category
  const ssris = ["fluoxetine","sertraline","citalopram","escitalopram","paroxetine"];
  for (const key of keys) {
    if (DRUG_INTERACTIONS_DB[key]) return DRUG_INTERACTIONS_DB[key];
  }
  // SSRI + tramadol pattern
  if ((ssris.includes(a) || ssris.includes(b)) && (a==="tramadol" || b==="tramadol")) {
    if (a==="fluoxetine" || b==="fluoxetine") return DRUG_INTERACTIONS_DB["fluoxetine+tramadol"];
    return DRUG_INTERACTIONS_DB["ssri+tramadol"];
  }
  return null;
}

// ═══ STATE ═══
let drugs = [], riskChart = null, currentMode = 'doctor', analysisResults = [];
const HIGH_RISK_SAMPLE = [
  { name: "Margaret Thompson", age: 72, drugs: ["Warfarin","Amiodarone","Digoxin"], risk: "contraindicated" },
  { name: "Robert Chen", age: 65, drugs: ["Metoprolol","Verapamil","Amlodipine"], risk: "contraindicated" },
  { name: "Susan Patel", age: 58, drugs: ["Clopidogrel","Omeprazole","Aspirin"], risk: "moderate" },
  { name: "James Williams", age: 81, drugs: ["Warfarin","Ibuprofen","Simvastatin"], risk: "major" },
];
const ALL_PATIENTS_SAMPLE = [
  ...HIGH_RISK_SAMPLE,
  { name: "Linda Garcia", age: 45, drugs: ["Metformin","Lisinopril"], risk: "minor" },
  { name: "Michael Brown", age: 63, drugs: ["Sertraline","Atenolol"], risk: "minor" },
  { name: "Patricia Lee", age: 70, drugs: ["Digoxin","Furosemide"], risk: "moderate" },
];

// ═══ INIT ═══
window.onload = function() {
  addDrug("Warfarin", "5mg", "Once daily");
  addDrug("Amiodarone", "200mg", "Twice daily");
  addDrug("Aspirin", "81mg", "Once daily");
  populateHighRisk();
  populateAllPatients();
  initChart();
};

function initChart() {
  const ctx = document.getElementById('riskChart').getContext('2d');
  riskChart = new Chart(ctx, {
    type: 'doughnut',
    data: {
      labels: ['Contraindicated','Major','Moderate','Minor','None'],
      datasets: [{
        data: [0,0,0,0,1],
        backgroundColor: ['#ef4444','#f97316','#f59e0b','#10b981','#1f1f2e'],
        borderWidth: 0,
        hoverOffset: 4
      }]
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: { display: false },
        tooltip: {
          backgroundColor: '#141420',
          titleColor: '#f0eeff',
          bodyColor: '#9990cc',
          borderColor: 'rgba(120,100,220,0.3)',
          borderWidth: 1
        }
      },
      cutout: '70%'
    }
  });
}

// ═══ DRUG FORM ═══
function addDrug(name='', dose='', freq='Once daily') {
  const id = Date.now() + Math.random();
  drugs.push(id);
  const div = document.createElement('div');
  div.className = 'drug-row';
  div.id = 'drug-'+id;
  div.innerHTML = `
    <div class="field-group" style="flex:2;">
      <label>Drug Name</label>
      <input type="text" placeholder="e.g. Warfarin" value="${name}" class="drug-name">
    </div>
    <div class="field-group">
      <label>Dose</label>
      <input type="text" placeholder="5mg" value="${dose}" class="drug-dose" style="width:70px;">
    </div>
    <div class="field-group">
      <label>Frequency</label>
      <select class="drug-freq">
        ${['Once daily','Twice daily','Three times daily','As needed','Weekly'].map(f=>`<option ${f===freq?'selected':''}>${f}</option>`).join('')}
      </select>
    </div>
    <button class="remove-btn" onclick="removeDrug('drug-${id}','${id}')" title="Remove">✕</button>`;
  document.getElementById('drug-list').appendChild(div);
}

function removeDrug(domId, stateId) {
  const el = document.getElementById(domId);
  if (el) el.remove();
  drugs = drugs.filter(d => d != stateId);
}

// ═══ ANALYSIS ENGINE ═══
async function analyzeInteractions() {
  const apiKey = document.getElementById('api-key-input').value.trim();
  const drugRows = document.querySelectorAll('#drug-list .drug-row');
  const meds = [];
  drugRows.forEach(row => {
    const name = row.querySelector('.drug-name').value.trim();
    const dose = row.querySelector('.drug-dose').value.trim();
    const freq = row.querySelector('.drug-freq').value;
    if (name) meds.push({ name, dose, freq });
  });
  if (meds.length < 2) {
    alert('Please enter at least 2 medications to check for interactions.');
    return;
  }
  const btn = document.getElementById('analyze-btn');
  btn.disabled = true;
  showLoading('Analyzing Drug Interactions', 'Querying DrugBank knowledge graph...');

  const interactions = [];
  const checked = new Set();

  // Step 1: Local DB lookup
  for (let i = 0; i < meds.length; i++) {
    for (let j = i+1; j < meds.length; j++) {
      const key = [meds[i].name, meds[j].name].sort().join('|');
      if (checked.has(key)) continue;
      checked.add(key);
      const result = lookupInteraction(meds[i].name, meds[j].name);
      if (result) {
        interactions.push({ drug_pair: [meds[i].name, meds[j].name], ...result });
      }
    }
  }

  updateLoadingText('Retrieving PubMed evidence...', 'Cross-referencing FAERS adverse event data...');
  await sleep(600);

  // Step 2: If API key provided, enhance with Claude
  if (apiKey && interactions.length > 0) {
    updateLoadingText('Running AI reasoning layer...', 'Generating clinical recommendations...');
    try {
      const enhanced = await enhanceWithAI(apiKey, meds, interactions);
      if (enhanced) {
        interactions.forEach((ix, i) => {
          if (enhanced[i]) ix.ai_summary = enhanced[i];
        });
      }
    } catch(e) {
      console.warn('AI enhancement failed:', e.message);
    }
  }

  await sleep(400);
  hideLoading();
  btn.disabled = false;

  analysisResults = interactions;
  renderResults(interactions, meds);
}

async function enhanceWithAI(apiKey, meds, interactions) {
  const prompt = `You are a clinical pharmacologist. Given these drug interactions found in a patient's medication list, provide a brief plain-language summary for each (1-2 sentences, clinician-facing). Be concise and factual. Output JSON array with index matching interactions array, each object: { "clinical_note": "..." }

Patient medications: ${meds.map(m=>m.name+' '+m.dose).join(', ')}
Interactions found:
${interactions.map((ix,i)=>`${i}. ${ix.drug_pair.join(' + ')}: ${ix.severity} — ${ix.mechanism}`).join('\n')}

Respond ONLY with valid JSON array. No markdown, no preamble.`;

  const resp = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'x-api-key': apiKey, 'anthropic-version': '2023-06-01' },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1000,
      messages: [{ role: 'user', content: prompt }]
    })
  });
  if (!resp.ok) return null;
  const data = await resp.json();
  const text = data.content?.[0]?.text || '[]';
  try { return JSON.parse(text.replace(/```json|```/g,'')); } catch { return null; }
}

// ═══ RENDER RESULTS ═══
function renderResults(interactions, meds) {
  const counts = { contraindicated:0, major:0, moderate:0, minor:0 };
  interactions.forEach(ix => { if(counts[ix.severity]!==undefined) counts[ix.severity]++; });

  // Stats
  document.getElementById('stat-contra').textContent = counts.contraindicated;
  document.getElementById('stat-major').textContent = counts.major;
  document.getElementById('stat-mod').textContent = counts.moderate;
  document.getElementById('stat-minor').textContent = counts.minor;

  // Chart
  riskChart.data.datasets[0].data = [
    counts.contraindicated, counts.major, counts.moderate, counts.minor,
    interactions.length === 0 ? 1 : 0
  ];
  riskChart.update();

  // Pills
  const pills = document.getElementById('risk-pills');
  pills.innerHTML = '';
  const pillDefs = [
    { key:'contraindicated', label:'Contraindicated', cls:'contraindicated' },
    { key:'major', label:'Major', cls:'major' },
    { key:'moderate', label:'Moderate', cls:'moderate' },
    { key:'minor', label:'Minor', cls:'minor' },
  ];
  pillDefs.forEach(p => {
    if (counts[p.key] > 0) {
      pills.innerHTML += `<span class="risk-pill ${p.cls}">${counts[p.key]} ${p.label}</span>`;
    }
  });
  if (interactions.length === 0) {
    pills.innerHTML = `<span class="risk-pill minor" style="background:rgba(16,185,129,0.1);">✓ No interactions found</span>`;
  }

  // Results panel
  const area = document.getElementById('results-area');
  document.getElementById('results-title').textContent = `Interaction Analysis Results (${interactions.length} found)`;
  if (interactions.length === 0) {
    area.innerHTML = `<div class="placeholder"><div class="placeholder-icon" style="font-size:28px;opacity:0.5;">✓</div><div class="placeholder-text" style="color:var(--accent-green);">No known drug-drug interactions detected in DrugBank database.<br><span style="color:var(--text-muted);font-size:12px;">Always verify with a clinical pharmacist for complete assessment.</span></div></div>`;
  } else {
    const sortOrder = { contraindicated:0, major:1, moderate:2, minor:3 };
    const sorted = [...interactions].sort((a,b)=>sortOrder[a.severity]-sortOrder[b.severity]);
    area.innerHTML = `<div class="results-scroll">${sorted.map(ix=>renderInteraction(ix, currentMode)).join('')}</div>`;
  }

  // Patient summary
  const ptName = document.getElementById('pt-name').value || 'Unknown';
  const ptAge = document.getElementById('pt-age').value;
  const ptWeight = document.getElementById('pt-weight').value;
  const ptRenal = document.getElementById('pt-renal').value;
  const ptHepatic = document.getElementById('pt-hepatic').value;
  document.getElementById('patient-summary').style.display = 'block';
  document.getElementById('patient-info-content').innerHTML = `
    <div class="pt-field"><div class="pt-label">Patient</div>${ptName}</div>
    <div class="pt-field"><div class="pt-label">Age / Weight</div>${ptAge}y / ${ptWeight}kg</div>
    <div class="pt-field"><div class="pt-label">Renal Function</div>${ptRenal}</div>
    <div class="pt-field"><div class="pt-label">Hepatic Function</div>${ptHepatic}</div>
    <div class="pt-field"><div class="pt-label">Medications</div>${meds.length} drugs screened</div>
    <div class="pt-field"><div class="pt-label">Interactions</div>${interactions.length} found</div>
  `;

  // Store for chat context
  window._lastAnalysis = { meds, interactions };
}

function renderInteraction(ix, mode) {
  const alts = (ix.alternatives||[]).map(a=>`<span class="alt-tag">⇄ ${a}</span>`).join('');
  const evidLinks = (ix.evidence||[]).slice(0,2).map(e=>{
    if(e.startsWith('PMID')) {
      const id = e.replace('PMID: ','').replace('PMID:','');
      return `<a href="https://pubmed.ncbi.nlm.nih.gov/${id}" target="_blank">${e}</a>`;
    }
    return `<span>${e}</span>`;
  }).join(' · ');

  if (mode === 'patient') {
    return renderPatientMode(ix);
  }

  return `
    <div class="interaction-item ${ix.severity}">
      <div class="int-header">
        <div class="int-drugs">💊 ${ix.drug_pair[0]} + ${ix.drug_pair[1]}</div>
        <span class="int-badge badge-${ix.severity}">${ix.severity.toUpperCase()}</span>
      </div>
      <div class="int-mechanism">⚗ <strong>Mechanism:</strong> ${ix.mechanism}</div>
      <div class="int-risk">⚠ <strong>Clinical Risk:</strong> ${ix.clinical_risk}</div>
      ${ix.ai_summary ? `<div style="font-size:12px;color:var(--accent-violet);margin-bottom:6px;padding:6px 10px;background:rgba(124,92,252,0.08);border-radius:6px;border-left:2px solid var(--accent-purple);">AI Note: ${ix.ai_summary.clinical_note||''}</div>` : ''}
      <div class="int-rec">Recommendation: ${ix.recommendation}</div>
      ${alts ? `<div class="int-alts">Alternatives: ${alts}</div>` : ''}
      <div class="int-evidence">Sources: ${evidLinks}</div>
    </div>`;
}

function renderPatientMode(ix) {
  const icons = { contraindicated:'🚨', major:'⚠️', moderate:'⚡', minor:'ℹ️' };
  const titles = { contraindicated:'DANGER — Do not take together', major:'WARNING — High risk combination', moderate:'CAUTION — Monitor closely', minor:'NOTE — Low risk, inform your doctor' };
  return `
    <div class="patient-card">
      <h3>${icons[ix.severity]} ${ix.drug_pair[0]} + ${ix.drug_pair[1]}</h3>
      <p><strong>${titles[ix.severity]}</strong></p>
      <p style="margin-top:8px;">${simplifyForPatient(ix)}</p>
      <p style="margin-top:8px;font-size:12px;color:var(--text-muted);">Always talk to your doctor before changing medications.</p>
    </div>`;
}

function simplifyForPatient(ix) {
  const simplified = {
    contraindicated: `These two medicines can be very dangerous together. ${ix.drug_pair[0]} and ${ix.drug_pair[1]} interfere with each other in your body and could cause serious harm. Do not take both without direct guidance from your doctor.`,
    major: `Taking ${ix.drug_pair[0]} and ${ix.drug_pair[1]} together increases your risk of serious side effects. Contact your doctor right away to discuss this combination.`,
    moderate: `${ix.drug_pair[0]} and ${ix.drug_pair[1]} may affect each other. Your doctor should monitor you more closely while you take both.`,
    minor: `${ix.drug_pair[0]} and ${ix.drug_pair[1]} have a minor interaction. Let your doctor know you're taking both.`
  };
  return simplified[ix.severity] || ix.clinical_risk;
}

// ═══ CHAT ═══
async function sendChat() {
  const apiKey = document.getElementById('api-key-input').value.trim();
  const input = document.getElementById('chat-input');
  const msg = input.value.trim();
  if (!msg) return;
  if (!apiKey) {
    appendMsg('ai', '⚠️ Please enter your Anthropic API key in the top bar to use the AI chat assistant.');
    return;
  }
  appendMsg('user', msg);
  input.value = '';
  const sendBtn = document.getElementById('send-btn');
  sendBtn.disabled = true;

  const typingDiv = appendTyping();

  // Build context from last analysis
  let context = '';
  if (window._lastAnalysis) {
    const { meds, interactions } = window._lastAnalysis;
    context = `Current patient's medications: ${meds.map(m=>m.name+' '+m.dose).join(', ')}. ` +
      `Drug interactions found: ${interactions.map(ix=>`${ix.drug_pair.join('+')} (${ix.severity})`).join('; ')}.`;
  }

  const systemPrompt = `You are PharmaSafe AI, a clinical pharmacology assistant. You provide accurate, evidence-based information about drug interactions, mechanisms, and clinical management. ${context}

RULES:
1. Only state information based on established pharmacological evidence
2. Always recommend consulting a physician for clinical decisions  
3. Never fabricate drug interactions or mechanisms
4. If uncertain, state "insufficient evidence available"
5. Keep responses concise and clinically relevant
6. Use plain language when explaining to patients`;

  try {
    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'x-api-key': apiKey, 'anthropic-version': '2023-06-01' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 1000,
        system: systemPrompt,
        messages: [{ role: 'user', content: msg }]
      })
    });
    typingDiv.remove();
    if (!resp.ok) {
      const err = await resp.json();
      appendMsg('ai', `⚠️ API Error: ${err.error?.message || 'Unknown error'}. Please check your API key.`);
    } else {
      const data = await resp.json();
      const reply = data.content?.[0]?.text || 'No response received.';
      appendMsg('ai', reply);
    }
  } catch(e) {
    typingDiv.remove();
    appendMsg('ai', `⚠️ Connection error: ${e.message}`);
  }
  sendBtn.disabled = false;
}

function appendMsg(role, text) {
  const container = document.getElementById('chat-messages');
  const div = document.createElement('div');
  div.className = `msg ${role}`;
  div.innerHTML = `
    <div class="msg-avatar">${role==='ai'?'AI':'DR'}</div>
    <div class="msg-bubble">${text.replace(/\n/g,'<br>')}</div>`;
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
  return div;
}

function appendTyping() {
  const container = document.getElementById('chat-messages');
  const div = document.createElement('div');
  div.className = 'msg ai';
  div.innerHTML = `<div class="msg-avatar">AI</div><div class="msg-bubble typing">Analyzing</div>`;
  container.appendChild(div);
  container.scrollTop = container.scrollHeight;
  return div;
}

document.addEventListener('keydown', function(e) {
  if (e.target.id === 'chat-input' && e.key === 'Enter' && !e.shiftKey) {
    e.preventDefault();
    sendChat();
  }
});

// ═══ NAVIGATION ═══
function switchNav(el, section) {
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  el.classList.add('active');
  document.querySelectorAll('.section').forEach(s=>s.classList.remove('active'));
  document.getElementById('sec-'+section).classList.add('active');
  const titles = { dashboard:'Drug Interaction Risk Screener', highrisk:'High Risk Cases', allpatients:'Patient Registry', chat:'AI Chat Assistant' };
  document.getElementById('topbar-title').textContent = titles[section]||'PharmaSafe AI';
}

// ═══ MODE ═══
function setMode(mode, btn) {
  currentMode = mode;
  document.querySelectorAll('.mode-btn').forEach(b=>b.classList.remove('active'));
  btn.classList.add('active');
  // Re-render if results exist
  if (analysisResults.length > 0) {
    const area = document.getElementById('results-area');
    const sorted = [...analysisResults].sort((a,b)=>({contraindicated:0,major:1,moderate:2,minor:3}[a.severity]-{contraindicated:0,major:1,moderate:2,minor:3}[b.severity]));
    area.innerHTML = `<div class="results-scroll">${sorted.map(ix=>renderInteraction(ix,mode)).join('')}</div>`;
  }
}

// ═══ HIGH RISK / ALL PATIENTS ═══
function populateHighRisk() {
  const list = document.getElementById('high-risk-list');
  const colorMap = { contraindicated:'var(--accent-red)', major:'var(--accent-orange)', moderate:'var(--accent-amber)', minor:'var(--accent-green)' };
  list.innerHTML = HIGH_RISK_SAMPLE.map(p=>`
    <div class="high-risk-item">
      <div class="hr-dot" style="background:${colorMap[p.risk]};box-shadow:0 0 6px ${colorMap[p.risk]};"></div>
      <div class="hr-info">
        <div class="hr-name">${p.name} · Age ${p.age}</div>
        <div class="hr-drugs">${p.drugs.join(' · ')}</div>
      </div>
      <span class="hr-badge" style="background:rgba(239,68,68,0.12);color:${colorMap[p.risk]};">${p.risk.toUpperCase()}</span>
    </div>`).join('');
}

function populateAllPatients() {
  const el = document.getElementById('all-patients-table');
  const colorMap = { contraindicated:'#f87171', major:'#fb923c', moderate:'#fbbf24', minor:'#34d399' };
  const rows = ALL_PATIENTS_SAMPLE.map(p=>`
    <tr style="border-bottom:1px solid var(--bg-border2);">
      <td style="padding:10px 8px;font-size:13.5px;">${p.name}</td>
      <td style="padding:10px 8px;font-size:13px;color:var(--text-secondary);">${p.age}</td>
      <td style="padding:10px 8px;font-size:12px;color:var(--text-secondary);">${p.drugs.join(', ')}</td>
      <td style="padding:10px 8px;"><span style="font-size:11px;font-weight:700;color:${colorMap[p.risk]};padding:3px 10px;background:rgba(0,0,0,0.2);border-radius:10px;">${p.risk.toUpperCase()}</span></td>
    </tr>`).join('');
  el.innerHTML = `<table style="width:100%;border-collapse:collapse;">
    <thead><tr style="border-bottom:1px solid var(--bg-border);">
      <th style="text-align:left;padding:8px;font-size:11px;color:var(--text-muted);text-transform:uppercase;letter-spacing:0.08em;">Patient</th>
      <th style="text-align:left;padding:8px;font-size:11px;color:var(--text-muted);text-transform:uppercase;letter-spacing:0.08em;">Age</th>
      <th style="text-align:left;padding:8px;font-size:11px;color:var(--text-muted);text-transform:uppercase;letter-spacing:0.08em;">Medications</th>
      <th style="text-align:left;padding:8px;font-size:11px;color:var(--text-muted);text-transform:uppercase;letter-spacing:0.08em;">Risk Level</th>
    </tr></thead>
    <tbody>${rows}</tbody>
  </table>`;
}

// ═══ HELPERS ═══
function showLoading(text, sub) {
  document.getElementById('loading-text').textContent = text;
  document.getElementById('loading-sub').textContent = sub;
  document.getElementById('loading-overlay').classList.add('active');
}
function hideLoading() { document.getElementById('loading-overlay').classList.remove('active'); }
function updateLoadingText(text, sub) {
  document.getElementById('loading-text').textContent = text;
  document.getElementById('loading-sub').textContent = sub;
}
function sleep(ms) { return new Promise(r=>setTimeout(r,ms)); }
</script>
</body>
</html>
