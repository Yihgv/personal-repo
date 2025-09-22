---
layout: base
title: Setup Quest — Mac Setup
permalink: /tools/mac-setup
---

<title>Setup Navigator — macOS (Step-by-Step Quiz)</title>
<style>
  :root { --bg:#0f172a; --panel:#111827; --panel2:#0b1220; --text:#e5e7eb; --muted:#9ca3af; --accent:#38bdf8; --accent2:#22d3ee; --good:#34d399; --bad:#f87171; --border:#1f2937; }
  html,body{height:100%}
  /* Page background image with color fallback and overlay for readable text.
    Use Jekyll's relative_url for correct site builds and a local filesystem
    fallback so the image is visible in VS Code file previews. */
  body{margin:0;background-color:var(--bg);background-image:linear-gradient(rgba(3,7,18,0.55), rgba(3,7,18,0.55)), url('{{ "/assets/setup-adventure.webp" | relative_url }}'), url('../assets/setup-adventure.webp');background-size:cover;background-position:center;background-repeat:no-repeat;color:var(--text);font:16px/1.5 system-ui,-apple-system,Segoe UI,Roboto,Arial;display:grid;place-items:start center;padding:32px 16px 80px}
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
      <h1>Setup Navigator — macOS (Step-by-Step Quiz)</h1>
      <p>Follow the steps in order to set up your macOS development environment.</p>
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
const state = { answers:{}, flags:{ os:"macos" }, index:0, filteredSteps:[], shuffledChoices:{} };
function save(){ localStorage.setItem(STORAGE_KEY, JSON.stringify({answers:state.answers,flags:state.flags,shuffledChoices:state.shuffledChoices,ts:Date.now()})); }
function load(){ try{ const raw=localStorage.getItem(STORAGE_KEY); if(!raw) return; const p=JSON.parse(raw); state.answers=p.answers||{}; state.flags=p.flags||{os:"macos"}; state.shuffledChoices=p.shuffledChoices||{}; }catch{} }
function resetAll(){ localStorage.removeItem(STORAGE_KEY); state.answers={}; state.flags={os:"macos"}; state.index=0; state.filteredSteps=STEPS.slice(); state.shuffledChoices={}; render(); }
function getSelectedKeys(id){ return state.answers[id]||[]; }
function setSelectedKeys(id, keys){ state.answers[id]=Array.isArray(keys)?keys:[keys]; save(); }
function currentStep(){ return state.filteredSteps[state.index]; }
function percentComplete(){ if(!state.filteredSteps.length) return 0; const answered=state.filteredSteps.filter(s=>getSelectedKeys(s.id).length>0).length; return Math.round((answered/state.filteredSteps.length)*100); }

// Fisher-Yates shuffle algorithm
function shuffleArray(array) {
  const shuffled = [...array];
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
}

// Get shuffled choices for a step, maintaining consistency across renders
function getShuffledChoices(stepId, originalChoices) {
  if (!state.shuffledChoices[stepId]) {
    state.shuffledChoices[stepId] = shuffleArray(originalChoices);
    save();
  }
  return state.shuffledChoices[stepId];
}

function render(){ const step=currentStep(), stepCard=document.getElementById("stepCard"), finalCard=document.getElementById("finalCard"); document.getElementById("progressFill").style.width=percentComplete()+"%"; document.getElementById("savePill").textContent="Progress: Auto-Saved"; if(!step){ stepCard.hidden=true; finalCard.hidden=false; return renderFinal(); } stepCard.hidden=false; finalCard.hidden=true; document.getElementById("stepId").textContent = step.id; document.getElementById("questionText").textContent = step.question; const hintEl=document.getElementById("hint"); if(step.hint){ hintEl.textContent=step.hint; hintEl.hidden=false; } else hintEl.hidden=true; const choicesEl=document.getElementById("choices"); choicesEl.innerHTML=""; const selected=new Set(getSelectedKeys(step.id)); const shuffledChoices = getShuffledChoices(step.id, step.choices); shuffledChoices.forEach(ch=>{ const row=document.createElement("label"); row.className="choice"; const input=document.createElement("input"); input.type="radio"; input.name="choice"; input.value=ch.key; input.checked=selected.has(ch.key); input.setAttribute('aria-label', ch.label); input.addEventListener("change",()=>{ setSelectedKeys(step.id,[ch.key]); document.getElementById("feedback").textContent=""; for(const el of document.querySelectorAll(".choice")) el.classList.remove("correct","incorrect"); save(); }); const text=document.createElement("div"); const strong=document.createElement("div"); strong.textContent=ch.label; text.appendChild(strong); row.appendChild(input); row.appendChild(text); choicesEl.appendChild(row); }); const linksEl=document.getElementById("links"); linksEl.innerHTML=""; (step.links||[]).forEach(l=>{ const a=document.createElement("a"); a.href=l.href; a.target="_blank"; a.rel="noopener noreferrer"; a.textContent=l.text; linksEl.appendChild(a); }); const idx=state.index; document.getElementById("stepCounter").textContent=`Step ${idx+1}/${state.filteredSteps.length||1}`; document.getElementById("backBtn").disabled = idx===0; document.getElementById("nextBtn").disabled = !getSelectedKeys(step.id).length; document.getElementById("feedback").textContent=""; for(const el of document.querySelectorAll(".choice")) el.classList.remove("correct","incorrect"); }
function checkAnswer(){ const step=currentStep(); if(!step) return; const selected=getSelectedKeys(step.id); const fb=document.getElementById("feedback"); if(!selected.length){ fb.textContent="Select an option to check."; fb.className="feedback err"; return; } const picked=selected[0]; const choice=step.choices.find(c=>c.key===picked); const isCorrect=!!choice?.correct; const choiceEls=[...document.querySelectorAll(".choice")]; const shuffledChoices = getShuffledChoices(step.id, step.choices); const pickedIdx=shuffledChoices.findIndex(c=>c.key===picked); if(pickedIdx>=0) choiceEls[pickedIdx].classList.add(isCorrect?"correct":"incorrect"); if(isCorrect){ fb.textContent="Correct — next step unlocked."; fb.className="feedback ok"; document.getElementById("nextBtn").disabled=false; if(choice.explanation){ const exp=document.createElement("div"); exp.className="hint"; exp.textContent="💡 " + choice.explanation; choiceEls[pickedIdx].appendChild(exp); } } else { fb.textContent="Not quite. Check the hint/links and try again."; fb.className="feedback err"; } }
function goNext(){ const step=currentStep(); if(!step) return; const sel=getSelectedKeys(step.id); if(!sel.length) return; if(!step.choices.find(c=>c.key===sel[0] && c.correct)) return; state.index++; if(state.index>=state.filteredSteps.length){ document.getElementById("stepCard").hidden=true; document.getElementById("finalCard").hidden=false; return renderFinal(); } render(); }
function goBack(){ if(state.index===0) return; state.index--; render(); }
function renderFinal(){ const all=state.filteredSteps, answered=Object.keys(state.answers).length; const correctCount=all.reduce((n,s)=>{ const sel=getSelectedKeys(s.id); if(!sel.length) return n; const ok=s.choices.some(c=>c.key===sel[0] && c.correct); return n+(ok?1:0); },0); document.getElementById("summaryText").innerHTML = `You completed <b>${all.length}</b> steps. Answered: <b>${answered}</b>, Correct: <b>${correctCount}</b>.`; const finalLinks=document.getElementById("finalLinks"); finalLinks.innerHTML=""; const a=document.createElement("a"); a.href="#"; a.textContent="Restart from scratch"; a.addEventListener("click",e=>{e.preventDefault(); resetAll();}); finalLinks.appendChild(a); }
function exportProgress(){ const data={answers:state.answers,flags:state.flags,steps:state.filteredSteps.map(s=>s.id),exportedAt:new Date().toISOString()}; const blob=new Blob([JSON.stringify(data,null,2)],{type:"application/json"}); const url=URL.createObjectURL(blob); const a=document.createElement("a"); a.href=url; a.download="macos-setup-progress.json"; document.body.appendChild(a); a.click(); setTimeout(()=>{URL.revokeObjectURL(url); a.remove();},0); }

/* ---------- Ordered Step-by-Step Setup ---------- */
const STEPS = [
  { id:"open.terminal", question:"Step 1: Which app do you open to run setup commands?", choices:[ {key:"a", label:"Terminal", correct:true, explanation:"All setup commands are typed into the macOS Terminal."}, {key:"b", label:"Finder"}, {key:"c", label:"System Settings"}, {key:"d", label:"Safari"} ] },
  { id:"shell.pwd", question:"Step 2: Which command prints your current working directory?", choices:[ {key:"a", label:"pwd", correct:true, explanation:"Verify where you are before creating/navigating folders."}, {key:"b", label:"ls"}, {key:"c", label:"cd .."}, {key:"d", label:"whoami"} ] },
  { id:"brew.install", question:"Step 3: What do you install to manage developer tools on macOS?", links:[{text:"Homebrew Install", href:"https://brew.sh/"}], choices:[ {key:"a", label:"Homebrew", correct:true, explanation:"Homebrew is the macOS package manager used throughout this guide."}, {key:"b", label:"Xcode"}, {key:"c", label:"Mac App Store"}, {key:"d", label:"Nothing—macOS already has everything"} ] },
  { id:"vscode.install", question:"Step 4: Where do you install VS Code for Mac?", links:[{text:"VS Code Download", href:"https://code.visualstudio.com/"}], choices:[ {key:"a", label:"From code.visualstudio.com (macOS build)", correct:true, explanation:"Install the official macOS version of Visual Studio Code."}, {key:"b", label:"Only via Homebrew"}, {key:"c", label:"Xcode"}, {key:"d", label:"You don't need an editor"} ] },
  { id:"shell.mkdir", question:"Step 5: Create a folder for course work named opencs. Which command is correct?", choices:[ {key:"a", label:"mkdir opencs", correct:true, explanation:"mkdir creates a new directory named opencs."}, {key:"b", label:"mk opencs"}, {key:"c", label:"newdir opencs"}, {key:"d", label:"touch opencs"} ] },
  { id:"shell.cd", question:"Step 6: Move into the new folder. Which command is correct?", choices:[ {key:"a", label:"cd opencs", correct:true, explanation:"cd changes into the opencs folder you just created."}, {key:"b", label:"open opencs"}, {key:"c", label:"ls opencs"}, {key:"d", label:"cat opencs"} ] },
  { id:"repo.clone", question:"Step 7: Clone the student (personal) repo into opencs. Which command is correct?", links:[{text:"Student Repo", href:"https://github.com/Open-Coding-Society/student"}], choices:[ {key:"a", label:"git clone https://github.com/Open-Coding-Society/student.git", correct:true, explanation:"git clone downloads the repo into the current folder."}, {key:"b", label:"git pull https://github.com/Open-Coding-Society/student.git"}, {key:"c", label:"git download Open-Coding-Society/student"}, {key:"d", label:"clone git Open-Coding-Society/student"} ] },
  { id:"cd.student", question:"Step 8: Move into the cloned repo directory. Which command is correct?", choices:[ {key:"a", label:"cd student", correct:true, explanation:"After cloning, cd student moves you into the project."}, {key:"b", label:"cd Open-Coding-Society"}, {key:"c", label:"cd .."}, {key:"d", label:"open student ."} ] },
  { id:"script.activate_macos", question:"Step 9: Run the macOS prerequisites script from the repo. Which command is correct?", choices:[ {key:"a", label:"./scripts/activate_macos.sh", correct:true, explanation:"Installs required dependencies on macOS per the guide."}, {key:"b", label:"bash scripts/activate_mac.sh"}, {key:"c", label:"source activate_macos"}, {key:"d", label:"run activate_macos.sh"} ] },
  { id:"script.activate", question:"Step 10: Run the script that prompts for your Git UID and Personal Email.", choices:[ {key:"a", label:"./scripts/activate.sh", correct:true, explanation:"This sets your Git name and email used for commits."}, {key:"b", label:"git config --global --prompt"}, {key:"c", label:"./scripts/git_setup.sh"}, {key:"d", label:"git init --email"} ] },
  { id:"script.venv", question:"Step 11: Create your Python virtual environment from the repo helper. Which command is correct?", choices:[ {key:"a", label:"./scripts/venv.sh", correct:true, explanation:"Creates (and activates) the project's Python virtual environment."}, {key:"b", label:"python -m venv env"}, {key:"c", label:"make venv"}, {key:"d", label:"brew install venv"} ] },
  { id:"session.cd", question:"Step 12: Next time you open Terminal to work, which command moves you into the project?", choices:[ {key:"a", label:"cd opencs/student", correct:true, explanation:"Navigate into the repo before activating the venv and opening the editor."}, {key:"b", label:"cd ~"}, {key:"c", label:"cd venv"}, {key:"d", label:"cd /"} ] },
  { id:"venv.activate", question:"Step 13: After you're inside the project, how do you activate the virtual environment?", choices:[ {key:"a", label:"source venv/bin/activate", correct:true, explanation:"Activates the venv so packages are isolated for this repo."}, {key:"b", label:"python venv"}, {key:"c", label:"activate.sh"}, {key:"d", label:"brew venv start"} ] },
  { id:"vs.open", question:"Step 14: Open the project in VS Code from the terminal. Which command is correct?", choices:[ {key:"a", label:"code .", correct:true, explanation:"code . launches VS Code in the current folder (the student repo)."}, {key:"b", label:"open vs"}, {key:"c", label:"vscode start"}, {key:"d", label:"editor student"} ] },
  { id:"checks.python", question:"(Optional) Which command prints your Python version?", choices:[ {key:"a", label:"python --version", correct:true, explanation:"Use version checks later if tools stop working."}, {key:"b", label:"pip --which"}, {key:"c", label:"ruby --pip"}, {key:"d", label:"git version python"} ] },
  { id:"checks.pip", question:"(Optional) Which command prints your pip version?", choices:[ {key:"a", label:"pip --version", correct:true, explanation:"Verifies pip is installed correctly."}, {key:"b", label:"pip --which"}, {key:"c", label:"pip list --global"}, {key:"d", label:"which pip"} ] },
  { id:"checks.ruby", question:"(Optional) Which command prints your Ruby version?", choices:[ {key:"a", label:"ruby -v", correct:true, explanation:"Checks Ruby, used by some tooling like Bundler."}, {key:"b", label:"ruby --which"}, {key:"c", label:"gem -v ruby"}, {key:"d", label:"bundle -ruby"} ] },
  { id:"checks.bundle", question:"(Optional) Which command prints your Bundler version?", choices:[ {key:"a", label:"bundle -v", correct:true, explanation:"Ensures Bundler is available for Ruby projects."}, {key:"b", label:"bundler --which"}, {key:"c", label:"gem bundle --version"}, {key:"d", label:"bundle version list"} ] },
  { id:"checks.gem", question:"(Optional) Which command prints your RubyGems version?", choices:[ {key:"a", label:"gem --version", correct:true, explanation:"Verifies the RubyGems package manager."}, {key:"b", label:"gem list --global"}, {key:"c", label:"gem -which"}, {key:"d", label:"ruby gem version"} ] },
  { id:"checks.gitconfig", question:"(Optional) Which command lists your global Git config?", choices:[ {key:"a", label:"git config --global --list", correct:true, explanation:"Handy to verify that your name/email were set by activate.sh."}, {key:"b", label:"git list --global config"}, {key:"c", label:"git status --global"}, {key:"d", label:"git --help --global"} ] }
];

/* init */
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