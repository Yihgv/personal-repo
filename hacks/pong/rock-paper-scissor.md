---
title: Rock paper Scissors
layout: opencs
description: Learn how to experiment with the console, elements, and see OOP in action while playing Rock paper Scissors!
permalink: /rock-paper-scissor/
---

<!-- UI lives in HTML so it shows even if JS has an issue -->
<div id="mainGameBox" style="max-width:700px;margin:32px auto 48px;position:relative;z-index:2;">
  <div id="rps-container" style="
    background: linear-gradient(135deg, black, purple);
    color: white; padding: 24px; border-radius: 15px; border: 3px solid purple;
    box-shadow: 0 0 20px rgba(128,0,128,.5); text-align:center; max-width: 600px; width: 90%; margin:0 auto;
    font-family: ui-monospace, SFMono-Regular, Menlo, Consolas, 'Liberation Mono', monospace;">
    <h2 style="color:#b66bff;margin:0 0 12px;">Rock Paper Scissors SHOOT!</h2>
    <p style="margin:6px 0;">Play from the browser console.</p>
    <p style="margin:6px 0;">Type <code>playRPS("rock")</code>, <code>playRPS("paper")</code>, or <code>playRPS("scissors")</code></p>

    <div id="images" style="display:flex;justify-content:center;gap:20px;margin:12px 0 8px;">
      <button id="rock-btn" style="background:none;border:none;padding:0;cursor:pointer;">
        <img id="rock-img" src="{{ '/images/rock.jpg' | relative_url }}"
             alt="rock" style="width:100px;border:2px solid #fff;border-radius:10px;">
      </button>
      <button id="paper-btn" style="background:none;border:none;padding:0;cursor:pointer;">
        <img id="paper-img" src="{{ '/images/paper.jpeg' | relative_url }}"
             alt="paper" style="width:100px;border:2px solid #fff;border-radius:10px;">
      </button>
      <button id="scissors-btn" style="background:none;border:none;padding:0;cursor:pointer;">
        <img id="scissors-img" src="{{ '/images/scissors.jpeg' | relative_url }}"
             alt="scissors" style="width:100px;border:2px solid #fff;border-radius:10px;">
      </button>
    </div>

    <div style="margin-bottom:8px;font-size:.95em;color:#ffd700;">Click any icon to see a console challenge!</div>

    <!-- Canvas mount -->
    <div id="battleMount" style="display:block; margin:12px auto;"></div>

    <div id="resultBox" style="margin-top:10px;font-size:16px;color:#00ffff;"></div>

    <noscript>
      <div style="background:#400;color:#fff;padding:8px;border-radius:8px;margin-top:10px;">
        Enable JavaScript to play.
      </div>
    </noscript>
  </div>
</div>

<script>
/* ------------------------------
   Error overlay (shows on page)
---------------------------------*/
const __err = (e) => {
  const box = document.createElement('pre');
  box.style.cssText = 'white-space:pre-wrap;background:#220;padding:12px;border-radius:8px;border:1px solid #400;color:#f88;font:12px/1.4 ui-monospace,monospace;margin:12px auto;max-width:600px;';
  box.textContent = 'JS error:\n' + (e?.stack || e);
  document.getElementById('mainGameBox')?.prepend(box);
  console.error(e);
};

try {
  // Small probe so you know JS executed
  document.getElementById('resultBox')
    ?.insertAdjacentHTML('beforeend','<span style="color:#0ff">JS is running ✅</span>');

  // --- helper: highlight chosen image ---
  function highlightImage(id){
    ['rock-img','paper-img','scissors-img'].forEach(i=>{
      const el=document.getElementById(i);
      if(el) el.style.boxShadow='';
    });
    const picked=document.getElementById(id);
    if(picked) picked.style.boxShadow='0 0 30px 10px gold';
  }

  // --- Background + Sprite classes ---
  class BattleBackground{
    constructor(image,w,h,ratio=0.1){ this.image=image; this.width=w; this.height=h; this.x=0; this.y=0; this.speed=2*ratio; }
    update(){ this.x=(this.x - this.speed) % this.width; }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
      ctx.drawImage(this.image, this.x+this.width, this.y, this.width, this.height);
    }
  }
  class BattleSprite{
    constructor(image,w,h,x,y){
      this.image=image; this.width=w; this.height=h;
      this.homeX=x; this.homeY=y; this.x=x; this.y=y;
      this.targetX=x; this.targetY=y; this.opacity=1; this.scale=1; this.rotation=0; this.animating=false;
    }
    update(){
      const k = this.animating ? 0.12 : 0.08;
      this.x += ( (this.animating?this.targetX:this.homeX) - this.x )*k;
      this.y += ( (this.animating?this.targetY:this.homeY) - this.y )*k;
    }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.save();
      ctx.globalAlpha=this.opacity;
      ctx.translate(this.x+this.width/2,this.y+this.height/2);
      ctx.rotate(this.rotation);
      ctx.scale(this.scale,this.scale);
      ctx.drawImage(this.image,-this.width/2,-this.height/2,this.width,this.height);
      ctx.restore();
    }
    resetVisuals(){ this.opacity=1; this.scale=1; this.rotation=0; }
  }

  // --- Canvas (mounts to #battleMount) ---
  const battleCanvas=document.createElement('canvas');
  battleCanvas.width=360; battleCanvas.height=180;
  battleCanvas.style.display='block';
  battleCanvas.style.margin='0 auto';
  battleCanvas.style.background='#111';
  battleCanvas.style.borderRadius='12px';
  battleCanvas.style.boxShadow='0 2px 12px rgba(0,0,0,.18)';
  const mount=document.getElementById('battleMount');
  if(!mount) throw new Error('#battleMount missing');
  mount.appendChild(battleCanvas);
  const ctx=battleCanvas.getContext('2d');

  // --- Assets ---
  const bgImage=new Image();
  // If you have a real bg file, point here; otherwise use gradient:
  // bgImage.src = "{{ '/images/platformer/backgrounds/alien_planet1.jpg' | relative_url }}";
  bgImage.src = "data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='360' height='180'><linearGradient id='g' x1='0' y1='0' x2='1' y2='0'><stop offset='0%' stop-color='%230b1020'/><stop offset='100%' stop-color='%231a2a55'/></linearGradient><rect width='100%' height='100%' fill='url(%23g)'/></svg>";

  const rockImg=new Image();     rockImg.src="{{ '/images/rock.jpg' | relative_url }}";
  const paperImg=new Image();   paperImg.src="{{ '/images/paper.jpeg' | relative_url }}";
  const scissorsImg=new Image();scissorsImg.src="{{ '/images/scissors.jpeg' | relative_url }}";

  const bg = new BattleBackground(bgImage,battleCanvas.width,battleCanvas.height,0.12);
  const sprites={
    rock:     new BattleSprite(rockImg,     96,96,  10,42),
    paper:    new BattleSprite(paperImg,    96,96, 132,42),
    scissors: new BattleSprite(scissorsImg, 96,96, 254,42),
  };

  // --- Battle state + helpers ---
  const battle={active:false,winner:null,loser:null,frames:0,max:120,tie:null};

  function startBattle(winner,loser){
    battle.active=true; battle.tie=null; battle.winner=winner; battle.loser=loser; battle.frames=0;
    sprites[winner].animating=true;
    sprites[winner].targetX=sprites[loser].homeX; sprites[winner].targetY=sprites[loser].homeY;
    sprites[loser].animating=false;
  }
  function startTie(choice){
    battle.active=true; battle.tie=choice; battle.winner=null; battle.loser=null; battle.frames=0;
    Object.values(sprites).forEach(s=>s.animating=false);
  }

  function render(){
    ctx.clearRect(0,0,battleCanvas.width,battleCanvas.height);
    bg.update(); bg.draw(ctx);

    ctx.save();
    ctx.font="bold 14px ui-monospace,monospace";
    ctx.fillStyle="cyan"; ctx.textAlign="center";
    ctx.fillText("Animated Battle: OOP", battleCanvas.width/2, 24);
    ctx.restore();

    if(battle.active){
      const t=battle.frames/battle.max;
      if(battle.tie){
        const wobble=Math.sin(battle.frames*0.3)*4;
        sprites[battle.tie].rotation=wobble*Math.PI/180;
      }else{
        const w=sprites[battle.winner], l=sprites[battle.loser];
        const pulse=(battle.frames<battle.max/2)? 1+(battle.frames/(battle.max/2))*0.2
                                                : 1.2-((battle.frames-battle.max/2)/(battle.max/2))*0.2;
        w.scale=pulse;
        l.opacity=Math.max(0.15,1-t*0.85);
        l.scale=Math.max(0.6,1-t*0.4);
        if(battle.winner==='rock' && battle.loser==='scissors'){ l.rotation= -t*(Math.PI/4); }
        if(battle.winner==='paper' && battle.loser==='rock'){ w.targetX=l.homeX-6; w.targetY=l.homeY-6; }
        if(battle.winner==='scissors' && battle.loser==='paper'){ w.rotation= t*(Math.PI/10); l.rotation= -t*(Math.PI/10); }
      }
      battle.frames++;
      if(battle.frames>=battle.max){
        battle.active=false; Object.values(sprites).forEach(s=>{ s.resetVisuals(); s.animating=false; });
      }
    }
    Object.values(sprites).forEach(s=>{ s.update(); s.draw(ctx); });
    requestAnimationFrame(render);
  }
  render();


// --- Double-click icons to play (single-click still shows tips) ---
document.getElementById('rock-btn')?.addEventListener('dblclick',()=>playRPS('rock'));
document.getElementById('paper-btn')?.addEventListener('dblclick',()=>playRPS('paper'));
document.getElementById('scissors-btn')?.addEventListener('dblclick',()=>playRPS('scissors'));

// --- Konami cheat code: next round = guaranteed win + glow ---
const KONAMI = ['ArrowUp','ArrowUp','ArrowDown','ArrowDown','ArrowLeft','ArrowRight','ArrowLeft','ArrowRight','b','a'];
let keybuf = [];
let cheatNextWin = false;
window.addEventListener('keydown',(e)=>{
  keybuf.push(e.key);
  if(keybuf.length>KONAMI.length) keybuf.shift();
  if(KONAMI.every((k,i)=> (keybuf[i]||'').toLowerCase()===k.toLowerCase())){
    cheatNextWin = true;
    toast('✨ Konami unlocked: next round auto-win!');
    const old = panel.style.boxShadow;
    panel.style.boxShadow='0 0 28px 6px rgba(255,0,200,.75), inset 0 0 18px rgba(0,255,255,.45)';
    setTimeout(()=> panel.style.boxShadow = old || '0 0 20px rgba(128,0,128,.5)', 1400);
  }
});

// ===== Replace your existing window.playRPS with this enhanced version =====
window.playRPS = function(playerChoice){
  const choices = ["rock","paper","scissors"];
  if(!choices.includes(playerChoice)){
    console.log("Invalid choice. Use 'rock', 'paper', or 'scissors'.");
    return { ok:false, error:"invalid_choice", choices };
  }
  highlightImage(playerChoice+"-img");

  const counters = { rock:"scissors", paper:"rock", scissors:"paper" };
  let computerChoice = choices[Math.floor(Math.random()*3)];
  if(cheatNextWin){ computerChoice = counters[playerChoice]; cheatNextWin = false; }

  let resultText, winner=null, loser=null;

  if(playerChoice === computerChoice){
    resultText = "Tie!"; startTie(playerChoice);
    state.ties++; state.streak = 0;
  } else if(
    (playerChoice==="rock" && computerChoice==="scissors") ||
    (playerChoice==="paper" && computerChoice==="rock") ||
    (playerChoice==="scissors" && computerChoice==="paper")
  ){
    resultText = "You Win!"; winner = playerChoice; loser = computerChoice;
    state.wins++; state.streak++; state.best = Math.max(state.best, state.streak);
  } else {
    resultText = "You Lose!"; winner = computerChoice; loser = playerChoice;
    state.losses++; state.streak = 0;
  }

  saveState(); renderScore();

  document.getElementById("resultBox").innerHTML = `
    <p>You chose: <b>${playerChoice.toUpperCase()}</b></p>
    <p>Computer chose: <b>${computerChoice.toUpperCase()}</b></p>
    <h3 style="color: cyan;">${resultText}</h3>
    ${resultText==="You Lose!" ? `<div style="color:#ffa3a3;margin-top:6px">${TAUNTS[Math.floor(Math.random()*TAUNTS.length)]}</div>` : "" }
  `;

  if(winner && loser) startBattle(winner, loser);

  // flair
  sfx(resultText);
  if(resultText==="You Win!") {
    confettiBurst();
    if([3,5,10].includes(state.streak)) toast(`🔥 Streak ${state.streak}!`);
  }

  const result = { ok:true, player:playerChoice, computer:computerChoice, result:resultText, streak:state.streak, best:state.best };
  window.lastRPS = result;
  return result;
};


  // --- Game API (returns an object so console doesn't show "undefined") ---
  window.playRPS=function(playerChoice){
    const choices=['rock','paper','scissors'];
    if(!choices.includes(playerChoice)){
      console.log("Invalid choice. Use 'rock', 'paper', or 'scissors'.");
      return { ok:false, error:"invalid_choice", choices };
    }
    highlightImage(playerChoice+'-img');
    const computerChoice=choices[Math.floor(Math.random()*3)];
    let resultText, winner=null, loser=null;

    if(playerChoice===computerChoice){ resultText='Tie!'; startTie(playerChoice); }
    else if(
      (playerChoice==='rock'&&computerChoice==='scissors')||
      (playerChoice==='paper'&&computerChoice==='rock')||
      (playerChoice==='scissors'&&computerChoice==='paper')
    ){ resultText='You Win!'; winner=playerChoice; loser=computerChoice; }
    else { resultText='You Lose!'; winner=computerChoice; loser=playerChoice; }

    document.getElementById('resultBox').innerHTML =
      `<p>You chose: <b>${playerChoice.toUpperCase()}</b></p>
       <p>Computer chose: <b>${computerChoice.toUpperCase()}</b></p>
       <h3 style="color: cyan;">${resultText}</h3>`;

    if(winner&&loser) startBattle(winner,loser);

    const result = { ok:true, player:playerChoice, computer:computerChoice, result:resultText };
    window.lastRPS = result; // optional quick access
    return result;
  };

  // --- Console-learning prompts on click (single-click) ---
  document.getElementById('rock-btn')?.addEventListener('click', ()=> {
    alert("🪨 Try in the console:\n\nrockImg && rockImg.complete ? 'already loaded' : 'loads on demand';\nrock.setBorder('4px solid lime');");
  });
  document.getElementById('paper-btn')?.addEventListener('click', ()=> {
    alert("📄 Try in the console:\n\npaper.rotate(15);");
  });
  document.getElementById('scissors-btn')?.addEventListener('click', ()=> {
    alert("✂️ Try in the console:\n\nscissors.setWidth(150);");
  });

} catch(e){ __err(e); }
</script>
