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
  <title>Multiple Choice Quiz — Set up </title>
  <style>
    /* Simple modern styling */
    :root{
      --bg:#0f1724; --card:#0b1220; --muted:#9aa4b2; --accent:#7dd3fc;
      --correct:#16a34a; --wrong:#ef4444;
      --radius:12px;
      color-scheme: dark;
    }
    *{box-sizing:border-box}
    body{
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      margin:0; padding:32px; background:linear-gradient(180deg,#071024 0%, #081223 100%);
      color: #e6eef6;
      -webkit-font-smoothing:antialiased;
    }
    .wrap{max-width:880px;margin:0 auto;}
    header{display:flex;align-items:center;gap:16px;margin-bottom:18px}
    header h1{font-size:20px;margin:0}
    .card{
      background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border-radius:var(--radius); padding:18px; box-shadow: 0 6px 18px rgba(2,6,23,0.6);
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
    .results{margin-top:12px;font-weight:700}
    .feedback{margin-top:8px;padding:10px;border-radius:8px;font-size:14px}
    .feedback.correct{background:rgba(22,163,74,0.12);border:1px solid rgba(22,163,74,0.18);color:var(--correct)}
    .feedback.wrong{background:rgba(239,68,68,0.08);border:1px solid rgba(239,68,68,0.14);color:var(--wrong)}
    .meta{font-size:13px;color:var(--muted);margin-bottom:8px}
    footer{margin-top:18px;color:var(--muted);font-size:13px}
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
        <div class="meta">Edit the <code>questions</code> array in the script (below) to add your questions and answers.</div>
      </div>
    </header>

    <main id="quiz"></main>

    <div class="card">
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center">
        <button id="submitBtn">Submit answers</button>
        <button id="resetBtn" class="secondary">Reset</button>
        <button id="showAnswersBtn" class="secondary">Show correct answers</button>
        <div style="margin-left:auto;color:var(--muted)" id="scoreDisplay">Score: —</div>
      </div>
      <div id="globalFeedback" style="margin-top:12px"></div>
    </div>

    <footer>
      Tip: to add questions, edit the `questions` array in the &lt;script&gt; section. Each question may have any number of choices. The `correct` property is the index (0-based) of the correct choice.
    </footer>
  </div>

  <script>
    /**************************************************************************
     * HOW TO USE
     *
     * Replace or add objects in the `questions` array below.
     * Each question object:
     *  - text: string (question text)
     *  - choices: array of strings (answers)
     *  - correct: number (index of correct choice; optional if not used)
     *  - explanation: string (optional, shown after submitting)
     *
     * Example:
     * { text: "What is 2+2?", choices: ["3","4","5"], correct: 1, explanation: "2+2=4" }
     **************************************************************************/

    const questions = [
      // ===== SAMPLE QUESTIONS - replace these with your own =====
      {
        text: "What repo did you fork?",
        choices: ["Mr M's student repo", "Open Coding Society's student repo", "Mr M's pages repo", "Open Coding Society's pages repo"],
        correct: 2,
        explanation: "You must fork the student repository from Open Coding Society so that it isn't tied to Mr. M's personal student repo and so you can make commits and own the repo."
      },
      {
        text: "What must you do when making your account on Open Coding Society?",
        choices: ["Click to enable Kasm", "Ignore Kasm", "Use school email", "Make password short and sweet"],
        correct: 1,
        explanation: "You must click to enable Kasm so that your account is able to use Kasm on Ubuntu website."
      }

{
        text: "Where must you access VSCode?",
        choices: ["GitHub", "VSCode.dev", "Jupyter Notebook", "Ubuntu Noble"],
        correct: 4,
        explanation: "You must access VSCode from Ubuntu Noble in order to be able to synch across all platforms including GitHub and commit."
      }

      {
        text: "Whats the first line of code you must type into terminal in order to access your repos?",
        choices: ["code .", "source venv/bin/activate", "mkdir opencs", "git pull"],
        correct: 2,
        explanation: "In order to access your repos, you must make your directory on your computer."
      }

{
        text: "How do you make your first commit?",
        choices: ["Make a change and press ctrl s", "make a change, press ctrl s, push", "Exit GitHub and close Ubuntu", "Make all changes in terminal and it will save"],
        correct: 2,
        explanation: "You must first make a change that you want to commit. Then you must save the change by pressing ctrl s. Finally, you must push to commit the change."
      }

      // =========================================================
      // Add or remove objects here.
    ];

    // ---------- render quiz ----------
    const quizEl = document.getElementById('quiz');

    function renderQuiz(){
      quizEl.innerHTML = '';
      questions.forEach((q, i) => {
        const card = document.createElement('section');
        card.className = 'card';
        card.dataset.index = i;

        const qNum = document.createElement('div');
        qNum.className = 'question-number';
        qNum.textContent = `Question ${i + 1}`;
        card.appendChild(qNum);

        const qText = document.createElement('div');
        qText.className = 'question-text';
        qText.innerHTML = q.text;
        card.appendChild(qText);

        const choices = document.createElement('div');
        choices.className = 'choices';
        q.choices.forEach((choiceText, j) => {
          const choice = document.createElement('div');
          choice.className = 'choice';

          const id = `q${i}_c${j}`;
          const radio = document.createElement('input');
          radio.type = 'radio';
          radio.name = `q${i}`;
          radio.id = id;
          radio.value = j;
          radio.setAttribute('data-q', i);
          radio.setAttribute('data-choice', j);

          const label = document.createElement('label');
          label.htmlFor = id;
          label.appendChild(radio);

          const span = document.createElement('span');
          span.innerHTML = choiceText;

          label.appendChild(span);
          choice.appendChild(label);
          choices.appendChild(choice);

          // keyboard-friendly: allow label click to toggle selection
          label.addEventListener('keydown', (e) => {
            if(e.key === 'Enter' || e.key === ' ') {
              e.preventDefault();
              radio.checked = true;
            }
          });
        });

        card.appendChild(choices);

        // placeholder for per-question feedback
        const feedback = document.createElement('div');
        feedback.className = 'feedback';
        feedback.style.display = 'none';
        card.appendChild(feedback);

        quizEl.appendChild(card);
      });
    }

    renderQuiz();

    // ---------- scoring and behavior ----------
    const submitBtn = document.getElementById('submitBtn');
    const resetBtn = document.getElementById('resetBtn');
    const showAnswersBtn = document.getElementById('showAnswersBtn');
    const scoreDisplay = document.getElementById('scoreDisplay');
    const globalFeedback = document.getElementById('globalFeedback');

    function getSelectedAnswer(qIndex){
      const selected = document.querySelector(`input[name="q${qIndex}"]:checked`);
      return selected ? Number(selected.value) : null;
    }

    function grade(){
      let correctCount = 0;
      questions.forEach((q, i) => {
        const card = quizEl.querySelector(`section[data-index="${i}"]`);
        const feedback = card.querySelector('.feedback');
        const selected = getSelectedAnswer(i);

        // Reset previous styles
        feedback.style.display = 'none';
        feedback.className = 'feedback';

        if(selected === null){
          feedback.textContent = 'No answer selected.';
          feedback.style.display = 'block';
          feedback.classList.add('wrong');
          return;
        }

        if(typeof q.correct === 'number'){
          if(selected === q.correct){
            feedback.textContent = 'Correct.' + (q.explanation ? ' ' + q.explanation : '');
            feedback.style.display = 'block';
            feedback.classList.add('correct');
            correctCount++;
          } else {
            const correctText = q.choices[q.correct] ?? '(answer not provided)';
            feedback.textContent = `Wrong. Correct answer: "${correctText}".` + (q.explanation ? ' ' + q.explanation : '');
            feedback.style.display = 'block';
            feedback.classList.add('wrong');
          }
        } else {
          // If no correct index provided, just mark answered
          feedback.textContent = 'Answer recorded.' + (q.explanation ? ' ' + q.explanation : '');
          feedback.style.display = 'block';
          feedback.classList.add('correct');
          correctCount++; // count as "answered"
        }
      });

      // display overall score (only if correct indexes were provided)
      const totalWithKey = questions.filter(q => typeof q.correct === 'number').length;
      if(totalWithKey > 0){
        scoreDisplay.textContent = `Score: ${correctCount} / ${totalWithKey}`;
        globalFeedback.innerHTML = `<div class="card" style="margin-top:12px"><strong>Result:</strong> You got ${correctCount} out of ${totalWithKey} correct.</div>`;
      } else {
        scoreDisplay.textContent = `Answered: ${correctCount} / ${questions.length}`;
        globalFeedback.innerHTML = `<div class="card" style="margin-top:12px"><strong>Note:</strong> No answer keys provided; marking answered questions as 'correct'. Add a <code>correct</code> number to each question to enable scoring.</div>`;
      }
    }

    submitBtn.addEventListener('click', () => {
      grade();
      // scroll to first unanswered or wrong if you want:
      // const firstWrong = quizEl.querySelector('.feedback.wrong');
      // if(firstWrong) firstWrong.scrollIntoView({behavior:'smooth', block:'center'});
    });

    resetBtn.addEventListener('click', () => {
      document.querySelectorAll('input[type="radio"]').forEach(r => r.checked = false);
      document.querySelectorAll('.feedback').forEach(f => { f.style.display='none'; f.className='feedback'; });
      scoreDisplay.textContent = 'Score: —';
      globalFeedback.innerHTML = '';
    });

    showAnswersBtn.addEventListener('click', () => {
      // reveal correct answers by temporarily highlighting labels
      questions.forEach((q, i) => {
        if(typeof q.correct !== 'number') return;
        const correctChoice = document.querySelector(`input[name="q${i}"][value="${q.correct}"]`);
        if(!correctChoice) return;
        const label = correctChoice.closest('label') || correctChoice.parentElement;
        // flash the correct answer
        label.style.outline = '3px solid rgba(125,211,252,0.14)';
        setTimeout(() => label.style.outline = '', 2000);
      });
    });

    // Optional: enable Enter to submit when focus is inside the quiz
    quizEl.addEventListener('keydown', (e) => {
      if(e.key === 'Enter' && (e.ctrlKey || e.metaKey)){
        submitBtn.click();
      }
    });

    // Accessibility: let clicking the whole label check the input
    document.addEventListener('click', (e) => {
      const label = e.target.closest('label');
      if(label){
        const input = label.querySelector('input[type="radio"]');
        if(input){
          input.checked = true;
        }
      }
    });

    // If you prefer to author questions directly in HTML instead of the questions array,
    // you can replace renderQuiz() with your HTML blocks and keep the grading functions.
  </script>
</body>
</html>
