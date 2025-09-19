---
layout: base
title: Windows-setup
permalink: /tools/window-setup
---

<style>
  :root {
    --bg:#0f172a; --panel:#111827; --panel2:#0b1220;
    --text:#e5e7eb; --muted:#9ca3af; --accent:#38bdf8; --accent2:#22d3ee;
    --good:#34d399; --bad:#f87171; --border:#1f2937;
  }
  html,body{height:100%}
  body{
    margin:0;
    background: radial-gradient(1200px 600px at 80% -200px,#0a1224 0%,var(--bg) 45%,#070b16 100%);
    color:var(--text);
    font:16px/1.5 system-ui,-apple-system,Segoe UI,Roboto,Arial;
    display:grid;place-items:start center;
    padding:32px 16px 80px;
  }
  .app{
    width:min(980px,100%);
    background:linear-gradient(180deg,var(--panel) 0%,var(--panel2) 100%);
    border:1px solid var(--border);
    border-radius:16px;
    box-shadow:0 20px 60px rgba(0,0,0,.45);
    overflow:hidden;
  }

  header{padding:20px 24px;border-bottom:1px solid var(--border);display:grid;gap:6px}
  header h1{margin:0;font-size:20px;letter-spacing:.2px}
  header p{margin:0;color:var(--muted);font-size:14px}

  /* HUD */
  .meta{display:flex;gap:10px;align-items:center;color:var(--muted);font-size:13px;flex-wrap:wrap;margin-top:6px}
  .pill{border:1px solid var(--border);padding:6px 12px;border-radius:999px;background:#0a0f1c}
  .progressbar{height:10px;background:#0a0f1c;border:1px solid var(--border);border-radius:999px;overflow:hidden;margin-top:10px}
  .progressbar>div{height:100%;width:0;background:linear-gradient(90deg,var(--accent) 0%,var(--accent2) 100%);transition:width .24s ease}

  /* Map */
  .card{background:#0b1324;border:1px solid var(--border);border-radius:12px;padding:16px}
  .map{display:flex;gap:10px;align-items:center;overflow-x:auto;padding:8px 4px}
  .node{--size:26px;width:var(--size);height:var(--size);border-radius:50%;border:1px solid rgba(255,255,255,0.12);background:#0c1730;color:var(--muted);display:grid;place-items:center;font-size:12px;flex:0 0 auto}
  .node.active{background:linear-gradient(180deg, rgba(56,189,248,.25), rgba(34,211,238,.2));color:#07202a;border-color:rgba(56,189,248,.6)}
  .node.done{background:linear-gradient(180deg, rgba(52,211,153,.35), rgba(16,185,129,.25));color:#052014;border-color:rgba(16,185,129,.7)}
  .link{height:2px;width:34px;background:linear-gradient(90deg, rgba(255,255,255,.08), rgba(255,255,255,.18));flex:0 0 auto}

  /* Step card */
  .content{padding:20px 24px;display:grid;gap:16px}
  .question{font-size:18px;margin:4px 0 12px}
  .choices{display:grid;gap:10px;margin:8px 0}
  .choice{display:flex;gap:10px;align-items:start;border:1px solid var(--border);border-radius:10px;padding:10px 12px;background:#0a1120;cursor:pointer}
  .choice input{margin-top:3px}
  .choice.correct{outline:1px solid rgba(52,211,153,.5)}
  .choice.incorrect{outline:1px solid rgba(248,113,113,.5)}
  .hint{margin-top:8px;font-size:14px;color:var(--muted)}
  .links{margin-top:10px;display:flex;gap:10px;flex-wrap:wrap}
  .links a{color:var(--accent);text-decoration:none;border-bottom:1px dashed rgba(56,189,248,.4)}
  .feedback{font-size:14px;margin-top:6px}
  .feedback.ok{color:var(--good)}
  .feedback.err{color:var(--bad)}

  .controls{
    display:flex; gap:12px; margin-top:6px; flex-wrap:wrap; width:100%; align-items:center;
  }
  .controls .left, .controls .right{display:flex; gap:10px}
  .controls .right{margin-left:auto}

  button{background:#101933;color:var(--text);border:1px solid var(--border);padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600}
  button.primary{background:linear-gradient(90deg,var(--accent) 0%,var(--accent2) 100%);color:#051026;border:none}
  button:disabled{opacity:.5;cursor:not-allowed}

  .summary{font-size:15px;color:var(--muted)}

  /* Confetti */
  .confetti{position:fixed;left:0;top:0;width:100%;height:100%;pointer-events:none;overflow:hidden}
  .confetti span{position:absolute;top:-10px;font-size:18px;opacity:.9;animation:fall 1400ms linear forwards}
  @keyframes fall{to{transform:translateY(110vh) rotate(540deg);opacity:1}}
</style>

<div class="app" role="application" aria-label="Setup Quest — Windows">
  <header>
    <h1>Setup Quest — Windows (VS Code & GitHub Pages)</h1>
    <p>Face each trial and unlock the next. Choose wisely.</p>
    <div class="progressbar" aria-hidden="true"><div id="progressFill"></div></div>
    <div class="meta">
      <span class="pill" id="stepCounter">Trial 1/1</span>
      <span class="pill" id="rankPill">Rank: Novice Adventurer</span>
      <span class="pill" id="xpPill">XP: 0</span>
      <span class="pill" id="streakPill">Streak: 0</span>
      <span class="pill" id="savePill">Progress: Auto-Saved</span>
    </div>
  </header>

  <main class="content">
    <!-- Map -->
    <section class="card"><div class="map" id="map"></div></section>

    <!-- Step -->
    <section id="stepCard" class="card" aria-live="polite">
      <div class="pill" style="opacity:.7;margin-bottom:8px" id="stepId"></div>
      <h2 class="question" id="questionText">Loading…</h2>
      <div id="hint" class="hint" hidden></div>
      <div id="choices" class="choices" role="group" aria-label="Choices"></div>
      <div id="feedback" class="feedback" aria-live="assertive"></div>
      <div id="links" class="links"></div>
      <div class="controls">
        <div class="left">
          <button id="backBtn">← Back</button>
          <button id="resetBtn" title="Clears saved answers">Reset</button>
        </div>
        <div class="right">
          <button id="checkBtn">Check Answer</button>
          <button id="nextBtn" class="primary" disabled>Next →</button>
        </div>
      </div>
    </section>

    <!-- Final -->
    <section id="finalCard" class="card" hidden>
      <h2>Quest Complete! 🎉</h2>
      <p class="summary" id="summaryText"></p>
      <div class="links" id="finalLinks"></div>
      <div class="controls">
        <div class="left"><button id="restartBtn">Restart</button></div>
        <div class="right"><button id="exportBtn" class="primary">Export Progress (.json)</button></div>
      </div>
    </section>
  </main>
</div>

<div class="confetti" id="confetti" aria-hidden="true"></div>

<script>
/* ---------- persistence + HUD ---------- */
const STORAGE_KEY = "setupquest_windows_v1";
const state = { answers:{}, index:0, filteredSteps:[], xp:0, streak:0 };
const XP_PER_CORRECT = 100;
const RANKS = [
  {name:"Novice Adventurer", threshold:0},
  {name:"Apprentice Tinkerer", threshold:300},
  {name:"Journeyman Builder", threshold:600},
  {name:"Senior Wayfinder", threshold:900},
  {name:"Master of Setup", threshold:1200},
];

function save(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); }
function load(){ try{ const raw=localStorage.getItem(STORAGE_KEY); if(raw) Object.assign(state, JSON.parse(raw)); }catch{} }

function getSel(id){ return state.answers[id]||[]; }
function setSel(id, keys){ state.answers[id]=Array.isArray(keys)?keys:[keys]; save(); }
function current(){ return state.filteredSteps[state.index]; }
function pct(){ if(!state.filteredSteps.length) return 0; const n=state.filteredSteps.filter(s=>getSel(s.id).length).length; return Math.round(n/state.filteredSteps.length*100); }

function updateHUD(){
  document.getElementById("progressFill").style.width = pct()+"%";
  const rank = RANKS.reduce((a,b)=> b.threshold<=state.xp?b:a, RANKS[0]);
  document.getElementById("rankPill").textContent = `Rank: ${rank.name}`;
  document.getElementById("xpPill").textContent = `XP: ${state.xp}`;
  document.getElementById("streakPill").textContent = `Streak: ${state.streak}`;
  document.getElementById("savePill").textContent = "Progress: Auto-Saved";
  document.getElementById("stepCounter").textContent = `Trial ${Math.min(state.index+1, state.filteredSteps.length)}/${state.filteredSteps.length||1}`;
}

function buildMap(){
  const m=document.getElementById("map"); m.innerHTML="";
  const total=state.filteredSteps.length;
  for(let i=0;i<total;i++){
    const n=document.createElement("div"); n.className="node"; n.textContent=i+1;
    if(i<state.index) n.classList.add("done"); else if(i===state.index) n.classList.add("active");
    m.appendChild(n); if(i!==total-1){ const l=document.createElement("div"); l.className="link"; m.appendChild(l); }
  }
}

function confettiBurst(){
  const icons=["✨","💎","🗡️","🛡️","⚙️","🔧","🧪","📦","🚀","🔮"];
  const root=document.getElementById("confetti");
  for(let i=0;i<24;i++){
    const s=document.createElement("span"); s.textContent=icons[(Math.random()*icons.length)|0];
    s.style.left=(Math.random()*100)+"vw"; s.style.animationDelay=(Math.random()*0.3)+"s";
    root.appendChild(s); setTimeout(()=>root.removeChild(s),1600);
  }
}

/* ---------- Render ---------- */
function render(){
  updateHUD(); buildMap();
  const step=current(), stepCard=document.getElementById("stepCard"), finalCard=document.getElementById("finalCard");
  if(!step){ stepCard.hidden=true; finalCard.hidden=false; confettiBurst(); return renderFinal(); }

  stepCard.hidden=false; finalCard.hidden=true;
  document.getElementById("stepId").textContent = step.id;
  document.getElementById("questionText").innerHTML = step.question;
  const hintEl=document.getElementById("hint");
  if(step.hint){ hintEl.innerHTML=step.hint; hintEl.hidden=false; } else hintEl.hidden=true;

  // choices
  const choicesEl=document.getElementById("choices"); choicesEl.innerHTML="";
  const selected=new Set(getSel(step.id));
  step.choices.forEach(ch=>{
    const row=document.createElement("label"); row.className="choice";
    const input=document.createElement("input"); input.type="radio"; input.name="choice"; input.value=ch.key; input.checked=selected.has(ch.key);
    input.addEventListener("change",()=>{
      setSel(step.id,[ch.key]);
      document.getElementById("feedback").textContent="";
      document.getElementById("nextBtn").disabled=true;
      choicesEl.querySelectorAll(".choice").forEach(el=>el.classList.remove("correct","incorrect"));
      save();
    });
    const text=document.createElement("div");
    const strong=document.createElement("div"); strong.innerHTML=ch.label; text.appendChild(strong);
    if(ch.sub){ const sub=document.createElement("div"); sub.className="hint"; sub.textContent=ch.sub; text.appendChild(sub); }
    row.appendChild(input); row.appendChild(text); choicesEl.appendChild(row);
  });

  // links
  const linksEl=document.getElementById("links"); linksEl.innerHTML="";
  (step.links||[]).forEach(l=>{ const a=document.createElement("a"); a.href=l.href; a.target="_blank"; a.rel="noopener noreferrer"; a.textContent=l.text; linksEl.appendChild(a); });

  document.getElementById("backBtn").disabled = state.index===0;
  document.getElementById("nextBtn").disabled = true;
  document.getElementById("feedback").textContent="";
}

function checkAnswer(){
  const step=current(); if(!step) return;
  const sel=getSel(step.id); const fb=document.getElementById("feedback");
  if(!sel.length){ fb.textContent="Select an option to check."; fb.className="feedback err"; return; }
  const picked=sel[0];
  const isCorrect=!!step.choices.find(c=>c.key===picked && c.correct);
  const choiceEls=[...document.querySelectorAll(".choice")];
  choiceEls.forEach(el=>el.classList.remove("correct","incorrect"));
  const idx=step.choices.findIndex(c=>c.key===picked);
  if(idx>=0) choiceEls[idx].classList.add(isCorrect?"correct":"incorrect");

  if(isCorrect){
    fb.innerHTML = `Correct — next trial unlocked.` + (step.explain ? ` <span class="hint">${step.explain}</span>` : "");
    fb.className="feedback ok";
    document.getElementById("nextBtn").disabled=false;

    const awardKey="__awarded_"+step.id;
    if(!state.answers[awardKey]){ state.xp+=XP_PER_CORRECT; state.streak+=1; state.answers[awardKey]=true; save(); updateHUD(); }
  }else{
    fb.innerHTML = `Not quite. Try again.` + (step.explain ? ` <span class="hint">${step.explain}</span>` : "");
    fb.className="feedback err";
    state.streak=0; save(); updateHUD();
  }
}

function nextStep(){
  const step=current(); if(!step) return;
  const sel=getSel(step.id); if(!sel.length) return;
  if(!step.choices.find(c=>c.key===sel[0] && c.correct)) return;
  state.index++; save();
  if(state.index>=state.filteredSteps.length){ document.getElementById("stepCard").hidden=true; document.getElementById("finalCard").hidden=false; confettiBurst(); return renderFinal(); }
  render();
}
function prevStep(){ if(state.index===0) return; state.index--; save(); render(); }

function renderFinal(){
  const all=state.filteredSteps;
  const correctCount=all.reduce((n,s)=>{ const sel=getSel(s.id); if(!sel.length) return n; const ok=s.choices.some(c=>c.key===sel[0] && c.correct); return n+(ok?1:0); },0);
  document.getElementById("summaryText").innerHTML =
    `You cleared <b>${all.length}</b> trials. Correct: <b>${correctCount}</b>.<br>`+
    `Final Rank: <b>${RANKS.reduce((a,b)=> b.threshold<=state.xp?b:a, RANKS[0]).name}</b> &nbsp;|&nbsp; XP: <b>${state.xp}</b>`;
  const links=document.getElementById("finalLinks"); links.innerHTML="";
  const a=document.createElement("a"); a.href="#"; a.textContent="Restart from the beginning"; a.addEventListener("click",e=>{e.preventDefault(); resetAll();}); links.appendChild(a);
}

function exportProgress(){
  const data={ answers:Object.fromEntries(Object.entries(state.answers).filter(([k])=>!k.startsWith("__awarded_"))), index:state.index, steps:state.filteredSteps.map(s=>s.id), xp:state.xp, streak:state.streak, exportedAt:new Date().toISOString() };
  const blob=new Blob([JSON.stringify(data,null,2)],{type:"application/json"});
  const url=URL.createObjectURL(blob); const a=document.createElement("a");
  a.href=url; a.download="windows-setup-progress.json"; document.body.appendChild(a); a.click(); setTimeout(()=>{URL.revokeObjectURL(url); a.remove();},0);
}

function resetAll(){ localStorage.removeItem(STORAGE_KEY); state.answers={}; state.index=0; state.xp=0; state.streak=0; normalize(); render(); }
function normalize(){ state.filteredSteps = STEPS.slice(); if(state.index<0 || state.index>=state.filteredSteps.length) state.index=0; }

/* ---------- Steps (converted from your original 8 Qs) ---------- */
const STEPS = [
  {
    id:"win.source",
    question:"Where should you download Visual Studio Code for Windows?",
    choices:[
      {key:"a", label:"From a random blog link"},
      {key:"b", label:"From a torrent site"},
      {key:"c", label:"From the official Visual Studio Code website", correct:true},
      {key:"d", label:"From Discord"},
    ],
    explain:"Always download from the official site to avoid tampered installers.",
    links:[{text:"VS Code (official)", href:"https://code.visualstudio.com/"}]
  },
  {
    id:"win.code-dot",
    question:"After cloning your GitHub project on Windows, which command opens it in VS Code?",
    choices:[
      {key:"a", label:"vscode ."},
      {key:"b", label:"open ."},
      {key:"c", label:"code .", correct:true},
      {key:"d", label:"run-vscode"},
    ],
    explain:"`code .` launches VS Code in the current folder."
  },
  {
    id:"win.venv",
    question:"What does the command <code>./scripts/venv.sh</code> do before opening VS Code?",
    choices:[
      {key:"a", label:"Creates a GitHub repo"},
      {key:"b", label:"Activates/sets up a Python virtual environment", correct:true},
      {key:"c", label:"Installs Node.js"},
      {key:"d", label:"Starts the localhost server"},
    ],
    explain:"The script prepares the Python virtual environment used for your site."
  },
  {
    id:"win.path",
    question:"To run <code>code .</code> from Command Prompt/PowerShell, which install option is required?",
    choices:[
      {key:"a", label:"Add “Open with Code” in Explorer"},
      {key:"b", label:"Add VS Code to PATH", correct:true},
      {key:"c", label:"Enable Remote Development"},
      {key:"d", label:"Install Node.js"},
    ],
    explain:"Adding VS Code to PATH exposes the <code>code</code> CLI."
  },
  {
    id:"win.make",
    question:"What does the <code>make</code> command do in this project?",
    choices:[
      {key:"a", label:"Downloads VS Code extensions"},
      {key:"b", label:"Automates Makefile tasks and runs a local server", correct:true},
      {key:"c", label:"Deletes temporary files"},
      {key:"d", label:"Syncs directly to GitHub Pages"},
    ],
    explain:"Make runs scripted tasks like starting the local dev server."
  },
  {
    id:"win.test-local",
    question:"Before syncing changes to GitHub, what must you always do?",
    choices:[
      {key:"a", label:"Restart VS Code"},
      {key:"b", label:"Run <code>make</code> to test locally", correct:true},
      {key:"c", label:"Delete the venv folder"},
      {key:"d", label:"Disable GitHub Actions"},
    ],
    explain:"Local testing prevents pushing broken code."
  },
  {
    id:"win.local-url",
    question:"Where can you see your local website after <code>make</code> succeeds?",
    choices:[
      {key:"a", label:"In Windows Explorer"},
      {key:"b", label:"At <code>http://127.0.0.1:####/</code> in your browser", correct:true},
      {key:"c", label:"Inside GitHub directly"},
      {key:"d", label:"In Task Manager"},
    ],
    explain:"The terminal prints the local address (e.g., 127.0.0.1:4500)."
  },
  {
    id:"win.sdlc",
    question:"What’s the correct order of the SDLC workflow here?",
    choices:[
      {key:"a", label:"Sync → Commit → Run Server → Test"},
      {key:"b", label:"Run Server → Make Changes → Commit → Test → Sync", correct:true},
      {key:"c", label:"Commit → Test → Run Server → Sync"},
      {key:"d", label:"Test → Run Server → Sync → Commit"},
    ],
    explain:"Run server → make changes → commit → test → sync."
  },
];

/* ---------- Wire up & init ---------- */
document.getElementById("checkBtn").addEventListener("click", checkAnswer);
document.getElementById("nextBtn").addEventListener("click", nextStep);
document.getElementById("backBtn").addEventListener("click", prevStep);
document.getElementById("resetBtn").addEventListener("click", resetAll);
document.getElementById("restartBtn")?.addEventListener("click", resetAll);
document.getElementById("exportBtn")?.addEventListener("click", exportProgress);

function init(){
  load();
  state.filteredSteps = STEPS.slice();
  if(state.index<0 || state.index>=state.filteredSteps.length) state.index=0;
  render();
}
document.addEventListener("DOMContentLoaded", init);
</script>

