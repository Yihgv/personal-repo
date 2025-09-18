---
layout: base
title: Kasm Setup
permalink: /tools/kasm-setup
---
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Multiple Choice Quiz — Set up</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --muted:#9aa4b2; --accent:#7dd3fc;
      --correct:#16a34a; --wrong:#ef4444; --radius:12px;
      color-scheme: dark;
    }
    *{box-sizing:border-box}
    body{
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      margin:0; padding:32px; background:linear-gradient(180deg,#071024 0%, #081223 100%);
      color:#e6eef6; -webkit-font-smoothing:antialiased;
    }
    .wrap{max-width:880px;margin:0 auto;}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
    header h1{font-size:20px;margin:0}
    .card{
      background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border-radius:var(--radius); padding:18px; box-shadow:0 6px 18px rgba(2,6,23,0.6);
      margin-bottom:14px;
    }
    .question-number{font-weight:600;color:var(--muted);font-size:13px}
    .question-text{font-size:18px;margin:8px 0 14px}
    .choices{display:flex;flex-direction:column;gap:8px}
    .choice label{
      display:flex;gap:12px;align-items:center;padding:10px;border-radius:10px;
      background:rgba(255,255,255,0.01);cursor:pointer;border:1px solid rgba(255,255,255,0.03)
    }
    .choice input{width:18px;height:18px}
    .actions{display:flex;gap:8px;align-items:center;margin-top:14px}
    button{background:var(--accent);border:none;color:#022;padding:10px 14px;border-radius:10px;cursor:pointer;font-weight:600}
    button.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--muted)}
    .feedback{margin-top:8px;padding:10px;border-radius:8px;font-size:14px}
    .feedback.correct{background:rgba(22,163,74,0.12);border:1px solid rgba(22,163,74,0.18);color:var(--correct)}
    .feedback.wrong{background:rgba(239,68,68,0.08);border:1px solid rgba(239,68,68,0.14);color:var(--wrong)}
    .meta{font-size:13px;color:var(--muted);margin-bottom:8px}
    footer{margin-top:18px;color:var(--muted);font-size:13px}
    .cover-title{font-size:18px; font-weight:700; margin-bottom:8px}
    .cover-sub{color:var(--muted); font-size:14px}
    @media (max-width:640px){
      body{padding:18px}
      .question-text{font-size:16px}
    }
  </style>
</head>
<body>
  <div class="wrap">
    <header>
      <img src="data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='36' height='36'><rect rx='9' width='36' height='36' fill='%237dd3fc'/><text x='50%' y='57%' font-size='18' font-family='Arial' fill='%23041a1f' text-anchor='middle'>Q</text></svg>" alt="" width="36" height="36">
      <div>
        <h1>Multiple-choice Quiz Set Up</h1>
        <div class="meta">Each question unlocks the next only if you answer correctly.</div>
      </div>
    </header>

    <main id="stage"></main>

    <div class="card">
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center">
        <button id="resetBtn" class="secondary">Reset</button>
        <button id="showAnswersBtn" class="secondary">Show correct choice (current)</button>
        <div style="margin-left:auto;color:var(--muted)" id="progressDisplay">Progress: 0 / 5</div>
      </div>
      <div id="globalFeedback" style="margin-top:12px"></div>
    </div>

    <footer>
      Tip: questions are 0-based keyed. Answer correctly to continue. Use Reset to start over.
    </footer>
  </div>

  <script>
    const questions = [
      {
        text: "What repo did you fork?",
        choices: [
          "Mr M's student repo",
          "Open Coding Society's student repo",
          "Mr M's pages repo",
          "Open Coding Society's pages repo"
        ],
        correct: 1,
        explanation: "Fork the Open Coding Society student repository so you own it and can commit independently."
      },
      {
        text: "What must you do when making your account on Open Coding Society?",
        choices: [
          "Click to enable Kasm",
          "Ignore Kasm",
          "Use school email",
          "Make password short and sweet"
        ],
        correct: 0,
        explanation: "Enable Kasm so your account can launch Ubuntu in the browser."
      },
      {
        text: "Where must you access VS Code?",
        choices: ["GitHub", "VSCode.dev", "Jupyter Notebook", "Ubuntu Noble"],
        correct: 3,
        explanation: "Use the VS Code inside the Ubuntu Noble Kasm session so files sync and git works properly."
      },
      {
        text: "What's the first command you type in the terminal to prepare for accessing repos?",
        choices: ["code .", "source venv/bin/activate", "mkdir opencs", "git pull"],
        correct: 2,
        explanation: "Create a working directory (e.g., <code>mkdir opencs</code>) before cloning into it."
      },
      {
        text: "How do you make your first commit from VS Code?",
        choices: [
          "Make a change and press Ctrl+S",
          "Make a change, press Ctrl+S, Commit (✓), then Push",
          "Exit GitHub and close Ubuntu",
          "Make all changes in terminal and it will auto-save"
        ],
        correct: 1,
        explanation: "Save, commit in the Source Control panel (✓), then Push to GitHub."
      }
    ];

    const coverTexts = [
      "Before question 1. Please answer this question before forking the Repository",
      "Before question  2. Please answer this question before you finish making your account on Open Coding Society",
      "Before question 3. Please answer this question before youopen up VS Code",
      "Before question 4: Please answer this question before you open up your repositories through terminal",
      "Before question 5: Please answer this question before you make your first commit on VS Code"
    ];

    let currentQuestionIndex = 0;
    let phase = "cover";
    const stageEl = document.getElementById('stage');
    const resetBtn = document.getElementById('resetBtn');
    const showAnswersBtn = document.getElementById('showAnswersBtn');
    const progressDisplay = document.getElementById('progressDisplay');
    const globalFeedback = document.getElementById('globalFeedback');

    function updateProgress(){
      progressDisplay.textContent = `Progress: ${Math.min(currentQuestionIndex, questions.length)} / ${questions.length}`;
    }

    function render(){
      stageEl.innerHTML = '';
      globalFeedback.innerHTML = '';
      updateProgress();

      if(currentQuestionIndex >= questions.length){
        const done = document.createElement('section');
        done.className = 'card';
        done.innerHTML = `
          <div class="cover-title">All done! 🎉</div>
          <div class="cover-sub">You answered all ${questions.length} questions correctly.</div>
        `;
        stageEl.appendChild(done);
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
        <div class="cover-sub">Click Continue to proceed to Question ${index + 1}.</div>
      `;
      const actions = document.createElement('div');
      actions.className = 'actions';
      const btn = document.createElement('button');
      btn.textContent = 'Continue';
      btn.addEventListener('click', () => {
        phase = 'question';
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
        <div class="question-number">Question ${index + 1}</div>
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
      submit.textContent = 'Submit answer';

      const nextBtn = document.createElement('button');
      nextBtn.textContent = 'Continue';
      nextBtn.className = 'secondary';
      nextBtn.style.display = 'none';

      submit.addEventListener('click', () => {
        const selected = document.querySelector(`input[name="q${index}"]:checked`);
        const val = selected ? Number(selected.value) : null;

        feedback.style.display = 'none';
        feedback.className = 'feedback';

        if(val === null){
          feedback.textContent = 'No answer selected.';
          feedback.style.display = 'block';
          feedback.classList.add('wrong');
          return;
        }

        if(val === q.correct){
          feedback.innerHTML = 'Correct.' + (q.explanation ? ' ' + q.explanation : '');
          feedback.style.display = 'block';
          feedback.classList.add('correct');

          // lock choices so explanation remains visible and answer can’t change
          card.querySelectorAll('input[type="radio"]').forEach(r => r.disabled = true);
          submit.disabled = true;

          // show manual Continue button (no auto-advance)
          nextBtn.style.display = '';
          nextBtn.focus();
        } else {
          const correctText = q.choices[q.correct];
          feedback.innerHTML = `Wrong. Correct answer: "${correctText}". ${q.explanation || ''}`;
          feedback.style.display = 'block';
          feedback.classList.add('wrong');
        }
      });

      nextBtn.addEventListener('click', () => {
        currentQuestionIndex++;
        phase = coverTexts[currentQuestionIndex] ? 'cover' : 'question';
        render();
      });

      actions.appendChild(submit);
      actions.appendChild(nextBtn);
      card.appendChild(actions);
      stageEl.appendChild(card);
    }

    resetBtn.addEventListener('click', () => {
      currentQuestionIndex = 0;
      phase = coverTexts[0] ? 'cover' : 'question';
      stageEl.querySelectorAll('input[type="radio"]').forEach(r => r.checked = false);
      globalFeedback.innerHTML = '';
      render();
    });

    showAnswersBtn.addEventListener('click', () => {
      if(phase !== 'question' || currentQuestionIndex >= questions.length) return;
      const q = questions[currentQuestionIndex];
      if(typeof q.correct !== 'number') return;
      const correctChoice = document.querySelector(`input[name="q${currentQuestionIndex}"][value="${q.correct}"]`);
      if(!correctChoice) return;
      const label = correctChoice.closest('label') || correctChoice.parentElement;
      label.style.outline = '3px solid rgba(125,211,252,0.14)';
      setTimeout(() => label.style.outline = '', 2000);
    });

    document.addEventListener('click', (e) => {
      const label = e.target.closest('label');
      if(label){
        const input = label.querySelector('input[type="radio"]');
        if(input){ input.checked = true; }
      }
    });

    phase = coverTexts[0] ? 'cover' : 'question';
    render();
  </script>
</body>
</html>
