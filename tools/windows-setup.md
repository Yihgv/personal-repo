---
title: Windows VS Code & GitHub Pages Quiz
comments: false
layout: post
permalink: /tools/windows-setup
---

<!-- Page-specific styles -->
<style>
  :root{--bg:#0f1724;--card:#0b1220;--text:#e6eef8;--muted:#9fb0c6;--accent:#6ee7b7;--wrong:#ff7b7b}
  body{font-family:Inter,ui-sans-serif,system-ui,Helvetica,Arial;margin:0;padding:32px;background:linear-gradient(180deg,#051226 0%, #071827 100%);color:var(--text)}
  .container{max-width:900px;margin:0 auto}
  header{display:flex;align-items:center;gap:16px;margin-bottom:20px}
  h1{margin:0;font-size:22px}
  p.lead{margin:0;color:var(--muted)}
  .card{background:var(--card);padding:20px;border-radius:12px;box-shadow:0 6px 24px rgba(2,6,23,0.6);margin-bottom:16px}
  .q{margin:14px 0;padding:12px;border-radius:8px;background:rgba(255,255,255,0.02)}
  .q h3{margin:0 0 8px 0;font-size:16px}
  label.choice{display:block;padding:8px;border-radius:6px;margin:6px 0;cursor:pointer}
  input[type=radio]{margin-right:10px}
  .controls{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
  button{background:var(--accent);border:0;padding:10px 14px;border-radius:8px;cursor:pointer;color:#042018;font-weight:600}
  button.secondary{background:transparent;border:1px solid rgba(255,255,255,0.06);color:var(--text)}
  .result{padding:12px;border-radius:8px;margin-top:12px}
  .correct{background:rgba(110,231,183,0.12);color:var(--accent);border:1px solid rgba(110,231,183,0.12)}
  .incorrect{background:rgba(255,123,123,0.08);color:var(--wrong);border:1px solid rgba(255,123,123,0.06)}
  small.help{display:block;color:var(--muted);margin-top:6px}
  footer{margin-top:18px;color:var(--muted);font-size:13px}
  .meta{font-size:13px;color:var(--muted);margin-bottom:8px}
</style>

<div class="container">
  <header>
    <div>
      <h1>Windows — VS Code & GitHub Pages Quiz</h1>
      <p class="lead">Short interactive multiple-choice quiz based on the VS Code / GitHub Pages setup guide.</p>
    </div>
  </header>

  <div class="card" id="quiz">
    <div class="meta">8 questions — pick one answer per question, then click <strong>Submit</strong></div>

    <!-- Questions will be injected here -->
  </div>

  <div class="controls">
    <button id="submitBtn">Submit</button>
    <button class="secondary" id="showAnswers">Show Answers</button>
    <button class="secondary" id="resetBtn">Reset</button>
    <div id="score" style="margin-left:12px;color:var(--muted)"></div>
  </div>

  <footer>
    <p>Based on the VSCode Setup guide for building a GitHub Pages student site. Use the quiz to verify learning and readiness to run <code>make</code>, commit, and sync your site.</p>
  </footer>
</div>

<script>
  const questions = [
    {
      id:1,
      q: 'Where should you download Visual Studio Code for Windows?',
      choices: [
        'From a random blog link',
        'From a torrent site',
        'From the official Visual Studio Code website',
        'From Discord'
      ],
      a:2,
      explanation: 'Always download VS Code from the official website to avoid tampered installers.'
    },
    {
      id:2,
      q: 'After cloning your GitHub project on Windows, which command opens it in VS Code?',
      choices: ['vscode .','open .','code .','run-vscode'],
      a:2,
      explanation: 'The CLI command is `code .` which opens the current folder in VS Code.'
    },
    {
      id:3,
      q: 'What does the command `./scripts/venv.sh` do before opening VS Code?',
      choices: ['Creates a GitHub repo','Activates a Python virtual environment','Installs Node.js','Starts the localhost server'],
      a:1,
      explanation: 'The script activates (or sets up) the virtual environment (venv) used for running the site locally.'
    },
    {
      id:4,
      q: 'If you want to use VS Code commands (`code .`) directly in Windows Command Prompt or PowerShell, which option must be enabled during installation?',
      choices: ['Add “Open with Code” in Explorer context menu','Add VS Code to PATH','Enable Remote Development','Install Node.js'],
      a:1,
      explanation: 'Adding VS Code to the PATH lets you run `code` from shells.'
    },
    {
      id:5,
      q: 'What does the command `make` do in the project setup?',
      choices: ['Downloads VS Code extensions','Automates tasks defined in a Makefile and runs a local server','Deletes temporary files','Syncs directly to GitHub Pages'],
      a:1,
      explanation: '`make` reads the Makefile and runs tasks such as starting a local server for testing.'
    },
    {
      id:6,
      q: 'Before syncing changes to GitHub, what must you always do?',
      choices: ['Restart VS Code','Run `make` to test changes locally','Delete the venv folder','Disable GitHub Actions'],
      a:1,
      explanation: 'Testing locally prevents pushing broken code that triggers Actions or breaks the live site.'
    },
    {
      id:7,
      q: 'Where can you see your local website running after `make` succeeds?',
      choices: ['In Windows Explorer','At `http://127.0.0.1:####/` in your browser','Inside GitHub directly','In the Windows Task Manager'],
      a:1,
      explanation: 'The local server address (e.g. http://127.0.0.1:4500/) is shown in the terminal output.'
    },
    {
      id:8,
      q: 'What’s the correct order of the SDLC workflow for this project?',
      choices: ['Sync → Commit → Run Server → Test','Run Server → Make Changes → Commit → Test → Sync','Commit → Test → Run Server → Sync','Test → Run Server → Sync → Commit'],
      a:1,
      explanation: 'Run server -> Make changes -> Commit -> Test -> Sync is the recommended flow in the guide.'
    }
  ];

  const quizEl = document.getElementById('quiz');

  function renderQuestions(){
    const frag = document.createDocumentFragment();
    questions.forEach((item, idx)=>{
      const div = document.createElement('div');
      div.className = 'q';
      div.id = 'q'+item.id;
      div.innerHTML = `<h3>${item.id}. ${item.q}</h3>`;
      const choices = document.createElement('div');
      item.choices.forEach((choice, i)=>{
        const id = `q${item.id}_c${i}`;
        const label = document.createElement('label');
        label.className = 'choice';
        label.htmlFor = id;
        label.innerHTML = `<input type="radio" name="q${item.id}" id="${id}" value="${i}" /> ${choice}`;
        choices.appendChild(label);
      });
      div.appendChild(choices);
      frag.appendChild(div);
    });
    quizEl.appendChild(frag);
  }

  function grade(){
    let correct = 0;
    questions.forEach(q=>{
      const sel = document.querySelector(`input[name=q${q.id}]:checked`);
      const container = document.getElementById('q'+q.id);
      // remove old result
      const old = container.querySelector('.result'); if(old) old.remove();
      const res = document.createElement('div');
      res.className = 'result';
      if(!sel){
        res.className += ' incorrect';
        res.innerHTML = `<strong>Unanswered.</strong><small class="help">${q.explanation}</small>`;
      } else {
        const val = Number(sel.value);
        if(val === q.a){
          correct++;
          res.className += ' correct';
          res.innerHTML = `<strong>Correct.</strong><small class="help">${q.explanation}</small>`;
        } else {
          res.className += ' incorrect';
          res.innerHTML = `<strong>Incorrect.</strong> Correct answer: <em>${q.choices[q.a]}</em><small class="help">${q.explanation}</small>`;
        }
      }
      container.appendChild(res);
    });
    const scoreEl = document.getElementById('score');
    scoreEl.textContent = `Score: ${correct} / ${questions.length}`;
    return correct;
  }

  function showAnswers(){
    questions.forEach(q=>{
      const inputs = document.querySelectorAll(`input[name=q${q.id}]`);
      inputs.forEach((inp, idx)=>{
        inp.checked = (idx === q.a);
      });
      const container = document.getElementById('q'+q.id);
      const old = container.querySelector('.result'); if(old) old.remove();
      const res = document.createElement('div');
      res.className = 'result correct';
      res.innerHTML = `<strong>Answer:</strong> <em>${q.choices[q.a]}</em><small class="help">${q.explanation}</small>`;
      container.appendChild(res);
    });
    document.getElementById('score').textContent = `Score: ${questions.length} / ${questions.length}`;
  }

  function resetQuiz(){
    document.querySelectorAll('input[type=radio]').forEach(i=>i.checked=false);
    document.querySelectorAll('.result').forEach(r=>r.remove());
    document.getElementById('score').textContent = '';
  }

  document.getElementById('submitBtn').addEventListener('click', ()=>{
    grade();
    window.scrollTo({top:0,behavior:'smooth'});
  });
  document.getElementById('showAnswers').addEventListener('click', ()=>showAnswers());
  document.getElementById('resetBtn').addEventListener('click', ()=>resetQuiz());

  renderQuestions();
</script>
