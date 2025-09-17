---
layout: base
title: Mac 💻
permalink: /tools/mac
---

<!-- MacOS Setup Quiz Section -->
<section class="quiz" style="margin-top: 50px; padding: 20px; background-color: #1a1a1a; border-radius: 15px;">
  <h2 class="quiz-title">MacOS Setup Quiz</h2>
  <form id="macQuizForm" class="quiz-form">

    <!-- Question 1 -->
    <p class="quiz-question">1. Which command is used to list files and directories in MacOS Terminal?</p>
    <label><input type="radio" name="q1" value="ls"> ls</label><br>
    <label><input type="radio" name="q1" value="pwd"> pwd</label><br>
    <label><input type="radio" name="q1" value="cd"> cd</label><br><br>

    <!-- Question 2 -->
    <p class="quiz-question">2. Which command is used to clone a project repository from GitHub?</p>
    <label><input type="radio" name="q2" value="git clone"> git clone</label><br>
    <label><input type="radio" name="q2" value="git pull"> git pull</label><br>
    <label><input type="radio" name="q2" value="git push"> git push</label><br><br>

    <!-- Question 3 -->
    <p class="quiz-question">3. What is the correct command to update Homebrew packages?</p>
    <label><input type="radio" name="q3" value="brew update"> brew update</label><br>
    <label><input type="radio" name="q3" value="brew upgrade"> brew upgrade</label><br>
    <label><input type="radio" name="q3" value="brew list"> brew list</label><br><br>

    <!-- Question 4 -->
    <p class="quiz-question">4. After opening a new Terminal session for the first time, what is the first directory you should create for OpenCS?</p>
    <label><input type="radio" name="q4" value="opencs"> mkdir opencs</label><br>
    <label><input type="radio" name="q4" value="student"> mkdir student</label><br>
    <label><input type="radio" name="q4" value="project"> mkdir project</label><br><br>

    <!-- Question 5 -->
    <p class="quiz-question">5. Which command activates the Python virtual environment in the OpenCS project?</p>
    <label><input type="radio" name="q5" value="source venv/bin/activate"> source venv/bin/activate</label><br>
    <label><input type="radio" name="q5" value="./scripts/activate.sh"> ./scripts/activate.sh</label><br>
    <label><input type="radio" name="q5" value="git commit"> git commit</label><br><br>

    <button type="button" class="quiz-button" onclick="checkMacQuiz()">Submit</button>
  </form>

  <div id="macQuizResult" class="quiz-result"></div>
</section>

<script>
  function checkMacQuiz() {
    let score = 0;
    const answers = {
      q1: "ls",
      q2: "git clone",
      q3: "brew upgrade",
      q4: "opencs",
      q5: "source venv/bin/activate"
    };

    const form = document.getElementById("macQuizForm");
    if (form.q1.value === answers.q1) score++;
    if (form.q2.value === answers.q2) score++;
    if (form.q3.value === answers.q3) score++;
    if (form.q4.value === answers.q4) score++;
    if (form.q5.value === answers.q5) score++;

    document.getElementById("macQuizResult").textContent = 
      "You scored " + score + " out of 5. 💻";
  }
</script>
