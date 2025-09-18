---
title: Rock paper Scissors
layout: opencs
description: Learn how to experiment with the console, elements, and see OOP in action while playing Rock paper Scissors!
permalink: /rock-paper-scissor/
---

<!-- UI in HTML so it shows even if JS has an issue -->
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

    <div style="margin-bottom:8px;font-size:.95em;color:#ffd700;">Shift+Click an icon to see a console challenge. (Click = play)</div>

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
  mount.style.position = 'relative';
  mount.appendChild(battleCanvas);
  const ctx=battleCanvas.getContext('2d');

  // --- Assets ---
  const bgImage=new Image();
  // If you have a real bg file, point here; otherwise gradient fallback:
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

  /* ===== Mini SFX engine ===== */
  const AC = window.AudioContext || window.webkitAudioContext;
  const audioCtx = new AC();
  let sfxEnabled = true;
  let sfxVolume  = 0.12;
  function beep(freq=440, time=0.08, type="square"){
    if(!sfxEnabled) return;
    if(audioCtx.state === "suspended") audioCtx.resume().catch(()=>{});
    const o = audioCtx.createOscillator();
    const g = audioCtx.createGain();
    o.type = type; o.frequency.value = freq;
    o.connect(g); g.connect(audioCtx.destination);
    const now = audioCtx.currentTime;
    g.gain.setValueAtTime(sfxVolume, now);
    g.gain.exponentialRampToValueAtTime(0.0008, now + time);
    o.start(now); o.stop(now + time);
  }
  function sfx(result){
    if(result === "You Win!")      { beep(880,0.09,"sawtooth"); setTimeout(()=>beep(1320,0.07,"sawtooth"), 70); }
    else if(result === "You Lose!"){ beep(220,0.12,"triangle"); }
    else                           { beep(440,0.06,"sine"); }
  }
  window.setSFX = (on=true)=>{ sfxEnabled = !!on; return sfxEnabled; };
  window.setSFXVolume = (v)=>{ sfxVolume = Math.max(0, Math.min(1, Number(v)||0)); return sfxVolume; };

  /* ===== Scoreboard (localStorage) ===== */
  const panel = document.getElementById('rps-container');
  const scoreBox = document.createElement('div');
  scoreBox.style.cssText = 'margin-top:10px;font-size:14px;color:#0ff';
  panel.appendChild(scoreBox);

  const state = {
    wins: +(localStorage.getItem('rps_w')||0),
    losses: +(localStorage.getItem('rps_l')||0),
    ties: +(localStorage.getItem('rps_t')||0),
    streak: +(localStorage.getItem('rps_s')||0),
    best: +(localStorage.getItem('rps_b')||0),
  };
  function saveState(){
    localStorage.setItem('rps_w',state.wins);
    localStorage.setItem('rps_l',state.losses);
    localStorage.setItem('rps_t',state.ties);
    localStorage.setItem('rps_s',state.streak);
    localStorage.setItem('rps_b',state.best);
  }
  function renderScore(){
    scoreBox.innerHTML = `W:${state.wins}  L:${state.losses}  T:${state.ties}  | Streak:${state.streak}  Best:${state.best}`;
  }
  renderScore();

  /* ===== Toast helper ===== */
  function toast(msg, ms=1400){
    const t=document.createElement('div');
    t.textContent=msg;
    t.style.cssText='position:fixed;left:50%;top:18px;transform:translateX(-50%);background:#111;border:1px solid #444;color:#0ff;padding:8px 12px;border-radius:10px;font:13px ui-monospace,monospace;z-index:9999;box-shadow:0 6px 20px rgba(0,0,0,.4)';
    document.body.appendChild(t);
    setTimeout(()=>t.remove(), ms);
  }

  /* ===== Confetti overlay ===== */
  const confettiCanvas = document.createElement('canvas');
  confettiCanvas.width = battleCanvas.width;
  confettiCanvas.height = battleCanvas.height;
  confettiCanvas.style.cssText = 'position:absolute;left:0;top:0;pointer-events:none';
  mount.appendChild(confettiCanvas);
  const cctx = confettiCanvas.getContext('2d');
  function confettiBurst(x=battleCanvas.width/2, y=battleCanvas.height/2, n=70){
    const parts = Array.from({length:n}, ()=>({
      x,y, vx:(Math.random()*2-1)*3.6, vy:(Math.random()*-3-1.5),
      s: Math.random()*3+2, a: 1, hue: Math.floor(Math.random()*360)
    }));
    const start = performance.now();
    function step(t){
      cctx.clearRect(0,0,confettiCanvas.width,confettiCanvas.height);
      parts.forEach(p=>{
        p.vy += 0.08; p.x += p.vx; p.y += p.vy; p.a -= 0.012;
        cctx.fillStyle = `hsla(${p.hue} 90% 60% / ${Math.max(0,p.a)})`;
        cctx.fillRect(p.x, p.y, p.s, p.s);
      });
      if(parts.some(p=>p.a>0 && p.y<confettiCanvas.height+10)) requestAnimationFrame(step);
      else cctx.clearRect(0,0,confettiCanvas.width,confettiCanvas.height);
    }
    requestAnimationFrame(step);
  }

  /* ===== CPU taunts (on loss) ===== */
  const TAUNTS = [
    "Skill issue? 😈",
    "I read your mind… and it said 'paper'.",
    "Consider… rock therapy.",
    "GGs—try again?",
    "I was born to snip paper."
  ];

  /* ===== Secret phrase cheat (console) ===== */
  window.__rpsCheatRounds = window.__rpsCheatRounds || 0;
  window.rpsSecret = "rockets to the moon";   // change to whatever you want
  window.unlockRPS = function(phrase, rounds=1){
    const ok = (phrase || "").toLowerCase().trim() === String(window.rpsSecret).toLowerCase();
    if (!ok) return false;
    window.__rpsCheatRounds = Math.max(1, Math.floor(rounds));
    try { toast("✨ Secret unlocked: next round auto-win!"); } catch {}
    const old = panel.style.boxShadow;
    panel.style.boxShadow='0 0 28px 6px rgba(255,0,200,.75), inset 0 0 18px rgba(0,255,255,.45)';
    setTimeout(()=> panel.style.boxShadow = old || '0 0 20px rgba(128,0,128,.5)', 1200);
    return true;
  };

  /* ===== SHIFT-CLICK: play vs tip ===== */
  function bindShiftTips() {
    const cfg = { "rock-btn":"rock", "paper-btn":"paper", "scissors-btn":"scissors" };
    Object.entries(cfg).forEach(([id, move]) => {
      document.getElementById(id)?.addEventListener("click", (e) => {
        if (e.shiftKey) {
          const tip =
            move === "rock" ? "🪨 Try in the console:\nrock.setBorder('4px solid lime');" :
            move === "paper" ? "📄 Try in the console:\npaper.rotate(15);" :
                               "✂️ Try in the console:\nscissors.setWidth(150);";
          alert(tip);
        } else {
          playRPS(move);  // single-click plays
        }
      });
    });
  }
  bindShiftTips();

  /* ===== Console-manipulable objects ===== */
  class GameObject {
    constructor(id) {
      this.el = document.getElementById(id);
      if (!this.el) throw new Error(`Element #${id} not found`);
    }
    rotate(deg) { this.el.style.transform = `rotate(${deg}deg)`; return this; }
    setBorder(style) { this.el.style.border = style; return this; }
    setWidth(px) { this.el.style.width = `${px}px`; return this; }
    setColor(color) { this.el.style.backgroundColor = color; return this; }
    reset() {
      this.el.style.transform = "";
      this.el.style.border = "";
      this.el.style.width = "";
      this.el.style.backgroundColor = "";
      return this;
    }
  }
  class Rock extends GameObject { constructor() { super("rock-img"); } }
  class Paper extends GameObject { constructor() { super("paper-img"); } }
  class Scissors extends GameObject { constructor() { super("scissors-img"); } }
  window.rock = new Rock(); window.paper = new Paper(); window.scissors = new Scissors();

  // --- Game API (returns object so console doesn't show "undefined") ---
  window.playRPS=function(playerChoice){
    const choices=['rock','paper','scissors'];
    if(!choices.includes(playerChoice)){
      console.log("Invalid choice. Use 'rock', 'paper', or 'scissors'.");
      return { ok:false, error:"invalid_choice", choices };
    }
    highlightImage(playerChoice+'-img');

    const counters = { rock:"scissors", paper:"rock", scissors:"paper" };
    let computerChoice = choices[Math.floor(Math.random()*3)];
    if (window.__rpsCheatRounds > 0) {
      computerChoice = counters[playerChoice];  // force a win
      window.__rpsCheatRounds--;
    }

    let resultText, winner=null, loser=null;
    if(playerChoice===computerChoice){ resultText='Tie!'; startTie(playerChoice); state.ties++; state.streak=0; }
    else if(
      (playerChoice==='rock'&&computerChoice==='scissors')||
      (playerChoice==='paper'&&computerChoice==='rock')||
      (playerChoice==='scissors'&&computerChoice==='paper')
    ){ resultText='You Win!'; winner=playerChoice; loser=computerChoice; state.wins++; state.streak++; state.best=Math.max(state.best,state.streak); }
    else { resultText='You Lose!'; winner=computerChoice; loser=playerChoice; state.losses++; state.streak=0; }

    saveState(); renderScore();

    document.getElementById('resultBox').innerHTML =
      `<p>You chose: <b>${playerChoice.toUpperCase()}</b></p>
       <p>Computer chose: <b>${computerChoice.toUpperCase()}</b></p>
       <h3 style="color: cyan;">${resultText}</h3>
       ${resultText==="You Lose!" ? `<div style="color:#ffa3a3;margin-top:6px">${TAUNTS[Math.floor(Math.random()*TAUNTS.length)]}</div>` : "" }`;

    if(winner&&loser) startBattle(winner,loser);

    sfx(resultText);
    if(resultText==="You Win!") {
      confettiBurst();
      if([3,5,10].includes(state.streak)) toast(`🔥 Streak ${state.streak}!`);
    }

    const result = { ok:true, player:playerChoice, computer:computerChoice, result:resultText, streak:state.streak, best:state.best };
    window.lastRPS = result;
    return result;
  };

} catch(e){ __err(e); }
</script>
