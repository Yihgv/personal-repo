---
title: Rock paper Scissors
comments: true
layout: opencs
description: Learn how to experiment with the console, elements, and see OOP in action while playing Rock paper Scissors!
permalink: /rock-paper-scissor/
---

<div id="mainGameBox" style="max-width:700px;margin:64px auto 48px auto;position:relative;z-index:2;">
  <div id="gameContainer">
    <canvas id='gameCanvas' style="display:none"></canvas>
  </div>
</div>

<script type="module">
  // --- UI (purple box) ---
  const instructionsStyle = `
    position: relative;
    margin: 64px auto 48px auto;
    background: linear-gradient(135deg, black, purple);
    color: white;
    padding: 30px;
    border-radius: 15px;
    z-index: 1000;
    max-width: 600px;
    width: 90%;
    max-height: 80vh;
    overflow-y: auto;
    font-family: 'Press Start 2P', cursive;
    border: 3px solid purple;
    box-shadow: 0 0 20px rgba(0, 21, 128, 0.5);
    text-align: center;
  `;

  const instructionsHTML = `
    <h2 style="color: purple; margin-bottom: 20px;">Rock Paper Scissors SHOOT!</h2>
    <div id="images" style="display:flex; justify-content:center; gap:20px; margin-bottom:14px;">
      <button id="rock-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="rock-img" src="{{site.baseurl}}/images/rps/rock.jpg"
             style="width:100px; border:2px solid white; border-radius:10px;">
      </button>
      <button id="paper-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="paper-img" src="{{site.baseurl}}/images/rps/paper.jpeg"
             style="width:100px; border:2px solid white; border-radius:10px;">
      </button>
      <button id="scissors-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="scissors-img" src="{{site.baseurl}}/images/rps/scissors.jpeg"
             style="width:100px; border:2px solid white; border-radius:10px;">
      </button>
    </div>

    <!-- mount battle canvas INSIDE the purple box so you can see it -->
    <div id="battleMount" style="display:block; margin:12px auto;"></div>

    <div id="resultBox" style="margin-top: 16px; font-size: 16px; color: yellow;"></div>
  `;

  const container = document.createElement("div");
  container.setAttribute("style", instructionsStyle);
  container.innerHTML = instructionsHTML;
  document.getElementById("mainGameBox").appendChild(container);

  // --- Create buttons on-page ---
  const rpsButtons = document.createElement("div");
  rpsButtons.id = "rps-buttons";
  rpsButtons.style = "text-align: center; margin-top: 20px;";

  rpsButtons.innerHTML = `
    <button onclick="playRPS('rock')" 
            style="padding:15px 25px; font-weight:bold; margin:5px; 
                   color:white; background-color:#8B4513; font-size:18px; border-radius:8px; border:none;">
      🪨 Rock
    </button>
    <button onclick="playRPS('paper')" 
            style="padding:15px 25px; font-weight:bold; margin:5px; 
                   color:black; background-color:#F0E68C; font-size:18px; border-radius:8px; border:none;">
      📄 Paper
    </button>
    <button onclick="playRPS('scissors')" 
            style="padding:15px 25px; font-weight:bold; margin:5px; 
                   color:white; background-color:#1E90FF; font-size:18px; border-radius:8px; border:none;">
      ✂️ Scissors
    </button>
  `;
  document.getElementById("mainGameBox").appendChild(rpsButtons);

  // --- helper: highlight chosen image ---
  function highlightImage(id){
    ["rock-img","paper-img","scissors-img"].forEach(i=>{
      const el = document.getElementById(i);
      if(el) el.style.boxShadow = "";
    });
    const picked = document.getElementById(id);
    if(picked) picked.style.boxShadow = "0 0 30px 10px gold";
  }

  // --- OOP classes ---
  class BattleBackground {
    constructor(image, width, height, speedRatio=0.1){
      this.image = image;
      this.width = width;
      this.height = height;
      this.x = 0; this.y = 0;
      this.speed = 2 * speedRatio;
    }
    update(){ this.x = (this.x - this.speed) % this.width; }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
      ctx.drawImage(this.image, this.x + this.width, this.y, this.width, this.height);
    }
  }

  class BattleSprite {
    constructor(image, width, height, x, y){
      this.image = image;
      this.width = width; this.height = height;
      this.homeX = x; this.homeY = y;
      this.x = x; this.y = y;
      this.targetX = x; this.targetY = y;
      this.opacity = 1; this.scale = 1; this.rotation = 0;
      this.animating = false;
    }
    update(){
      if(this.animating){
        this.x += (this.targetX - this.x)*0.12;
        this.y += (this.targetY - this.y)*0.12;
      } else {
        this.x += (this.homeX - this.x)*0.08;
        this.y += (this.homeY - this.y)*0.08;
      }
    }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.save();
      ctx.globalAlpha = this.opacity;
      ctx.translate(this.x + this.width/2, this.y + this.height/2);
      ctx.rotate(this.rotation);
      ctx.scale(this.scale, this.scale);
      ctx.drawImage(this.image, -this.width/2, -this.height/2, this.width, this.height);
      ctx.restore();
    }
    resetVisuals(){
      this.opacity = 1; this.scale = 1; this.rotation = 0;
    }
    resetPosition(){
      this.x = this.homeX; this.y = this.homeY;
      this.targetX = this.homeX; this.targetY = this.homeY;
      this.animating = false;
    }
  }

  // --- Canvas mounted inside purple box ---
  const battleCanvas = document.createElement('canvas');
  battleCanvas.width = 360;
  battleCanvas.height = 180;
  battleCanvas.style.display = 'block';
  battleCanvas.style.margin = '0 auto';
  battleCanvas.style.background = '#111';
  battleCanvas.style.borderRadius = '12px';
  battleCanvas.style.boxShadow = '0 2px 12px rgba(0,0,0,0.18)';
  document.getElementById('battleMount').appendChild(battleCanvas);
  const ctx = battleCanvas.getContext('2d');

  // --- assets ---
  const bgImage = new Image();
  bgImage.src = '{{site.baseurl}}/images/platformer/backgrounds/alien_planet1.jpg';

  const rockImg = new Image();
  rockImg.src = '{{site.baseurl}}/images/rps/rock.jpg';
  const paperImg = new Image();
  paperImg.src = '{{site.baseurl}}/images/rps/paper.jpeg';
  const scissorsImg = new Image();
  scissorsImg.src = '{{site.baseurl}}/images/rps/scissors.jpeg';

  const bg = new BattleBackground(bgImage, battleCanvas.width, battleCanvas.height, 0.12);

  const sprites = {
    rock: new BattleSprite(rockImg, 96, 96, 10, 42),
    paper: new BattleSprite(paperImg, 96, 96, 132, 42),
    scissors: new BattleSprite(scissorsImg, 96, 96, 254, 42)
  };

  // --- global battle state ---
  const battle = {active:false, winner:null, loser:null, frames:0, max:120, tie:null};

  function startBattle(winner, loser){
    battle.active = true;
    battle.tie = null;
    battle.winner = winner;
    battle.loser = loser;
    battle.frames = 0;
    sprites[winner].animating = true;
    sprites[winner].targetX = sprites[loser].homeX;
    sprites[winner].targetY = sprites[loser].homeY;
    sprites[loser].animating = false;
  }

  function startTie(choice){
    battle.active = true;
    battle.tie = choice;
    battle.winner = null;
    battle.loser = null;
    battle.frames = 0;
    Object.values(sprites).forEach(s=>{ s.animating = false; });
  }

  function render(){
    ctx.clearRect(0,0,battleCanvas.width,battleCanvas.height);
    bg.update(); bg.draw(ctx);
    ctx.save();
    ctx.font = "bold 14px 'Press Start 2P', cursive";
    ctx.fillStyle = "cyan";
    ctx.textAlign = "center";
    ctx.fillText("Animated Battle: OOP", battleCanvas.width/2, 24);
    ctx.restore();

    if(battle.active){
      const t = battle.frames / battle.max;
      if(battle.tie){
        const wobble = Math.sin(battle.frames*0.3)*4;
        sprites[battle.tie].rotation = wobble * Math.PI/180;
      } else {
        const w = sprites[battle.winner];
        const l = sprites[battle.loser];
        const pulse = (battle.frames < battle.max/2)
          ? 1 + (battle.frames/(battle.max/2))*0.2
          : 1.2 - ((battle.frames - battle.max/2)/(battle.max/2))*0.2;
        w.scale = pulse;
        l.opacity = Math.max(0.15, 1 - t*0.85);
        l.scale   = Math.max(0.6, 1 - t*0.4);
        if(battle.winner === "rock" && battle.loser === "scissors") l.rotation = -t*(Math.PI/4);
        if(battle.winner === "paper" && battle.loser === "rock") { w.targetX = l.homeX-6; w.targetY=l.homeY-6; }
        if(battle.winner === "scissors" && battle.loser === "paper"){ w.rotation=t*(Math.PI/10); l.rotation=-t*(Math.PI/10);}
      }
      battle.frames++;
      if(battle.frames >= battle.max){ battle.active=false; Object.values(sprites).forEach(s=>{ s.resetVisuals(); s.animating=false; }); }
    }

    Object.values(sprites).forEach(s=>{ s.update(); s.draw(ctx); });
    requestAnimationFrame(render);
  }
  render();

  // --- game logic + console entry point ---
  window.playRPS = function(playerChoice){
    const choices = ["rock","paper","scissors"];
    if(!choices.includes(playerChoice)){ console.log("Invalid choice. Use 'rock', 'paper', or 'scissors'."); return; }
    highlightImage(playerChoice+"-img");
    const computerChoice = choices[Math.floor(Math.random()*choices.length)];
    let resultText, winner=null, loser=null;
    if(playerChoice===computerChoice){ resultText="Tie!"; startTie(playerChoice); }
    else if((playerChoice==="rock" && computerChoice==="scissors")||(playerChoice==="paper" && computerChoice==="rock")||(playerChoice==="scissors" && computerChoice==="paper")){
      resultText="You Win!"; winner=playerChoice; loser=computerChoice;
    } else { resultText="You Lose!"; winner=computerChoice; loser=playerChoice; }
    document.getElementById("resultBox").innerHTML = `
      <p>You chose: <b>${playerChoice.toUpperCase()}</b></p>
      <p>Computer chose: <b>${computerChoice.toUpperCase()}</b></p>
      <h3 style="color: cyan;">${resultText}</h3>
    `;
    if(winner && loser) startBattle(winner, loser);
    console.log(`You chose: ${playerChoice.toUpperCase()}`);
    console.log(`Computer chose: ${computerChoice.toUpperCase()}`);
    console.log(`Result: ${resultText}`);
  };

  // --- OOP console learning ---
  class GameObject {
    constructor(id){ this.el=document.getElementById(id); if(!this.el) throw new Error(`Element #${id} not found`); }
    rotate(deg){ this.el.style.transform=`rotate(${deg}deg)`; return this; }
    setBorder(style){ this.el.style.border=style; return this; }
    setWidth(px){ this.el.style.width=`${px}px`; return this; }
    setColor(color){ this.el.style.backgroundColor=color; return this; }
    reset(){ this.el.style.transform=""; this.el.style.border=""; this.el.style.width=""; this.el.style.backgroundColor=""; return this; }
  }

  class Rock extends GameObject { constructor(){ super("rock-img"); } }
  class Paper extends GameObject { constructor(){ super("paper-img"); } }
  class Scissors extends GameObject { constructor(){ super("scissors-img"); } }

  const rock = new Rock(); const paper = new Paper(); const scissors = new Scissors();
  window.rock = rock; window.paper = paper; window.scissors = scissors;

  // --- alerts for console exploration ---
  document.getElementById("rock-btn").addEventListener("click",()=>{ alert("🪨 Try in the console:\n\nrock.setBorder('4px solid lime');"); });
  document.getElementById("paper-btn").addEventListener("click",()=>{ alert("📄 Try in the console:\n\npaper.rotate(15);"); });
  document.getElementById("scissors-btn").addEventListener("click",()=>{ alert("✂️ Try in the console:\n\nscissors.setWidth(150);"); });
</script>