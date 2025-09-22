---
layout: base
title: Setup Quest — Kasm & CSP Tools
permalink: /tools/kasm-setup
---

<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Setup Quest — Tech Adventure</title>
  <style>
    :root{
      --bg:#0a0f1b; --card:#0b1220; --muted:#ffffff; --accent:#86e1ff;
      --accent2:#a7f3d0; --correct:#22c55e; --wrong:#f43f5e; --radius:14px;
      --ink:#e6eef6;
      color-scheme: dark;
    }
    *{box-sizing:border-box}
   body {
  font-family: Inter, ui-sans-serif, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  margin: 0;
  padding: 28px;
  color: var(--ink);
  -webkit-font-smoothing: antialiased;
background: url('{{ site.baseurl }}/assets/setup-adventure.webp') no-repeat center center fixed;
  background-size: cover;
}
body, h1, h2, h3, p, .subtitle, .tag, .btn, .hud, .card {
  text-shadow: 1px 1px 4px rgba(0,0,0,0.8);
}
  .wrap{max-width:980px;margin:0 auto;}
    header{display:flex;align-items:center;gap:14px;margin-bottom:18px}
    header h1{font-size:22px;margin:0}
    .subtitle{color:var(--muted);font-size:13px;margin-top:2px}
    .hud{display:flex;flex-wrap:wrap;gap:10px;align-items:center;margin-top:4px}
    .tag{background:rgba(255,255,255,0.04); border:1px solid rgba(255,255,255,0.06);
      padding:6px 10px; border-radius:999px; font-size:12px; color:var(--muted)}
    .xpbar{flex:1; min-width:220px; height:10px; border-radius:999px; background:rgba(255,255,255,0.06); position:relative}
    .xpbar > span{position:absolute; left:0; top:0; bottom:0; width:0%; background:linear-gradient(90deg,var(--accent),var(--accent2)); border-radius:999px}
    .hud-right{margin-left:auto; display:flex; gap:10px; align-items:center}
    .btn{background:var(--accent); color:#022; border:none; padding:8px 12px; border-radius:10px; font-weight:700; cursor:pointer}
    .btn.secondary{background:transparent;color:var(--muted);border:1px solid rgba(255,255,255,0.08)}
    .btn.ghost{background:transparent;color:var(--ink);border:1px dashed rgba(255,255,255,0.12)}
   .card {
  background: rgba(0, 0, 0, 0.7); /* solid dark background */
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: var(--radius);
  padding: 18px;
  box-shadow: 0 10px 28px rgba(2, 6, 23, 0.55);
  margin-bottom: 16px;
}
    .map{display:flex; gap:10px; align-items:center; overflow-x:auto; padding:10px 4px; margin-bottom:10px}
    .node{--size:28px;width:var(--size); height:var(--size); border-radius:50%; background:rgba(255,255,255,0.06); border:1px solid rgba(255,255,255,0.10);
      display:grid; place-items:center; font-size:12px; color:var(--muted); flex:0 0 auto}
    .node.active{background:linear-gradient(180deg, rgba(134,225,255,0.25), rgba(167,243,208,0.2)); color:#031b22; border-color:rgba(134,225,255,0.7)}
    .node.done{background:linear-gradient(180deg, rgba(34,197,94,0.35), rgba(16,185,129,0.25)); color:#052014; border-color:rgba(16,185,129,0.7)}
    .link{height:2px; width:36px; background:linear-gradient(90deg, rgba(255,255,255,0.08), rgba(255,255,255,0.18)); flex:0 0 auto}
    .question-number{font-weight:700;color:var(--muted);font-size:13px}
    .question-text{font-size:18px;margin:10px 0 14px}
    .choices{display:flex;flex-direction:column;gap:8px}
    .choice label{display:flex;gap:12px;align-items:center;padding:10px;border-radius:10px;
      background:rgba(255,255,255,0.02);cursor:pointer;border:1px solid rgba(255,255,255,0.08)}
    .choice input{width:18px;height:18px}
    .actions{display:flex;gap:8px;align-items:center;margin-top:14px;flex-wrap:wrap}
    .feedback{margin-top:10px;padding:10px;border-radius:10px;font-size:14px;border:1px solid transparent}
    .feedback.correct{background:rgba(34,197,94,0.12);border-color:rgba(34,197,94,0.28);color:var(--correct)}
    .feedback.wrong{background:rgba(244,63,94,0.10);border-color:rgba(244,63,94,0.22);color:var(--wrong)}
    .cover-title{font-size:18px; font-weight:800; margin-bottom:8px}
    .cover-sub{color:var(--muted); font-size:14px}
    .lore{margin-top:10px; font-size:13px; color:var(--muted); background:rgba(255,255,255,0.03);
      border:1px dashed rgba(255,255,255,0.12); border-radius:10px; padding:10px}
    .loot{display:flex; gap:10px; align-items:center; background:linear-gradient(180deg, rgba(134,225,255,0.06), rgba(167,243,208,0.06));
      border:1px solid rgba(134,225,255,0.25); padding:10px; border-radius:12px; margin-top:10px; font-size:13px; color:#d8fff4}
    .mascot{display:flex; gap:10px; align-items:center}
    .bubble{padding:10px 12px; border-radius:12px; background:rgba(255,255,255,0.04); border:1px solid rgba(255,255,255,0.08); font-size:13px; color:var(--muted)}
    .confetti{position:fixed; left:0; top:0; width:100%; height:100%; pointer-events:none; overflow:hidden}
    .confetti span{position:absolute; top:-10px; font-size:16px; opacity:0.9; animation:fall 1400ms linear forwards}
    @keyframes fall{to{ transform:translateY(110vh) rotate(540deg); opacity:1 }}
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <h1>Setup Quest</h1>
      <div class="subtitle">Prepare your CSP toolkit. Clear each check to advance the adventure.</div>
      <div class="hud">
        <span class="tag" id="rankTag">Rank: Novice Adventurer</span>
        <div class="xpbar"><span id="xpFill"></span></div>
        <div class="hud-right">
          <button id="resetBtn" class="btn secondary">Reset Quest</button>
          <button id="showAnswersBtn" class="btn ghost">Glimpse: Show correct choice (current)</button>
        </div>
      </div>
    </header>
  <div class="card">
      <div class="map" id="map"></div>
      <div class="mascot"><div class="bubble" id="mascotBubble">Welcome, adventurer! Click <strong>Continue</strong> when you’re ready to face the first trial.</div></div>
    </div>

   <main id="stage"></main>

  <div class="card">
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center">
        <button id="continueBtn" class="btn" style="display:none">Continue</button>
        <span class="tag" id="progressDisplay">Progress: 0 / 10</span>
        <span class="tag" id="xpDisplay">XP: 0</span>
        <span class="tag" id="streakDisplay">Streak: 0</span>
        <div style="margin-left:auto;color:var(--muted)" id="globalFeedback"></div>
      </div>
    </div>
  </div>

  <div class="confetti" id="confetti" aria-hidden="true"></div>

  <script>
    /* -------------------------
     * FULL QUESTIONS (10 total)
     * ------------------------- */
    const originalQuestions = [
      {
        text: "What repo did you fork?",
        choices: [
          "a. Mr M's student repo",
          "b. Open Coding Society's student repo",
          "c. Mr M's pages repo",
          "d. Open Coding Society's pages repo"
        ],
        correct: 1,
        explanation: "Fork the Open Coding Society student repository so you own it and can commit independently."
      },
      {
        text: "What must you do when making your account on Open Coding Society?",
        choices: [
          "a. Click to enable Kasm",
          "b. Ignore Kasm",
          "c. Use school email",
          "d. Make password short and sweet"
        ],
        correct: 0,
        explanation: "Enable Kasm so your account can launch Ubuntu in the browser."
      },
      {
        text: "Where must you access VS Code?",
        choices: [
          "a. GitHub",
          "b. VSCode.dev",
          "c. Jupyter Notebook",
          "d. Ubuntu Noble"
        ],
        correct: 3,
        explanation: "Use the VS Code inside the Ubuntu Noble Kasm session so files sync and git works properly."
      },
      {
        text: "What's the first command you type in the terminal to prepare for accessing repos?",
        choices: [
          "a. code .",
          "b. source venv/bin/activate",
          "c. mkdir opencs",
          "d. git pull"
        ],
        correct: 2,
        explanation: "Create a working directory (e.g., <code>mkdir opencs</code>) before cloning into it."
      },
      {
        text: "How do you make your first commit from VS Code?",
        choices: [
          "a. Make a change and press Ctrl+S",
          "b. Make a change, press Ctrl+S, Commit (✓), then Push",
          "c. Exit GitHub and close Ubuntu",
          "d. Make all changes in terminal and it will auto-save"
        ],
        correct: 1,
        explanation: "Save, commit in the Source Control panel (✓), then Push to GitHub."
      }
    ];

    const newQuestionsOrdered = [
      {
        text: "How can you access the Ubuntu cloud computer?",
        choices: [
          "a. put kasm.opencodingsociety.com into google",
          "b. put https://github.com/Open-Coding-Society/student.git into google",
          "c. open your student repository",
          "d. Search up VSCode on google"
        ],
        correct: 0,
        explanation: "Open the browser and go to <code>kasm.opencodingsociety.com</code> to launch the Ubuntu cloud computer."
      },
      {
        text: "How do you clone a GitHub directory?",
        choices: [
          "a. mkdir (link to the repository)",
          "b. git pull",
          "c. git merge",
          "d. git clone (link to the repository)"
        ],
        correct: 3,
        explanation: "Use <code>git clone &lt;repo-url&gt;</code> to copy a remote repository locally."
      },
      {
        text: "How can you open up the console on your VS Code?",
        choices: [
          "a. Close the tab and yell at Mr. M for help",
          "b. Click into extensions and download python",
          "c. Click help on the top left, then toggle developer tools",
          "d. Click testing and run make in the terminal on the bottom"
        ],
        correct: 2,
        explanation: "In VS Code: <em>Help → Toggle Developer Tools</em> opens the devtools console."
      },
      {
        text: "How can you make a Jupyter notebook with all the jokes inside?",
        choices: [
          "a. Click File → New File → select Python",
          "b. Make a new directory through the terminal and open Jupyter Notebook there",
          "c. Click File → New File → select Jupyter Notebook",
          "d. Go to <code>_config.yml</code> and change the minima to cayman"
        ],
        correct: 2,
        explanation: "Use <em>File → New File → Jupyter Notebook</em> to create a new <code>.ipynb</code>."
      },
      {
        text: "What command do you use to change the theme on your website?",
        choices: [
          "a. make use-\"The theme name\"",
          "b. bundle exec jekyll serve",
          "c. make enable-(\"The theme you want\")",
          "d. make start-(\"The theme you want\")"
        ],
        correct: 0,
        explanation: "Run <code>make use-&quot;THEME&quot;</code> to switch your site’s theme."
      }
    ];

    // Final ordered list of 10 questions
    const questions = [
      originalQuestions[0],
      originalQuestions[1],
      originalQuestions[2],
      originalQuestions[3],
      newQuestionsOrdered[0],
      newQuestionsOrdered[1],
      originalQuestions[4],
      newQuestionsOrdered[2],
      newQuestionsOrdered[3],
      newQuestionsOrdered[4]
    ];

    // Cover texts, one per question in the same order
    const coverTexts = [
      "Trial 1 — The Forking Fork: Answer before you fork the Repository",
      "Trial 2 — Kasm Keystone: Answer before you finish making your account on Open Coding Society",
      "Trial 3 — The Editor’s Gate: Answer before you open VS Code inside Kasm",
      "Trial 4 — Found the Basecamp: Answer before you open your repositories through terminal",
      "Trial 5 — Cloud Portal: Answer before you open your cloud computer",
      "Trial 6 — Binding Ritual: Answer before you clone your student repository",
      "Trial 7 — First Inscription: Answer before your first commit on VS Code",
      "Trial 8 — Console Crystal: Answer before you add the jokes (you’ll need the console)",
      "Trial 9 — The Notebook Tome: Answer before you add the jokes inside a notebook",
      "Trial 10 — Skinchanger: Answer before you change the theme for your website"
    ];

    /* -------------------------
     * STATE / UI ELEMENTS
     * ------------------------- */
    let currentQuestionIndex = 0;
    let phase = "cover"; // "cover" | "question"
    let xp = 0, streak = 0;
    const XP_PER_CORRECT = 100;
    const RANKS = [
      {name:"Novice Adventurer", threshold:0},
      {name:"Apprentice Tinkerer", threshold:300},
      {name:"Journeyman Builder", threshold:600},
      {name:"Senior Wayfinder", threshold:900},
      {name:"Master of Setup", threshold:1200}
    ];

    const stageEl = document.getElementById('stage');
    const resetBtn = document.getElementById('resetBtn');
    const showAnswersBtn = document.getElementById('showAnswersBtn');
    const progressDisplay = document.getElementById('progressDisplay');
    const globalFeedback = document.getElementById('globalFeedback');
    const continueBtn = document.getElementById('continueBtn');

    const xpDisplay = document.getElementById('xpDisplay');
    const streakDisplay = document.getElementById('streakDisplay');
    const xpFill = document.getElementById('xpFill');
    const rankTag = document.getElementById('rankTag');
    const mapEl = document.getElementById('map');
    const mascotBubble = document.getElementById('mascotBubble');

    const confettiEl = document.getElementById('confetti');

    /* -------------------------
     * MAP / HUD / CONFETTI
     * ------------------------- */
    function buildMap(){
      mapEl.innerHTML = '';
      for(let i=0;i<questions.length;i++){
        const node = document.createElement('div');
        node.className = 'node';
        node.textContent = i+1;
        if(i < currentQuestionIndex){ node.classList.add('done'); node.title = `Node ${i+1}: cleared`; }
        else if(i === currentQuestionIndex){ node.classList.add('active'); node.title = `Node ${i+1}: current`; }
        else { node.title = `Node ${i+1}: locked`; }
        mapEl.appendChild(node);
        if(i !== questions.length-1){
          const link = document.createElement('div');
          link.className = 'link';
          mapEl.appendChild(link);
        }
      }
    }

    function updateHUD(){
      progressDisplay.textContent = `Progress: ${Math.min(currentQuestionIndex, questions.length)} / ${questions.length}`;
      xpDisplay.textContent = `XP: ${xp}`;
      streakDisplay.textContent = `Streak: ${streak}`;
      const nextRank = RANKS.reduce((a,b)=> b.threshold<=xp ? b : a, RANKS[0]);
      rankTag.textContent = `Rank: ${nextRank.name}`;
      const nextIndex = Math.min(RANKS.findIndex(r=>r.threshold>xp), RANKS.length-1);
      const curIndex = Math.max(nextIndex-1,0);
      const curBase = RANKS[curIndex].threshold;
      const curCap = RANKS[Math.min(curIndex+1, RANKS.length-1)].threshold || (curBase+300);
      const pct = Math.max(0, Math.min(100, ((xp - curBase) / Math.max(1, curCap - curBase)) * 100));
      xpFill.style.width = pct + '%';
    }

    function setMascot(text){
      mascotBubble.innerHTML = text;
    }

    function confettiBurst(){
      const icons = ["✨","💎","🗡️","🛡️","⚙️","🔧","🧪","📦","🚀","🔮"];
      for(let i=0;i<24;i++){
        const s = document.createElement('span');
        s.textContent = icons[(Math.random()*icons.length)|0];
        s.style.left = (Math.random()*100) + 'vw';
        s.style.animationDelay = (Math.random()*0.3)+'s';
        confettiEl.appendChild(s);
        setTimeout(()=>{ confettiEl.removeChild(s); }, 1600);
      }
    }

    /* -------------------------
     * RENDER
     * ------------------------- */
    function render(){
      stageEl.innerHTML = '';
      globalFeedback.textContent = '';
      updateHUD();
      buildMap();

      if(currentQuestionIndex >= questions.length){
        const done = document.createElement('section');
        done.className = 'card';
        done.innerHTML = `
          <div class="cover-title">Quest Complete! 🎉</div>
          <div class="cover-sub">You cleared all ${questions.length} trials. Title earned: <strong>Master of Setup</strong>.</div>
          <div class="loot">
            <span>🏆</span>
            <div><strong>Loot Acquired:</strong> Setup Sigil — You may now speed-run future installs with confidence.</div>
          </div>
        `;
        stageEl.appendChild(done);
        confettiBurst();
        setMascot("Legendary! You’ve forged your toolkit. If you want to practice again, hit Reset Quest.");
        return;
      }

      if(phase === 'cover' && coverTexts[currentQuestionIndex]){
        renderCover(currentQuestionIndex, coverTexts[currentQuestionIndex]);
      } else {
        renderQuestion(currentQuestionIndex, questions[currentQuestionIndex]);
      }
    }

    function renderCover(index, text){
      const card = document.createElement('section');
      card.className = 'card';
      card.innerHTML = `
        <div class="cover-title">${text}</div>
        <div class="cover-sub">Press <strong>Continue</strong> to face Trial ${index + 1}.</div>
        <div class="lore">Archivist’s Note: Victory demands proof. Choose wisely—only a correct answer unlocks the next node.</div>
      `;
      const actions = document.createElement('div');
      actions.className = 'actions';
      const btn = document.createElement('button');
      btn.textContent = 'Continue';
      btn.addEventListener('click', () => {
        phase = 'question';
        setMascot(`Trial ${index+1}: Answer to advance.`);
        render();
      });
      actions.appendChild(btn);
      card.appendChild(actions);
      stageEl.appendChild(card);
    }

    function renderQuestion(index, q){
      const card = document.createElement('section');
      card.className = 'card';
      card.dataset.index = index;

      card.innerHTML = `
        <div class="question-number">Trial ${index + 1}</div>
        <div class="question-text">${q.text}</div>
      `;

      const choices = document.createElement('div');
      choices.className = 'choices';

      q.choices.forEach((choiceText, j) => {
        const choice = document.createElement('div');
        choice.className = 'choice';

        const id = `q${index}_c${j}`;
        const radio = document.createElement('input');
        radio.type = 'radio';
        radio.name = `q${index}`;
        radio.id = id;
        radio.value = j;

        const label = document.createElement('label');
        label.htmlFor = id;
        label.appendChild(radio);

        const span = document.createElement('span');
        span.innerHTML = choiceText;

        label.appendChild(span);
        choice.appendChild(label);
        choices.appendChild(choice);
      });

      card.appendChild(choices);

      const feedback = document.createElement('div');
      feedback.className = 'feedback';
      feedback.style.display = 'none';
      card.appendChild(feedback);

      const actions = document.createElement('div');
      actions.className = 'actions';

      const submit = document.createElement('button');
      submit.textContent = 'Lock in answer';

      const nextBtn = document.createElement('button');
      nextBtn.textContent = 'Advance';
      nextBtn.className = 'secondary';
      nextBtn.style.display = 'none';

      submit.addEventListener('click', () => {
        const selected = document.querySelector(`input[name="q${index}"]:checked`);
        const val = selected ? Number(selected.value) : null;

        feedback.style.display = 'none';
        feedback.className = 'feedback';

        if(val === null){
          feedback.innerHTML = 'No choice selected — the gate remains closed.';
          feedback.style.display = 'block';
          feedback.classList.add('wrong');
          setMascot("Pick one path before proceeding, hero.");
          return;
        }

        if(val === q.correct){
          feedback.innerHTML = 'Success! ' + (q.explanation ? q.explanation : '');
          feedback.style.display = 'block';
          feedback.classList.add('correct');

          // XP & streak
          streak += 1;
          xp += XP_PER_CORRECT;
          updateHUD();

          // lock choices
          card.querySelectorAll('input[type="radio"]').forEach(r => r.disabled = true);
          submit.disabled = true;

          nextBtn.style.display = '';
          nextBtn.focus();

          // ✅ Add your CapCut video below the explanation
          const video = document.createElement('video');
          video.controls = true;
          video.width = 560;
          video.preload = 'metadata';
          video.style.marginTop = '12px';
          video.setAttribute('playsinline','');
          // Use baseurl so it works on GitHub Pages with /CSP-team
          video.src = `{{ site.baseurl }}/assets/videos/q${index+1}.mp4`;
          feedback.appendChild(video);

          setMascot(`Gate ${index+1} opened. Collect your shard and advance!`);
        } else {
          streak = 0;
          updateHUD();
          const correctText = q.choices[q.correct];
          feedback.innerHTML = `Alas! Correct answer: <em>"${correctText}"</em>. ${q.explanation || ''}`;
          feedback.style.display = 'block';
          feedback.classList.add('wrong');
          setMascot("Missed this one. Study the hint above and try again.");
        }
      });

      nextBtn.addEventListener('click', () => {
        currentQuestionIndex++;
        phase = coverTexts[currentQuestionIndex] ? 'cover' : 'question';
        if(currentQuestionIndex === questions.length){ confettiBurst(); }
        render();
      });

      actions.appendChild(submit);
      actions.appendChild(nextBtn);
      card.appendChild(actions);
      stageEl.appendChild(card);
    }

    showAnswersBtn.addEventListener('click', () => {
      if(phase !== 'question' || currentQuestionIndex >= questions.length) return;
      const q = questions[currentQuestionIndex];
      if(typeof q.correct !== 'number') return;
      const correctChoice = document.querySelector(`input[name="q${currentQuestionIndex}"][value="${q.correct}"]`);
      if(!correctChoice) return;
      const label = correctChoice.closest('label') || correctChoice.parentElement;
      label.style.outline = '3px solid rgba(134,225,255,0.35)';
      label.style.boxShadow = '0 0 0 4px rgba(134,225,255,0.08)';
      setTimeout(() => { label.style.outline = ''; label.style.boxShadow = ''; }, 1600);
      setMascot("The Archivist grants a glimpse of truth…");
    });

    // Click whole label to select radio
    document.addEventListener('click', (e) => {
      const label = e.target.closest('label');
      if(label){
        const input = label.querySelector('input[type="radio"]');
        if(input){ input.checked = true; }
      }
    });

    // Reset
    resetBtn.addEventListener('click', () => {
      currentQuestionIndex = 0;
      phase = coverTexts[0] ? 'cover' : 'question';
      streak = 0; xp = 0;
      setMascot("The quest begins anew. Fortune favors the bold.");
      render();
    });

    // Optional continue button (not strictly needed, cover has one)
    continueBtn.addEventListener('click', () => {
      if(phase === 'cover'){
        phase = 'question';
        render();
      }
    });

    // INIT
    phase = coverTexts[0] ? 'cover' : 'question';
    render();
  </script>
</body>
</html>

