---
layout: base
title: Mac Setup
permalink: /tools/mac-setup
---
<style>
  :root {
    --bg: #0f172a; --panel: #111827; --panel2:#0b1220;
    --text:#e5e7eb; --muted:#9ca3af; --accent:#38bdf8; --accent2:#22d3ee;
    --good:#34d399; --bad:#f87171; --border:#1f2937;
  }
  html,body{height:100%} body{margin:0;background:radial-gradient(1200px 600px at 80% -200px,#0a1224 0%,var(--bg) 45%,#070b16 100%);color:var(--text);font:16px/1.5 system-ui,-apple-system,Segoe UI,Roboto,Arial;display:grid;place-items:start center;padding:32px 16px 80px}
  .app{width:min(920px,100%);background:linear-gradient(180deg,var(--panel) 0%,var(--panel2) 100%);border:1px solid var(--border);border-radius:16px;box-shadow:0 20px 60px rgba(0,0,0,.45);overflow:hidden}
  header{padding:20px 24px;border-bottom:1px solid var(--border);display:grid;gap:6px}
  header h1{margin:0;font-size:20px;letter-spacing:.2px}
  header p{margin:0;color:var(--muted);font-size:14px}
  .progressbar{height:10px;background:#0a0f1c;border:1px solid var(--border);border-radius:999px;overflow:hidden;margin-top:10px}
  .progressbar>div{height:100%;width:0;background:linear-gradient(90deg,var(--accent) 0%,var(--accent2) 100%);transition:width .24s ease}
  .content{padding:20px 24px;display:grid;gap:16px}
  .card{background:#0b1324;border:1px solid var(--border);border-radius:12px;padding:16px}
  .meta{display:flex;gap:10px;align-items:center;color:var(--muted);font-size:14px}
  .pill{border:1px solid var(--border);padding:4px 10px;border-radius:999px}
  .question{font-size:18px;margin:4px 0 12px}
  .choices{display:grid;gap:10px;margin:8px 0}
  .choice{display:flex;gap:10px;align-items:start;border:1px solid var(--border);border-radius:10px;padding:10px 12px;background:#0a1120;cursor:pointer;flex-direction:column}
  .choice input{margin-top:3px}
  .choice.correct{outline:1px solid rgba(52,211,153,.5)}
  .choice.incorrect{outline:1px solid rgba(248,113,113,.5)}
  .hint{margin-top:6px;font-size:14px;color:var(--muted)}
  .links{margin-top:10px;display:flex;gap:10px;flex-wrap:wrap}
  .links a{color:var(--accent);text-decoration:none;border-bottom:1px dashed rgba(56,189,248,.4)}
  .feedback{font-size:14px;margin-top:6px}
  .feedback.ok{color:var(--good)}
  .feedback.err{color:var(--bad)}
  .controls{display:flex;justify-content:space-between;gap:12px;margin-top:6px}
  .controls .left,.controls .right{display:flex;gap:10px}
  button{background:#101933;color:var(--text);border:1px solid var(--border);padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600}
  button.primary{background:linear-gradient(90deg,var(--accent) 0%,var(--accent2) 100%);color:#051026;border:none}
  button:disabled{opacity:.5;cursor:not-allowed}
  .summary{font-size:15px;color:var(--muted)}
</style>

  <div class="app" role="application" aria-label="Setup Navigator Quiz — macOS">
    <header>
      <h1>Setup Navigator — macOS (Quiz)</h1>
      <p>Turn your macOS setup guide into bite-size checks. Pick the correct answer to advance.</p>
      <div class="progressbar" aria-hidden="true"><div id="progressFill"></div></div>
      <div class="meta">
        <span class="pill" id="stepCounter">Step 1/1</span>
        <span class="pill" id="trackPill">Track: macOS</span>
        <span class="pill" id="savePill">Progress: Auto-Saved</span>
      </div>
    </header>
    <main class="content">
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
      <section id="finalCard" class="card" hidden>
        <h2>All Done ✅</h2>
        <p class="summary" id="summaryText"></p>
        <div class="links" id="finalLinks"></div>
        <div class="controls">
          <div class="left"><button id="restartBtn">Restart</button></div>
          <div class="right"><button id="exportBtn" class="primary">Export Progress (.json)</button></div>
        </div>
      </section>
    </main>
  </div>

<script>
/* ---------- persistence + engine ---------- */
const STORAGE_KEY = "setup_navigator_macos_v1";
const state = { answers:{}, flags:{ os:"macos" }, index:0, filteredSteps:[] };

function save(){ localStorage.setItem(STORAGE_KEY, JSON.stringify({answers:state.answers,flags:state.flags,ts:Date.now()})); }
function load(){ try{ const raw=localStorage.getItem(STORAGE_KEY); if(!raw) return; const p=JSON.parse(raw); state.answers=p.answers||{}; state.flags=p.flags||{os:"macos"}; }catch{} }
function resetAll(){ localStorage.removeItem(STORAGE_KEY); state.answers={}; state.flags={os:"macos"}; state.index=0; state.filteredSteps=STEPS.slice(); render(); }
function getSelectedKeys(id){ return state.answers[id]||[]; }
function setSelectedKeys(id, keys){ state.answers[id]=Array.isArray(keys)?keys:[keys]; save(); }
function currentStep(){ return state.filteredSteps[state.index]; }
function percentComplete(){ if(!state.filteredSteps.length) return 0; const answered=state.filteredSteps.filter(s=>getSelectedKeys(s.id).length>0).length; return Math.round((answered/state.filteredSteps.length)*100); }

function render(){
  const step=currentStep(), stepCard=document.getElementById("stepCard"), finalCard=document.getElementById("finalCard");
  document.getElementById("progressFill").style.width=percentComplete()+"%";
  document.getElementById("savePill").textContent="Progress: Auto-Saved";
  if(!step){ stepCard.hidden=true; finalCard.hidden=false; return renderFinal(); }
  stepCard.hidden=false; finalCard.hidden=true;

  document.getElementById("stepId").textContent = step.id;
  document.getElementById("questionText").textContent = step.question;

  const hintEl=document.getElementById("hint");
  if(step.hint){ hintEl.textContent=step.hint; hintEl.hidden=false; } else hintEl.hidden=true;

  const choicesEl=document.getElementById("choices"); choicesEl.innerHTML="";
  const selected=new Set(getSelectedKeys(step.id));
  step.choices.forEach(ch=>{
    const row=document.createElement("label"); row.className="choice";
    const input=document.createElement("input"); input.type="radio"; input.name="choice"; input.value=ch.key; input.checked=selected.has(ch.key); input.ariaLabel=ch.label;
    input.addEventListener("change",()=>{ setSelectedKeys(step.id,[ch.key]); document.getElementById("feedback").textContent=""; for(const el of document.querySelectorAll(".choice")) el.classList.remove("correct","incorrect"); save(); });
    const text=document.createElement("div"); const strong=document.createElement("div"); strong.textContent=ch.label; text.appendChild(strong);
    row.appendChild(input); row.appendChild(text); choicesEl.appendChild(row);
  });

  const linksEl=document.getElementById("links"); linksEl.innerHTML="";
  (step.links||[]).forEach(l=>{ const a=document.createElement("a"); a.href=l.href; a.target="_blank"; a.rel="noopener noreferrer"; a.textContent=l.text; linksEl.appendChild(a); });

  const idx=state.index;
  document.getElementById("stepCounter").textContent=`Step ${idx+1}/${state.filteredSteps.length||1}`;
  document.getElementById("backBtn").disabled = idx===0;
  document.getElementById("nextBtn").disabled = !getSelectedKeys(step.id).length;
  document.getElementById("feedback").textContent="";
  for(const el of document.querySelectorAll(".choice")) el.classList.remove("correct","incorrect");
}

function checkAnswer(){
  const step=currentStep(); if(!step) return;
  const selected=getSelectedKeys(step.id); const fb=document.getElementById("feedback");
  if(!selected.length){ fb.textContent="Select an option to check."; fb.className="feedback err"; return; }
  const picked=selected[0]; const choice=step.choices.find(c=>c.key===picked); const isCorrect=!!choice?.correct;

  const choiceEls=[...document.querySelectorAll(".choice")]; 
  const pickedIdx=step.choices.findIndex(c=>c.key===picked);
  if(pickedIdx>=0) choiceEls[pickedIdx].classList.add(isCorrect?"correct":"incorrect");

  if(isCorrect){ 
    fb.textContent="Correct — next step unlocked."; 
    fb.className="feedback ok"; 
    document.getElementById("nextBtn").disabled=false;

    // ✅ show explanation if exists
    if(choice.explanation){
      const exp=document.createElement("div");
      exp.className="hint";
      exp.textContent="💡 " + choice.explanation;
      choiceEls[pickedIdx].appendChild(exp);
    }
  }
  else { 
    fb.textContent="Not quite. Check the hint/links and try again."; 
    fb.className="feedback err"; 
  }
}
function goNext(){ const step=currentStep(); if(!step) return;
  const sel=getSelectedKeys(step.id); if(!sel.length) return;
  if(!step.choices.find(c=>c.key===sel[0] && c.correct)) return;
  state.index++; if(state.index>=state.filteredSteps.length){ document.getElementById("stepCard").hidden=true; document.getElementById("finalCard").hidden=false; return renderFinal(); }
  render();
}
function goBack(){ if(state.index===0) return; state.index--; render(); }
function renderFinal(){
  const all=state.filteredSteps, answered=Object.keys(state.answers).length;
  const correctCount=all.reduce((n,s)=>{ const sel=getSelectedKeys(s.id); if(!sel.length) return n; const ok=s.choices.some(c=>c.key===sel[0] && c.correct); return n+(ok?1:0); },0);
  document.getElementById("summaryText").innerHTML = `You completed <b>${all.length}</b> steps. Answered: <b>${answered}</b>, Correct: <b>${correctCount}</b>.`;
  const finalLinks=document.getElementById("finalLinks"); finalLinks.innerHTML="";
  const a=document.createElement("a"); a.href="#"; a.textContent="Restart from scratch"; a.addEventListener("click",e=>{e.preventDefault(); resetAll();}); finalLinks.appendChild(a);
}
function exportProgress(){
  const data={answers:state.answers,flags:state.flags,steps:state.filteredSteps.map(s=>s.id),exportedAt:new Date().toISOString()};
  const blob=new Blob([JSON.stringify(data,null,2)],{type:"application/json"}); const url=URL.createObjectURL(blob);
  const a=document.createElement("a"); a.href=url; a.download="macos-setup-progress.json"; document.body.appendChild(a); a.click(); setTimeout(()=>{URL.revokeObjectURL(url); a.remove();},0);
}

/* ---------- macOS steps with explanations ---------- */
const STEPS = [
  { id:"intro.scope",
    question:"Which platform does this module cover right now?",
    choices:[
      {key:"mac", label:"macOS", correct:true, explanation:"This guide focuses on macOS setup, including tools like Homebrew, Git, and VS Code."},
      {key:"win", label:"Windows", correct:false},
      {key:"kasm", label:"Kasm (browser workspace)", correct:false},
    ]
  },
  { id:"terminal.which",
    question:"Which app do you open to run setup commands?",
    choices:[
      {key:"a", label:"Terminal", correct:true, explanation:"Terminal is where you run shell commands such as ls, cd, and git."},
      {key:"b", label:"Finder"},
      {key:"c", label:"System Settings"},
      {key:"d", label:"Safari"},
    ]
  },
  { id:"shell.pwd",
    question:"Which command prints your current working directory?",
    choices:[
      {key:"a", label:"pwd", correct:true, explanation:"pwd (print working directory) shows your current folder path."},
      {key:"b", label:"ls"},
      {key:"c", label:"cd .."},
      {key:"d", label:"whoami"},
    ]
  },
  { id:"shell.mkdir",
    question:"Create a folder for course work named ocnpcs. Which command is correct?",
    choices:[
      {key:"a", label:"mkdir ocnpcs", correct:true, explanation:"mkdir creates a new directory named ocnpcs."},
      {key:"b", label:"mk ocnpcs"},
      {key:"c", label:"newdir ocnpcs"},
      {key:"d", label:"touch ocnpcs"},
    ]
  },
  { id:"shell.cd",
    question:"Move into that new folder. Which command is correct?",
    choices:[
      {key:"a", label:"cd ocnpcs", correct:true, explanation:"cd changes your working directory into ocnpcs."},
      {key:"b", label:"open ocnpcs"},
      {key:"c", label:"ls ocnpcs"},
      {key:"d", label:"cat ocnpcs"},
    ]
  },
  { id:"git.version",
    question:"Confirm Git is installed. What should succeed?",
    choices:[
      {key:"a", label:"git --version", correct:true, explanation:"git --version confirms Git is available on your system."},
      {key:"b", label:"git --install"},
      {key:"c", label:"git version install"},
      {key:"d", label:"brew git version"},
    ]
  },
  { id:"repo.clone",
    question:"Clone the classroom repo to your ocnpcs folder. Which command is correct?",
    choices:[
      {key:"a", label:"git clone https://github.com/Open-Coding-Society/student.git", correct:true, explanation:"git clone downloads the repo from GitHub to your local folder."},
      {key:"b", label:"git pull https://github.com/Open-Coding-Society/student.git"},
      {key:"c", label:"git download Open-Coding-Society/student"},
      {key:"d", label:"clone git Open-Coding-Society/student"},
    ]
  },
  { id:"cd.student",
    question:"Move into the cloned repo directory. Which command is correct?",
    choices:[
      {key:"a", label:"cd student", correct:true, explanation:"After cloning, cd student moves you into the project directory."},
      {key:"b", label:"cd Open-Coding-Society"},
      {key:"c", label:"cd .."},
      {key:"d", label:"open student ."},
    ]
  },
  { id:"script.activate_macos",
    question:"Run the macOS prerequisites script from the repo. Which command is correct?",
    choices:[
      {key:"a", label:"./scripts/activate_macos.sh", correct:true, explanation:"This script installs required dependencies for macOS."},
      {key:"b", label:"bash scripts/activate_mac.sh"},
      {key:"c", label:"source activate_macos"},
      {key:"d", label:"run activate_macos.sh"},
    ]
  },
  { id:"script.activate",
    question:"Run the script that prompts for your Git UID and Personal Email.",
    choices:[
      {key:"a", label:"./scripts/activate.sh", correct:true, explanation:"This script sets up your Git identity (name and email)."},
      {key:"b", label:"git config --global --prompt"},
      {key:"c", label:"./scripts/git_setup.sh"},
      {key:"d", label:"git init --email"},
    ]
  },
  { id:"script.venv",
    question:"Create your Python virtual environment from the repo helper. Which command is correct?",
    choices:[
      {key:"a", label:"./scripts/venv.sh", correct:true, explanation:"This helper script creates and activates your Python venv for the project."},
      {key:"b", label:"python -m venv env"},
      {key:"c", label:"make venv"},
      {key:"d", label:"brew install venv"},
    ]
  },
  { id:"venv.activate",
    question:"After opening a new terminal, how do you activate the virtual environment?",
    choices:[
      {key:"a", label:"source venv/bin/activate", correct:true, explanation:"This activates your Python virtual environment so packages are isolated."},
      {key:"b", label:"python venv"},
      {key:"c", label:"activate.sh"},
      {key:"d", label:"brew venv start"},
    ]
  },
  { id:"vs.open",
    question:"Open the project in VS Code from the terminal. Which command is correct?",
    choices:[
      {key:"a", label:"code .", correct:true, explanation:"code . launches VS Code in the current folder."},
      {key:"b", label:"open vs"},
      {key:"c", label:"vscode start"},
      {key:"d", label:"editor student"},
    ]
  }
];

/* ---------- init ---------- */
load();
state.filteredSteps = STEPS.slice();
render();

document.getElementById("checkBtn").addEventListener("click",checkAnswer);
document.getElementById("nextBtn").addEventListener("click",goNext);
document.getElementById("backBtn").addEventListener("click",goBack);
document.getElementById("resetBtn").addEventListener("click",()=>{ if(confirm("Clear all progress?")) resetAll(); });
document.getElementById("restartBtn").addEventListener("click",()=>{ resetAll(); });
document.getElementById("exportBtn").addEventListener("click",exportProgress);
</script>
