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
    if(ch.sub){ const sub=document.createElement("div"); sub.className="hint"; sub.textContent=ch.sub; text.appendChild(sub); }
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
  const choiceEls=[...document.querySelectorAll(".choice")]; const pickedIdx=step.choices.findIndex(c=>c.key===picked);
  if(pickedIdx>=0) choiceEls[pickedIdx].classList.add(isCorrect?"correct":"incorrect");
  if(isCorrect){ fb.textContent="Correct — next step unlocked."; fb.className="feedback ok"; document.getElementById("nextBtn").disabled=false; }
  else { fb.textContent="Not quite. Check the hint/links and try again."; fb.className="feedback err"; }
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

/* ---------- macOS steps converted from your guide ---------- */
const STEPS = [
  { id:"intro.scope",
    question:"Which platform does this module cover right now?",
    hint:"We’ll add Windows/Kasm tracks later—this one is macOS only.",
    choices:[
      {key:"mac", label:"macOS", correct:true},
      {key:"win", label:"Windows", correct:false},
      {key:"kasm", label:"Kasm (browser workspace)", correct:false},
    ]
  },

  { id:"terminal.which",
    question:"Which app do you open to run setup commands?",
    choices:[
      {key:"a", label:"Terminal", correct:true},
      {key:"b", label:"Finder"},
      {key:"c", label:"System Settings"},
      {key:"d", label:"Safari"},
    ],
    hint:"Keep Terminal in the Dock for easy access."
  },

  { id:"shell.pwd",
    question:"Which command prints your current working directory?",
    choices:[
      {key:"a", label:"pwd", correct:true},
      {key:"b", label:"ls"},
      {key:"c", label:"cd .."},
      {key:"d", label:"whoami"},
    ],
    hint:"You’ll use ls, pwd, mkdir, cd, git, cat."
  },

  { id:"shell.mkdir",
    question:"Create a folder for course work named <code>ocnpcs</code>. Which command is correct?",
    choices:[
      {key:"a", label:"mkdir ocnpcs", correct:true},
      {key:"b", label:"mk ocnpcs"},
      {key:"c", label:"newdir ocnpcs"},
      {key:"d", label:"touch ocnpcs"},
    ]
  },

  { id:"shell.cd",
    question:"Move into that new folder. Which command is correct?",
    choices:[
      {key:"a", label:"cd ocnpcs", correct:true},
      {key:"b", label:"open ocnpcs"},
      {key:"c", label:"ls ocnpcs"},
      {key:"d", label:"cat ocnpcs"},
    ]
  },

  { id:"git.version",
    question:"Confirm Git is installed. What should succeed?",
    choices:[
      {key:"a", label:"git --version", correct:true},
      {key:"b", label:"git --install"},
      {key:"c", label:"git version install"},
      {key:"d", label:"brew git version"},
    ],
    links:[{text:"Install Git (if needed)", href:"https://git-scm.com/downloads"}]
  },

  { id:"repo.clone",
    question:"Clone the classroom repo to your <code>ocnpcs</code> folder. Which command is correct?",
    hint:"Run commands one line at a time.",
    choices:[
      {key:"a", label:"git clone https://github.com/Open-Coding-Society/student.git", correct:true},
      {key:"b", label:"git pull https://github.com/Open-Coding-Society/student.git"},
      {key:"c", label:"git download Open-Coding-Society/student"},
      {key:"d", label:"clone git Open-Coding-Society/student"},
    ],
    links:[{text:"Git clone docs", href:"https://git-scm.com/docs/git-clone"}]
  },

  { id:"cd.student",
    question:"Move into the cloned repo directory. Which command is correct?",
    choices:[
      {key:"a", label:"cd student", correct:true},
      {key:"b", label:"cd Open-Coding-Society"},
      {key:"c", label:"cd .."},
      {key:"d", label:"open student ."},
    ]
  },

  { id:"scripts.onebyone",
    question:"How should you run the upcoming script commands?",
    choices:[
      {key:"a", label:"One line at a time in Terminal", correct:true},
      {key:"b", label:"Paste them all at once"},
      {key:"c", label:"Run them in Finder"},
      {key:"d", label:"Double-click the .sh files"},
    ]
  },

  { id:"script.activate_macos",
    question:"Run the macOS prerequisites script from the repo. Which command is correct?",
    choices:[
      {key:"a", label:"./scripts/activate_macos.sh", correct:true},
      {key:"b", label:"bash scripts/activate_mac.sh"},
      {key:"c", label:"source activate_macos"},
      {key:"d", label:"run activate_macos.sh"},
    ]
  },

  { id:"script.activate",
    question:"Run the script that prompts for your Git UID and Personal Email.",
    choices:[
      {key:"a", label:"./scripts/activate.sh", correct:true},
      {key:"b", label:"git config --global --prompt"},
      {key:"c", label:"./scripts/git_setup.sh"},
      {key:"d", label:"git init --email"},
    ]
  },

  { id:"script.venv",
    question:"Create your Python virtual environment from the repo helper. Which command is correct?",
    choices:[
      {key:"a", label:"./scripts/venv.sh", correct:true},
      {key:"b", label:"python -m venv ."},
      {key:"c", label:"pip install venv"},
      {key:"d", label:"virtualenv activate"},
    ]
  },

  { id:"check.python",
    question:"Which command verifies Python is available?",
    choices:[
      {key:"a", label:"python --version", correct:true},
      {key:"b", label:"pip --python"},
      {key:"c", label:"python --install"},
      {key:"d", label:"py --v"},
    ]
  },

  { id:"check.pip",
    question:"Which command verifies pip is available?",
    choices:[
      {key:"a", label:"pip --version", correct:true},
      {key:"b", label:"pip install --version"},
      {key:"c", label:"python --pip"},
      {key:"d", label:"brew pip --version"},
    ]
  },

  { id:"check.ruby",
    question:"Which command prints your Ruby version?",
    choices:[
      {key:"a", label:"ruby -v", correct:true},
      {key:"b", label:"ruby --about"},
      {key:"c", label:"gem -v"},
      {key:"d", label:"bundler --ruby"},
    ]
  },

  { id:"check.bundler",
    question:"Which command prints your Bundler (Ruby) version?",
    choices:[
      {key:"a", label:"bundler -v", correct:true},
      {key:"b", label:"bundle version show"},
      {key:"c", label:"ruby --bundler"},
      {key:"d", label:"gem bundler -v"},
    ]
  },

  { id:"check.gem",
    question:"Which command prints your RubyGems version?",
    choices:[
      {key:"a", label:"gem --version", correct:true},
      {key:"b", label:"gen --version"},
      {key:"c", label:"gems -v"},
      {key:"d", label:"ruby --gems"},
    ]
  },

  { id:"git.config",
    question:"After running <code>git config --global --list</code>, which indicates identity is set correctly?",
    choices:[
      {key:"a", label:"Entries including user.name and user.email", correct:true},
      {key:"b", label:"Only core.autocrlf"},
      {key:"c", label:"An empty output"},
      {key:"d", label:"remote.origin.url only"},
    ]
  },

  { id:"vscode.open",
    question:"From the repo directory, open the folder in VS Code. Which command is correct?",
    choices:[
      {key:"a", label:"code .", correct:true},
      {key:"b", label:"open .code"},
      {key:"c", label:"vscode ./"},
      {key:"d", label:"open code"},
    ],
    hint:"If <code>code</code> isn’t found, enable “Shell Command: Install ‘code’ command in PATH” from VS Code’s Command Palette."
  },

  { id:"session.start",
    question:"Each time you start a new Terminal session to work, which sequence is correct?",
    choices:[
      {key:"a", label:"cd ocnpcs/student → source venv/bin/activate → code .", correct:true},
      {key:"b", label:"source venv/bin/activate → cd ocnpcs/student → code ."},
      {key:"c", label:"code . → cd ocnpcs/student → source venv/bin/activate"},
      {key:"d", label:"open student → activate venv → code"},
    ]
  },

  { id:"brew.list",
    question:"Which Homebrew command lists installed packages?",
    choices:[
      {key:"a", label:"brew list", correct:true},
      {key:"b", label:"brew packages"},
      {key:"c", label:"brew show --all"},
      {key:"d", label:"brew status"},
    ],
    links:[{text:"Homebrew Basics", href:"https://brew.sh/"}]
  },

  { id:"brew.search",
    question:"Search for a package in Homebrew. Which is correct?",
    choices:[
      {key:"a", label:"brew search &lt;package_name&gt;", correct:true},
      {key:"b", label:"brew find &lt;package_name&gt;"},
      {key:"c", label:"brew look &lt;package_name&gt;"},
      {key:"d", label:"brew query &lt;package_name&gt;"},
    ]
  },

  { id:"brew.update.upgrade",
    question:"Update Homebrew itself and upgrade installed packages. Which pair is correct?",
    choices:[
      {key:"a", label:"brew update  →  brew upgrade", correct:true},
      {key:"b", label:"brew refresh →  brew update"},
      {key:"c", label:"brew upgrade →  brew update"},
      {key:"d", label:"brew reinstall → brew refresh"},
    ]
  },

  { id:"brew.uninstall",
    question:"Uninstall a package with Homebrew. Which command is correct?",
    choices:[
      {key:"a", label:"brew uninstall &lt;package_name&gt;", correct:true},
      {key:"b", label:"brew remove --force &lt;package_name&gt;"},
      {key:"c", label:"brew delete &lt;package_name&gt;"},
      {key:"d", label:"brew purge &lt;package_name&gt;"},
    ]
  },

  { id:"sdlc.git",
    question:"In the SDLC cycle, which command records your changes in the local repo?",
    choices:[
      {key:"a", label:'git commit -m "message"', correct:true},
      {key:"b", label:"git push origin main"},
      {key:"c", label:"git pull"},
      {key:"d", label:"git clone"},
    ],
    hint:"Clone → edit → add/commit → push."
  },
];

/* wire up */
document.getElementById("checkBtn").addEventListener("click", checkAnswer);
document.getElementById("nextBtn").addEventListener("click", goNext);
document.getElementById("backBtn").addEventListener("click", goBack);
document.getElementById("resetBtn").addEventListener("click", resetAll);
document.getElementById("restartBtn")?.addEventListener("click", resetAll);
document.getElementById("exportBtn")?.addEventListener("click", exportProgress);

load(); state.filteredSteps = STEPS.slice(); render();
</script>
