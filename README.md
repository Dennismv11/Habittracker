<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="theme-color" content="#000000">
<title>DayLevel</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #000;
    --bg2: #111;
    --bg3: #1c1c1e;
    --bg4: #2c2c2e;
    --text: #fff;
    --text2: rgba(255,255,255,0.6);
    --text3: rgba(255,255,255,0.3);
    --accent: #fff;
    --green: #30d158;
    --yellow: #ffd60a;
    --red: #ff453a;
    --blue: #0a84ff;
    --orange: #ff9f0a;
    --border: rgba(255,255,255,0.08);
    --radius: 16px;
    --radius-sm: 10px;
  }
  body {
    font-family: -apple-system, 'SF Pro Text', 'Helvetica Neue', sans-serif;
    background: var(--bg);
    color: var(--text);
    max-width: 420px;
    margin: 0 auto;
    min-height: 100vh;
    padding-bottom: 80px;
    -webkit-font-smoothing: antialiased;
  }
  .screen { display: none; padding: 0 16px; }
  .screen.active { display: block; }

  /* Header */
  .header {
    display: flex; align-items: center; justify-content: space-between;
    padding: 56px 16px 8px;
    position: sticky; top: 0; z-index: 10;
    background: linear-gradient(to bottom, #000 80%, transparent);
  }
  .header h1 { font-size: 34px; font-weight: 700; letter-spacing: -0.5px; }
  .header-sub { font-size: 13px; color: var(--text2); margin-top: 2px; }
  .header-right { text-align: right; }

  /* Tab Bar */
  .tabbar {
    position: fixed; bottom: 0; left: 50%; transform: translateX(-50%);
    width: 100%; max-width: 420px;
    background: rgba(28,28,30,0.92);
    backdrop-filter: blur(20px);
    border-top: 0.5px solid var(--border);
    display: flex; justify-content: space-around; align-items: flex-end;
    padding: 8px 0 20px;
    z-index: 100;
  }
  .tab-item {
    display: flex; flex-direction: column; align-items: center; gap: 4px;
    cursor: pointer; padding: 4px 12px;
    transition: opacity 0.15s;
  }
  .tab-icon { font-size: 22px; line-height: 1; }
  .tab-label { font-size: 10px; color: var(--text3); font-weight: 500; }
  .tab-item.active .tab-icon { filter: brightness(1); }
  .tab-item.active .tab-label { color: var(--blue); }
  .tab-item:not(.active) .tab-icon { opacity: 0.4; }

  /* Cards */
  .card {
    background: var(--bg3);
    border-radius: var(--radius);
    padding: 16px;
    margin-bottom: 12px;
    border: 0.5px solid var(--border);
  }
  .card-title {
    font-size: 13px; font-weight: 600; text-transform: uppercase;
    letter-spacing: 0.5px; color: var(--text2); margin-bottom: 12px;
  }

  /* Section Label */
  .section-label {
    font-size: 22px; font-weight: 700; letter-spacing: -0.3px;
    margin: 20px 0 12px;
  }

  /* DAY RATING */
  .mood-row {
    display: flex; gap: 12px; justify-content: center; margin: 8px 0 4px;
  }
  .mood-btn {
    flex: 1; display: flex; flex-direction: column; align-items: center;
    gap: 8px; padding: 16px 8px; border-radius: var(--radius);
    border: 1.5px solid var(--border); background: var(--bg4);
    cursor: pointer; transition: all 0.2s;
  }
  .mood-btn:hover { border-color: rgba(255,255,255,0.2); }
  .mood-btn.selected-good { border-color: var(--green); background: rgba(48,209,88,0.12); }
  .mood-btn.selected-neutral { border-color: var(--yellow); background: rgba(255,214,10,0.12); }
  .mood-btn.selected-bad { border-color: var(--red); background: rgba(255,69,58,0.12); }
  .mood-emoji { font-size: 32px; }
  .mood-label { font-size: 12px; color: var(--text2); font-weight: 500; }

  /* CALENDAR */
  .calendar-grid {
    display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px;
    margin-top: 8px;
  }
  .cal-day-label {
    text-align: center; font-size: 11px; color: var(--text3);
    font-weight: 600; padding: 4px 0;
  }
  .cal-day {
    aspect-ratio: 1; border-radius: 50%; display: flex;
    align-items: center; justify-content: center;
    font-size: 13px; font-weight: 500; cursor: pointer;
    transition: all 0.15s; color: var(--text2);
  }
  .cal-day.today { border: 1.5px solid rgba(255,255,255,0.3); color: var(--text); }
  .cal-day.good { background: var(--green); color: #000; }
  .cal-day.neutral { background: var(--yellow); color: #000; }
  .cal-day.bad { background: var(--red); color: #fff; }
  .cal-day.empty { opacity: 0; pointer-events: none; }

  /* HABITS */
  .habit-item {
    display: flex; align-items: center; gap: 12px;
    padding: 14px 0; border-bottom: 0.5px solid var(--border);
  }
  .habit-item:last-child { border-bottom: none; }
  .habit-check {
    width: 26px; height: 26px; border-radius: 50%;
    border: 2px solid rgba(255,255,255,0.2);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; flex-shrink: 0; transition: all 0.2s;
    font-size: 14px;
  }
  .habit-check.done { background: var(--blue); border-color: var(--blue); }
  .habit-text { flex: 1; }
  .habit-name { font-size: 16px; font-weight: 500; }
  .habit-streak { font-size: 12px; color: var(--orange); margin-top: 2px; }
  .habit-add { 
    display: flex; align-items: center; gap: 8px; padding: 12px 0;
    color: var(--blue); font-size: 16px; cursor: pointer; font-weight: 500;
  }

  /* GOALS AMPEL */
  .goal-item {
    display: flex; align-items: center; gap: 12px;
    padding: 12px 0; border-bottom: 0.5px solid var(--border);
  }
  .goal-item:last-child { border-bottom: none; }
  .goal-dot { width: 12px; height: 12px; border-radius: 50%; flex-shrink: 0; }
  .goal-dot.green { background: var(--green); box-shadow: 0 0 6px var(--green); }
  .goal-dot.yellow { background: var(--yellow); box-shadow: 0 0 6px var(--yellow); }
  .goal-dot.red { background: var(--red); box-shadow: 0 0 6px var(--red); }
  .goal-text { flex: 1; font-size: 15px; font-weight: 500; }
  .goal-done { text-decoration: line-through; opacity: 0.4; }
  .goal-check { color: var(--text3); font-size: 20px; cursor: pointer; }
  .goal-check.done { color: var(--green); }

  /* PROGRESS */
  .progress-ring-wrap {
    display: flex; justify-content: center; align-items: center;
    padding: 16px 0; flex-direction: column; gap: 8px;
  }
  .big-stat { font-size: 42px; font-weight: 700; letter-spacing: -1px; }
  .big-stat-sub { font-size: 14px; color: var(--text2); }
  .progress-bar-wrap { margin: 8px 0; }
  .progress-label {
    display: flex; justify-content: space-between;
    font-size: 13px; color: var(--text2); margin-bottom: 6px;
  }
  .progress-bar {
    height: 6px; border-radius: 3px; background: var(--bg4); overflow: hidden;
  }
  .progress-fill {
    height: 100%; border-radius: 3px; transition: width 0.6s ease;
  }
  .stat-row { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
  .stat-mini {
    background: var(--bg4); border-radius: var(--radius-sm);
    padding: 14px; border: 0.5px solid var(--border);
  }
  .stat-mini-val { font-size: 24px; font-weight: 700; margin-bottom: 2px; }
  .stat-mini-label { font-size: 12px; color: var(--text2); }

  /* CHALLENGE */
  .challenge-card {
    background: linear-gradient(135deg, #1c1c2e 0%, #16213e 100%);
    border-radius: var(--radius); padding: 20px;
    border: 1px solid rgba(10,132,255,0.3); margin-bottom: 12px;
    position: relative; overflow: hidden;
  }
  .challenge-badge {
    display: inline-flex; align-items: center; gap: 6px;
    background: rgba(10,132,255,0.2); border: 1px solid rgba(10,132,255,0.4);
    border-radius: 20px; padding: 4px 12px;
    font-size: 12px; font-weight: 600; color: var(--blue);
    margin-bottom: 12px; text-transform: uppercase; letter-spacing: 0.5px;
  }
  .challenge-title { font-size: 20px; font-weight: 700; margin-bottom: 8px; line-height: 1.2; }
  .challenge-desc { font-size: 14px; color: var(--text2); line-height: 1.5; margin-bottom: 16px; }
  .challenge-btn {
    background: var(--blue); color: #fff; border: none; border-radius: var(--radius-sm);
    padding: 12px 20px; font-size: 16px; font-weight: 600;
    cursor: pointer; width: 100%; transition: opacity 0.15s;
  }
  .challenge-btn:hover { opacity: 0.85; }
  .challenge-btn.done { background: var(--green); }
  .challenge-btn.loading { background: var(--bg4); color: var(--text2); cursor: default; }
  .challenge-meta { font-size: 12px; color: var(--text3); margin-top: 10px; text-align: center; }

  /* INPUT */
  .input-field {
    background: var(--bg4); border: 0.5px solid var(--border);
    border-radius: var(--radius-sm); color: var(--text);
    font-size: 16px; padding: 12px 14px; width: 100%;
    margin-bottom: 8px; outline: none;
    -webkit-appearance: none;
  }
  .input-field:focus { border-color: var(--blue); }
  .btn-primary {
    background: var(--blue); color: #fff; border: none;
    border-radius: var(--radius-sm); padding: 12px 20px;
    font-size: 16px; font-weight: 600; cursor: pointer;
    transition: opacity 0.15s;
  }
  .btn-secondary {
    background: var(--bg4); color: var(--text2); border: 0.5px solid var(--border);
    border-radius: var(--radius-sm); padding: 10px 16px;
    font-size: 15px; font-weight: 500; cursor: pointer;
  }

  /* Toast */
  .toast {
    position: fixed; bottom: 90px; left: 50%; transform: translateX(-50%);
    background: rgba(44,44,46,0.95); border: 0.5px solid var(--border);
    backdrop-filter: blur(20px); border-radius: 20px;
    padding: 10px 20px; font-size: 14px; font-weight: 500;
    z-index: 999; white-space: nowrap;
    animation: toastin 0.3s ease;
    display: none;
  }
  @keyframes toastin { from { opacity:0; transform: translateX(-50%) translateY(10px); } to { opacity:1; transform: translateX(-50%) translateY(0); } }

  /* Modal */
  .modal-bg {
    display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.7);
    z-index: 200; align-items: flex-end; justify-content: center;
  }
  .modal-bg.open { display: flex; }
  .modal {
    background: var(--bg3); border-radius: 20px 20px 0 0;
    width: 100%; max-width: 420px; padding: 20px 20px 40px;
    border-top: 0.5px solid var(--border);
  }
  .modal h3 { font-size: 20px; font-weight: 700; margin-bottom: 16px; }
  .modal-handle {
    width: 36px; height: 4px; background: var(--bg4);
    border-radius: 2px; margin: 0 auto 16px;
  }

  .notification-hint {
    background: var(--bg3); border-radius: var(--radius); padding: 16px;
    border: 0.5px solid var(--border); margin-bottom: 12px;
    font-size: 14px; color: var(--text2); line-height: 1.5;
  }
  .notification-hint strong { color: var(--text); }

  .firebase-hint {
    background: rgba(255,159,10,0.08); border: 1px solid rgba(255,159,10,0.2);
    border-radius: var(--radius-sm); padding: 14px; margin-bottom: 12px;
    font-size: 13px; color: rgba(255,159,10,0.9); line-height: 1.5;
  }
</style>
</head>
<body>

<div class="toast" id="toast"></div>

<!-- TAB: TODAY -->
<div class="screen active" id="screen-today">
  <div class="header">
    <div>
      <div class="header-sub" id="today-date"></div>
      <h1>Heute</h1>
    </div>
    <div class="header-right">
      <div style="font-size:28px" id="streak-display">🔥 0</div>
      <div style="font-size:11px; color:var(--text2)">Tage in Folge</div>
    </div>
  </div>

  <div class="section-label">Tagesbewertung</div>
  <div class="card">
    <div class="card-title">Wie war dein Tag?</div>
    <div class="mood-row">
      <button class="mood-btn" id="mood-good" onclick="setMood('good')">
        <div class="mood-emoji">😄</div>
        <div class="mood-label">Effektiv</div>
      </button>
      <button class="mood-btn" id="mood-neutral" onclick="setMood('neutral')">
        <div class="mood-emoji">😐</div>
        <div class="mood-label">Normal</div>
      </button>
      <button class="mood-btn" id="mood-bad" onclick="setMood('bad')">
        <div class="mood-emoji">😞</div>
        <div class="mood-label">Schwach</div>
      </button>
    </div>
  </div>

  <div class="section-label">Habits</div>
  <div class="card" id="habits-card">
    <div id="habits-list"></div>
    <div class="habit-add" onclick="openAddHabit()">
      <span style="font-size:20px">+</span> Habit hinzufügen
    </div>
  </div>

  <div class="section-label">Daily Challenge</div>
  <div id="challenge-area"></div>
</div>

<!-- TAB: KALENDER -->
<div class="screen" id="screen-calendar">
  <div class="header">
    <div>
      <h1>Kalender</h1>
    </div>
  </div>
  <div class="card">
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px">
      <button class="btn-secondary" style="padding:6px 12px" onclick="prevMonth()">‹</button>
      <div style="font-weight:600;font-size:16px" id="cal-month-label"></div>
      <button class="btn-secondary" style="padding:6px 12px" onclick="nextMonth()">›</button>
    </div>
    <div class="calendar-grid" id="cal-grid"></div>
    <div style="display:flex;gap:16px;margin-top:16px;justify-content:center">
      <div style="display:flex;align-items:center;gap:6px;font-size:12px;color:var(--text2)">
        <div style="width:10px;height:10px;border-radius:50%;background:var(--green)"></div>Effektiv
      </div>
      <div style="display:flex;align-items:center;gap:6px;font-size:12px;color:var(--text2)">
        <div style="width:10px;height:10px;border-radius:50%;background:var(--yellow)"></div>Normal
      </div>
      <div style="display:flex;align-items:center;gap:6px;font-size:12px;color:var(--text2)">
        <div style="width:10px;height:10px;border-radius:50%;background:var(--red)"></div>Schwach
      </div>
    </div>
  </div>
</div>

<!-- TAB: ZIELE -->
<div class="screen" id="screen-goals">
  <div class="header">
    <div><h1>Ziele</h1></div>
  </div>

  <div class="section-label">Tagesziele</div>
  <div class="card">
    <div class="card-title">Ampelsystem · <span id="ampel-time-label">Morgen = Grün</span></div>
    <div id="daily-goals-list"></div>
    <div class="habit-add" onclick="openAddGoal('daily')">
      <span style="font-size:20px">+</span> Tagesziel hinzufügen
    </div>
  </div>

  <div class="section-label">Wochenziele</div>
  <div class="card">
    <div id="weekly-goals-list"></div>
    <div class="habit-add" onclick="openAddGoal('weekly')">
      <span style="font-size:20px">+</span> Wochenziel hinzufügen
    </div>
  </div>
</div>

<!-- TAB: FORTSCHRITT -->
<div class="screen" id="screen-progress">
  <div class="header">
    <div><h1>Fortschritt</h1></div>
  </div>

  <div class="card">
    <div class="card-title">Monatsscore</div>
    <div class="progress-ring-wrap">
      <div class="big-stat" id="prog-score">0</div>
      <div class="big-stat-sub" id="prog-score-sub">Punkte diesen Monat</div>
    </div>
    <div class="progress-bar-wrap">
      <div class="progress-label">
        <span>😄 Gute Tage</span><span id="prog-good-count">0</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" id="prog-good-bar" style="background:var(--green);width:0%"></div>
      </div>
    </div>
    <div class="progress-bar-wrap" style="margin-top:8px">
      <div class="progress-label">
        <span>😐 Normale Tage</span><span id="prog-neutral-count">0</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" id="prog-neutral-bar" style="background:var(--yellow);width:0%"></div>
      </div>
    </div>
    <div class="progress-bar-wrap" style="margin-top:8px">
      <div class="progress-label">
        <span>😞 Schwache Tage</span><span id="prog-bad-count">0</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" id="prog-bad-bar" style="background:var(--red);width:0%"></div>
      </div>
    </div>
  </div>

  <div class="stat-row">
    <div class="stat-mini">
      <div class="stat-mini-val" id="prog-streak">0🔥</div>
      <div class="stat-mini-label">Aktueller Streak</div>
    </div>
    <div class="stat-mini">
      <div class="stat-mini-val" id="prog-habits-today">0%</div>
      <div class="stat-mini-label">Habits heute erledigt</div>
    </div>
    <div class="stat-mini">
      <div class="stat-mini-val" id="prog-goals-done">0</div>
      <div class="stat-mini-label">Ziele diese Woche</div>
    </div>
    <div class="stat-mini">
      <div class="stat-mini-val" id="prog-challenges-done">0</div>
      <div class="stat-mini-label">Challenges abgeschlossen</div>
    </div>
  </div>

  <div class="firebase-hint" style="margin-top:12px">
    ⚙️ <strong>Firebase einrichten:</strong> Erstelle ein Projekt auf console.firebase.google.com, aktiviere Firestore, kopiere deine Config in den Code (Variable <code>FIREBASE_CONFIG</code> im Script). Alle Daten werden dann automatisch synchronisiert.
  </div>
</div>

<!-- MODALS -->
<div class="modal-bg" id="modal-habit">
  <div class="modal">
    <div class="modal-handle"></div>
    <h3>Habit hinzufügen</h3>
    <input class="input-field" id="habit-input" placeholder="z.B. Spanisch lernen" />
    <div style="display:flex;gap:8px">
      <button class="btn-primary" style="flex:1" onclick="addHabit()">Hinzufügen</button>
      <button class="btn-secondary" onclick="closeModal('modal-habit')">Abbrechen</button>
    </div>
  </div>
</div>

<div class="modal-bg" id="modal-goal">
  <div class="modal">
    <div class="modal-handle"></div>
    <h3 id="modal-goal-title">Ziel hinzufügen</h3>
    <input class="input-field" id="goal-input" placeholder="z.B. Präsentation vorbereiten" />
    <div style="display:flex;gap:8px">
      <button class="btn-primary" style="flex:1" onclick="addGoal()">Hinzufügen</button>
      <button class="btn-secondary" onclick="closeModal('modal-goal')">Abbrechen</button>
    </div>
  </div>
</div>

<!-- TAB BAR -->
<nav class="tabbar">
  <div class="tab-item active" onclick="showTab('today', this)">
    <div class="tab-icon">☀️</div>
    <div class="tab-label">Heute</div>
  </div>
  <div class="tab-item" onclick="showTab('calendar', this)">
    <div class="tab-icon">📅</div>
    <div class="tab-label">Kalender</div>
  </div>
  <div class="tab-item" onclick="showTab('goals', this)">
    <div class="tab-icon">🎯</div>
    <div class="tab-label">Ziele</div>
  </div>
  <div class="tab-item" onclick="showTab('progress', this)">
    <div class="tab-icon">📊</div>
    <div class="tab-label">Fortschritt</div>
  </div>
</nav>

<script>
const ANTHROPIC_KEY = null; // Wird über API-Proxy gehandhabt

// ─── STATE ───────────────────────────────────────────────────────────────
let state = JSON.parse(localStorage.getItem('daylevel_state') || '{}');
if (!state.moods) state.moods = {};
if (!state.habits) state.habits = [
  { id: 1, name: 'Handelsblatt lesen', streak: 0 },
  { id: 2, name: 'Spanisch lernen', streak: 0 },
  { id: 3, name: 'Joggen gehen', streak: 0 }
];
if (!state.habitDone) state.habitDone = {};
if (!state.dailyGoals) state.dailyGoals = [];
if (!state.weeklyGoals) state.weeklyGoals = [];
if (!state.goalDone) state.goalDone = {};
if (!state.challenge) state.challenge = {};
if (!state.challengesDone) state.challengesDone = 0;

const today = new Date();
const todayKey = today.toISOString().split('T')[0];
let calYear = today.getFullYear();
let calMonth = today.getMonth();
let addingGoalType = 'daily';
let nextHabitId = Math.max(...state.habits.map(h=>h.id), 0) + 1;
let nextGoalId = Date.now();

function save() { localStorage.setItem('daylevel_state', JSON.stringify(state)); }

// ─── TABS ─────────────────────────────────────────────────────────────────
function showTab(name, el) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.tab-item').forEach(t => t.classList.remove('active'));
  document.getElementById('screen-' + name).classList.add('active');
  el.classList.add('active');
  if (name === 'calendar') renderCalendar();
  if (name === 'progress') renderProgress();
}

// ─── TODAY DATE ───────────────────────────────────────────────────────────
function setupHeader() {
  const days = ['Sonntag','Montag','Dienstag','Mittwoch','Donnerstag','Freitag','Samstag'];
  const months = ['Januar','Februar','März','April','Mai','Juni','Juli','August','September','Oktober','November','Dezember'];
  document.getElementById('today-date').textContent =
    days[today.getDay()] + ', ' + today.getDate() + '. ' + months[today.getMonth()];
}

// ─── MOOD ─────────────────────────────────────────────────────────────────
function setMood(type) {
  state.moods[todayKey] = type;
  save();
  ['good','neutral','bad'].forEach(m => {
    const btn = document.getElementById('mood-' + m);
    btn.className = 'mood-btn';
    if (m === type) btn.classList.add('selected-' + m);
  });
  updateStreak();
  showToast(type === 'good' ? '😄 Top! Weiter so!' : type === 'neutral' ? '😐 Morgen besser!' : '😞 Nicht aufgeben!');
}

function loadMood() {
  const m = state.moods[todayKey];
  if (m) {
    document.getElementById('mood-' + m).classList.add('selected-' + m);
  }
}

// ─── STREAK ───────────────────────────────────────────────────────────────
function updateStreak() {
  let streak = 0;
  let d = new Date();
  while (true) {
    const key = d.toISOString().split('T')[0];
    if (state.moods[key]) { streak++; d.setDate(d.getDate()-1); }
    else break;
  }
  document.getElementById('streak-display').textContent = '🔥 ' + streak;
  return streak;
}

// ─── HABITS ───────────────────────────────────────────────────────────────
function renderHabits() {
  const list = document.getElementById('habits-list');
  if (!state.habitDone[todayKey]) state.habitDone[todayKey] = {};
  list.innerHTML = state.habits.map(h => {
    const done = !!state.habitDone[todayKey][h.id];
    return `<div class="habit-item">
      <div class="habit-check ${done?'done':''}" onclick="toggleHabit(${h.id})">
        ${done ? '✓' : ''}
      </div>
      <div class="habit-text">
        <div class="habit-name" style="${done?'opacity:0.4;text-decoration:line-through':''}">${h.name}</div>
        <div class="habit-streak">${h.streak > 0 ? '🔥 '+h.streak+' Tage' : ''}</div>
      </div>
      <div style="color:var(--text3);cursor:pointer;font-size:20px;padding:4px" onclick="deleteHabit(${h.id})">×</div>
    </div>`;
  }).join('');
}

function toggleHabit(id) {
  if (!state.habitDone[todayKey]) state.habitDone[todayKey] = {};
  const was = state.habitDone[todayKey][id];
  state.habitDone[todayKey][id] = !was;
  const habit = state.habits.find(h => h.id === id);
  if (habit) habit.streak = state.habitDone[todayKey][id] ? (habit.streak + 1) : Math.max(0, habit.streak - 1);
  save();
  renderHabits();
}

function deleteHabit(id) {
  state.habits = state.habits.filter(h => h.id !== id);
  save(); renderHabits();
}

function openAddHabit() {
  document.getElementById('habit-input').value = '';
  document.getElementById('modal-habit').classList.add('open');
}

function addHabit() {
  const val = document.getElementById('habit-input').value.trim();
  if (!val) return;
  state.habits.push({ id: nextHabitId++, name: val, streak: 0 });
  save(); renderHabits();
  closeModal('modal-habit');
  showToast('✓ Habit hinzugefügt');
}

// ─── CALENDAR ────────────────────────────────────────────────────────────
function renderCalendar() {
  const months = ['Januar','Februar','März','April','Mai','Juni','Juli','August','September','Oktober','November','Dezember'];
  document.getElementById('cal-month-label').textContent = months[calMonth] + ' ' + calYear;
  const grid = document.getElementById('cal-grid');
  const labels = ['Mo','Di','Mi','Do','Fr','Sa','So'];
  let html = labels.map(l => `<div class="cal-day-label">${l}</div>`).join('');
  const first = new Date(calYear, calMonth, 1);
  let startDay = first.getDay();
  startDay = startDay === 0 ? 6 : startDay - 1;
  for (let i = 0; i < startDay; i++) html += `<div class="cal-day empty"></div>`;
  const days = new Date(calYear, calMonth+1, 0).getDate();
  for (let d = 1; d <= days; d++) {
    const key = `${calYear}-${String(calMonth+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    const mood = state.moods[key] || '';
    const isToday = key === todayKey;
    html += `<div class="cal-day ${mood} ${isToday?'today':''}">${d}</div>`;
  }
  grid.innerHTML = html;
}

function prevMonth() { calMonth--; if (calMonth < 0) { calMonth=11; calYear--; } renderCalendar(); }
function nextMonth() { calMonth++; if (calMonth > 11) { calMonth=0; calYear++; } renderCalendar(); }

// ─── GOALS / AMPEL ────────────────────────────────────────────────────────
function getAmpelColor() {
  const h = today.getHours();
  if (h < 12) return 'green';
  if (h < 17) return 'yellow';
  return 'red';
}

function setupAmpelLabel() {
  const h = today.getHours();
  const lbl = document.getElementById('ampel-time-label');
  if (h < 12) lbl.textContent = 'Morgen · Grün';
  else if (h < 17) lbl.textContent = 'Nachmittag · Gelb';
  else lbl.textContent = 'Abend · Rot';
}

function renderGoals() {
  const color = getAmpelColor();
  renderGoalList('daily-goals-list', state.dailyGoals, color);
  renderGoalList('weekly-goals-list', state.weeklyGoals, 'blue');
}

function renderGoalList(containerId, goals, color) {
  const el = document.getElementById(containerId);
  if (!goals.length) { el.innerHTML = ''; return; }
  el.innerHTML = goals.map(g => {
    const done = !!state.goalDone[g.id];
    const dotColor = done ? 'var(--green)' : color === 'green' ? 'var(--green)' : color === 'yellow' ? 'var(--yellow)' : color === 'red' ? 'var(--red)' : 'var(--blue)';
    return `<div class="goal-item">
      <div class="goal-dot" style="background:${dotColor};box-shadow:0 0 6px ${dotColor}"></div>
      <div class="goal-text ${done?'goal-done':''}">${g.name}</div>
      <div class="goal-check ${done?'done':''}" onclick="toggleGoal('${g.id}')">
        ${done ? '✓' : '○'}
      </div>
      <div style="color:var(--text3);cursor:pointer;margin-left:4px" onclick="deleteGoal('${g.id}')">×</div>
    </div>`;
  }).join('');
}

function toggleGoal(id) {
  state.goalDone[id] = !state.goalDone[id];
  save(); renderGoals();
}

function deleteGoal(id) {
  state.dailyGoals = state.dailyGoals.filter(g => g.id !== id);
  state.weeklyGoals = state.weeklyGoals.filter(g => g.id !== id);
  save(); renderGoals();
}

function openAddGoal(type) {
  addingGoalType = type;
  document.getElementById('modal-goal-title').textContent = type === 'daily' ? 'Tagesziel hinzufügen' : 'Wochenziel hinzufügen';
  document.getElementById('goal-input').value = '';
  document.getElementById('modal-goal').classList.add('open');
}

function addGoal() {
  const val = document.getElementById('goal-input').value.trim();
  if (!val) return;
  const goal = { id: 'g' + (nextGoalId++), name: val };
  if (addingGoalType === 'daily') state.dailyGoals.push(goal);
  else state.weeklyGoals.push(goal);
  save(); renderGoals();
  closeModal('modal-goal');
  showToast('✓ Ziel hinzugefügt');
}

// ─── PROGRESS ─────────────────────────────────────────────────────────────
function renderProgress() {
  const year = today.getFullYear(), month = today.getMonth();
  let good=0, neutral=0, bad=0;
  Object.entries(state.moods).forEach(([k,v]) => {
    const d = new Date(k);
    if (d.getFullYear()===year && d.getMonth()===month) {
      if (v==='good') good++;
      else if (v==='neutral') neutral++;
      else bad++;
    }
  });
  const total = good+neutral+bad || 1;
  const score = good*3 + neutral*1 + bad*0;
  document.getElementById('prog-score').textContent = score;
  document.getElementById('prog-good-count').textContent = good;
  document.getElementById('prog-neutral-count').textContent = neutral;
  document.getElementById('prog-bad-count').textContent = bad;
  document.getElementById('prog-good-bar').style.width = Math.round(good/total*100)+'%';
  document.getElementById('prog-neutral-bar').style.width = Math.round(neutral/total*100)+'%';
  document.getElementById('prog-bad-bar').style.width = Math.round(bad/total*100)+'%';

  const streak = updateStreak();
  document.getElementById('prog-streak').textContent = streak + '🔥';

  const habits = state.habits.length;
  const doneTd = Object.values(state.habitDone[todayKey]||{}).filter(Boolean).length;
  document.getElementById('prog-habits-today').textContent =
    habits > 0 ? Math.round(doneTd/habits*100)+'%' : '–';

  const weekGoalsDone = Object.values(state.goalDone).filter(Boolean).length;
  document.getElementById('prog-goals-done').textContent = weekGoalsDone;
  document.getElementById('prog-challenges-done').textContent = state.challengesDone;
}

// ─── CHALLENGE ────────────────────────────────────────────────────────────
async function loadChallenge() {
  const area = document.getElementById('challenge-area');
  if (state.challenge.date === todayKey) {
    renderChallenge(state.challenge);
    return;
  }
  area.innerHTML = `<div class="challenge-card">
    <div class="challenge-badge">⚡ Daily Sidequest</div>
    <div class="challenge-title" style="color:var(--text2)">Challenge wird geladen...</div>
    <div class="challenge-desc">KI generiert deine heutige Challenge</div>
    <button class="challenge-btn loading" disabled>Wird geladen...</button>
  </div>`;
  try {
    const resp = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'anthropic-version': '2023-06-01', 'x-api-key': '' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 300,
        messages: [{
          role: 'user',
          content: `Generiere eine motivierende Daily Challenge für eine Habit-Tracker-App. Beantworte NUR mit JSON:
{"title":"kurzer Titel max 8 Wörter","desc":"Beschreibung was zu tun ist, 1-2 Sätze, motivierend auf Deutsch","category":"sport|wissen|social|kreativ|achtsamkeit"}

Abwechslungsreiche Ideen: Sport (Liegestütze, Laufen, Dehnen), Wissen (Hauptstädte, Sprache, Geschichte), Social (Kompliment, Anruf, Hilfe anbieten), Kreativ (Zeichnen, Schreiben), Achtsamkeit (Meditation, Dankbarkeit). Heute: ${todayKey}`
        }]
      })
    });
    const data = await resp.json();
    const text = data.content?.[0]?.text || '';
    const clean = text.replace(/```json|```/g,'').trim();
    const ch = JSON.parse(clean);
    ch.date = todayKey;
    ch.done = false;
    state.challenge = ch;
    save();
    renderChallenge(ch);
  } catch(e) {
    const fallbacks = [
      { title: '50 Liegestütze', desc: 'Mach heute 50 Liegestütze – aufgeteilt in Sätze wenn nötig. Dein Körper wird es dir danken!', category: 'sport' },
      { title: '3 neue Hauptstädte lernen', desc: 'Lerne heute 3 Hauptstädte die du noch nicht kannst. In einer Woche wirst du abgefragt!', category: 'wissen' },
      { title: '2 aufrichtige Komplimente', desc: 'Gib heute 2 Menschen ein ehrliches Kompliment. Einfach, aber wirkungsvoll.', category: 'social' }
    ];
    const ch = { ...fallbacks[Math.floor(Math.random()*fallbacks.length)], date: todayKey, done: false };
    state.challenge = ch;
    save();
    renderChallenge(ch);
  }
}

const categoryEmoji = { sport:'🏃', wissen:'🧠', social:'🤝', kreativ:'🎨', achtsamkeit:'🧘' };

function renderChallenge(ch) {
  const area = document.getElementById('challenge-area');
  area.innerHTML = `<div class="challenge-card">
    <div class="challenge-badge">${categoryEmoji[ch.category]||'⚡'} ${ch.category || 'Challenge'}</div>
    <div class="challenge-title">${ch.title}</div>
    <div class="challenge-desc">${ch.desc}</div>
    <button class="challenge-btn ${ch.done?'done':''}" onclick="completeChallenge()">
      ${ch.done ? '✓ Erledigt!' : 'Challenge annehmen'}
    </button>
    <div class="challenge-meta">Täglich um Mitternacht neue Challenge</div>
  </div>`;
}

function completeChallenge() {
  if (state.challenge.done) return;
  state.challenge.done = true;
  state.challengesDone++;
  save();
  renderChallenge(state.challenge);
  showToast('🎉 Challenge abgeschlossen!');
}

// ─── HELPERS ──────────────────────────────────────────────────────────────
function closeModal(id) { document.getElementById(id).classList.remove('open'); }
function showToast(msg) {
  const t = document.getElementById('toast');
  t.textContent = msg; t.style.display = 'block';
  setTimeout(() => { t.style.display = 'none'; }, 2500);
}

document.querySelectorAll('.modal-bg').forEach(m => {
  m.addEventListener('click', e => { if (e.target === m) m.classList.remove('open'); });
});

// ─── INIT ─────────────────────────────────────────────────────────────────
setupHeader();
loadMood();
updateStreak();
renderHabits();
renderGoals();
setupAmpelLabel();
loadChallenge();
</script>
</body>
</html>
