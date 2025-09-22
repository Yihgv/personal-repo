---
layout: post
categories: [Hacks]
title: Snake Game
description: Play a simple Snake game in your browser
type: project
comments: true
permalink: /snakegame
hide: true
---

<style>
/* Page-local styles for a cleaner game layout */
:root {
  --brand: #0b82ff;
  --ink: #0f172a;
  --muted: #64748b;
}

.game-shell { max-width: 760px; margin: 0 auto; }
.game-title { text-align: left; margin: .5rem 0 0; }

.game-card {
  background: #ffffff;
  border: 1px solid rgba(0,0,0,0.08);
  border-radius: 16px;
  box-shadow: 0 8px 22px rgba(0,0,0,0.06);
  padding: 12px 16px 18px;
}

@media (prefers-color-scheme: dark) {
  .game-card { background: #0f172a; border-color: rgba(255,255,255,0.08); }
}

.hud { display:flex; align-items:center; justify-content:flex-start; gap: 12px; margin-bottom: 12px; }
.hud .score { font-weight: 700; }
.hud .hint { display:none; }

.kbd, kbd { display:inline-block; padding: 2px 6px; border-radius:6px; background:#ffffff; color:#111827; font-weight:700; }
@media (prefers-color-scheme: dark) { .kbd, kbd { background:#1f2937; color:#e5e7eb; } }

.wrap{ margin-left:auto; margin-right:auto; }
canvas{ display:none; border: 10px solid var(--brand); border-radius: 12px; background: #0c1326; }
canvas:focus{ outline:none; }

/* Screen typography */
#gameover p, #setting p, #menu p{ font-size: 18px; margin: .25rem 0 .5rem; }

/* Links styled as buttons */
#gameover a, #setting a, #menu a{
    font-size: 28px; display:block; margin: 8px 0; text-decoration:none;
    color: #1e90ff; font-weight: 600;
}
#gameover a:hover, #setting a:hover, #menu a:hover{ text-decoration: underline; cursor: pointer; }

/* Screen visibility */
#menu{ display:block; }
#gameover{ display:none; }
#setting{ display:none; }

/* Settings radio group */
#setting input{ display:none; }
#setting label{ cursor:pointer; margin-right:8px; padding:4px 10px; border-radius:8px; background:#e5e7eb; color:#111827; }
#setting input:checked + label{ background-color: #d1eaff; color: #0b5394; }
@media (prefers-color-scheme: dark) { #setting label{ background:#1f2937; color:#e5e7eb; } }

/* Inner stage panel for screens */
.stage {
    text-align:center;
    background: rgba(0,0,0,0.12);
    border: 1px solid rgba(0,0,0,0.08);
    border-radius: 14px;
    padding: 24px 16px;
    box-shadow: inset 0 1px 0 rgba(255,255,255,0.03);
}
@media (prefers-color-scheme: dark) { .stage { background: rgba(255,255,255,0.04); border-color: rgba(255,255,255,0.06);} }
</style>


<div class="game-shell">
  <h2 class="game-title">Snake</h2>
    <div class="game-card">
        <div class="hud">
            <div class="score">Score: <span id="score_value">0</span></div>
        </div>
        <div class="stage">
            <!-- Main Menu -->
            <div id="menu" class="py-3">
                <p>Welcome to Snake, press <kbd>space</kbd> to begin</p>
                <a id="new_game" class="link-alert">new game</a>
                <a id="setting_menu" class="link-alert">settings</a>
            </div>
            <!-- Game Over -->
            <div id="gameover" class="py-3">
                <p>Game Over, press <kbd>space</kbd> to try again</p>
                <a id="new_game1" class="link-alert">new game</a>
                <a id="setting_menu1" class="link-alert">settings</a>
            </div>
            <!-- Play Screen -->
            <canvas id="snake" class="wrap" width="500" height="500" tabindex="1"></canvas>
            <!-- Settings Screen -->
            <div id="setting" class="py-3">
                <p>Settings Screen, press <kbd>space</kbd> to go back to playing</p>
                <a id="new_game2" class="link-alert">new game</a>
                <br>
                <p>Speed:
                    <input id="speed1" type="radio" name="speed" value="110" checked/>
                    <label for="speed1">Slow</label>
                    <input id="speed2" type="radio" name="speed" value="80"/>
                    <label for="speed2">Medium</label>
                    <input id="speed3" type="radio" name="speed" value="40"/>
                    <label for="speed3">Fast</label>
                    <input id="speed4" type="radio" name="speed" value="30"/>
                    <label for="speed4">Very Fast</label>
                    <input id="speed5" type="radio" name="speed" value="19"/>
                    <label for="speed5">Impossible</label>
                </p>
                <p>Wall:
                    <input id="wallon" type="radio" name="wall" value="1" checked/>
                    <label for="wallon">On</label>
                    <input id="walloff" type="radio" name="wall" value="0"/>
                    <label for="walloff">Off</label>
                </p>
            </div>
        </div>
    </div>
</div>

<script>
    (function(){
        /* Attributes of Game */
        /////////////////////////////////////////////////////////////
        // Canvas & Context
        const canvas = document.getElementById("snake");
        const ctx = canvas.getContext("2d");
        // HTML Game IDs
        const SCREEN_SNAKE = 0;
        const screen_snake = document.getElementById("snake");
        const ele_score = document.getElementById("score_value");
        const speed_setting = document.getElementsByName("speed");
        const wall_setting = document.getElementsByName("wall");
        // HTML Screen IDs (div)
        const SCREEN_MENU = -1, SCREEN_GAME_OVER=1, SCREEN_SETTING=2;
        const screen_menu = document.getElementById("menu");
        const screen_game_over = document.getElementById("gameover");
        const screen_setting = document.getElementById("setting");
        // HTML Event IDs (a tags)
        const button_new_game = document.getElementById("new_game");
        const button_new_game1 = document.getElementById("new_game1");
        const button_new_game2 = document.getElementById("new_game2");
        const button_setting_menu = document.getElementById("setting_menu");
        const button_setting_menu1 = document.getElementById("setting_menu1");
        // Game Control
        const BLOCK = 10;   // size of block rendering
        let SCREEN = SCREEN_MENU;
        let snake;
        let snake_dir;
        let snake_next_dir;
        let snake_speed;
        let food = {x: 0, y: 0};
        let score;
        let wall;
        /* Display Control */
        /////////////////////////////////////////////////////////////
        // 0 for the game
        // 1 for the main menu
        // 2 for the settings screen
        // 3 for the game over screen
        let showScreen = function(screen_opt){
            SCREEN = screen_opt;
            switch(screen_opt){
                case SCREEN_SNAKE:
                    screen_snake.style.display = "block";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "none";
                    screen_game_over.style.display = "none";
                    break;
                case SCREEN_GAME_OVER:
                    screen_snake.style.display = "block";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "none";
                    screen_game_over.style.display = "block";
                    break;
                case SCREEN_SETTING:
                    screen_snake.style.display = "none";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "block";
                    screen_game_over.style.display = "none";
                    break;
            }
        }
        /* Actions and Events  */
        /////////////////////////////////////////////////////////////
        window.onload = function(){
            // HTML Events to Functions
            button_new_game.onclick = function(){newGame();};
            button_new_game1.onclick = function(){newGame();};
            button_new_game2.onclick = function(){newGame();};
            button_setting_menu.onclick = function(){showScreen(SCREEN_SETTING);};
            button_setting_menu1.onclick = function(){showScreen(SCREEN_SETTING);};
            // speed
            setSnakeSpeed(150);
            for(let i = 0; i < speed_setting.length; i++){
                speed_setting[i].addEventListener("click", function(){
                    for(let i = 0; i < speed_setting.length; i++){
                        if(speed_setting[i].checked){
                            setSnakeSpeed(speed_setting[i].value);
                        }
                    }
                });
            }
            // wall setting
            setWall(1);
            for(let i = 0; i < wall_setting.length; i++){
                wall_setting[i].addEventListener("click", function(){
                    for(let i = 0; i < wall_setting.length; i++){
                        if(wall_setting[i].checked){
                            setWall(wall_setting[i].value);
                        }
                    }
                });
            }
            // activate window events
            window.addEventListener("keydown", function(evt) {
                // spacebar detected
                if(evt.code === "Space" && SCREEN !== SCREEN_SNAKE)
                    newGame();
            }, true);
        }
        /* Snake is on the Go (Driver Function)  */
        /////////////////////////////////////////////////////////////
        let mainLoop = function(){
            let _x = snake[0].x;
            let _y = snake[0].y;
            snake_dir = snake_next_dir;   // read async event key
            // Direction 0 - Up, 1 - Right, 2 - Down, 3 - Left
            switch(snake_dir){
                case 0: _y--; break;
                case 1: _x++; break;
                case 2: _y++; break;
                case 3: _x--; break;

            }
            snake.pop(); // tail is removed
            snake.unshift({x: _x, y: _y}); // head is new in new position/orientation
            // Wall Checker
            if(wall === 1){
                // Wall on, Game over test
                if (snake[0].x < 0 || snake[0].x === canvas.width / BLOCK || snake[0].y < 0 || snake[0].y === canvas.height / BLOCK){
                    showScreen(SCREEN_GAME_OVER);
                    return;
                }
            }else{
                // Wall Off, Circle around
                for(let i = 0, x = snake.length; i < x; i++){
                    if(snake[i].x < 0){
                        snake[i].x = snake[i].x + (canvas.width / BLOCK);
                    }
                    if(snake[i].x === canvas.width / BLOCK){
                        snake[i].x = snake[i].x - (canvas.width / BLOCK);
                    }
                    if(snake[i].y < 0){
                        snake[i].y = snake[i].y + (canvas.height / BLOCK);
                    }
                    if(snake[i].y === canvas.height / BLOCK){
                        snake[i].y = snake[i].y - (canvas.height / BLOCK);
                    }
                }
            }
            // Snake vs Snake checker
            for(let i = 1; i < snake.length; i++){
                // Game over test
                if (snake[0].x === snake[i].x && snake[0].y === snake[i].y){
                    showScreen(SCREEN_GAME_OVER);
                    return;
                }
            }
            // Snake eats food checker
            if(checkBlock(snake[0].x, snake[0].y, food.x, food.y)){
                snake[snake.length] = {x: snake[0].x, y: snake[0].y};
                altScore(++score);
                addFood();
                drawFood(food.x, food.y);;
            }
            // Repaint canvas with a dark background
            ctx.beginPath();
            ctx.fillStyle = "#22e5ec65"; // Dark background
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            // Paint snake
           for(let i = 0; i < snake.length; i++){
    if(i === 0){
        drawSnakeHead(snake[i].x, snake[i].y, snake_dir); // 蛇头
    } else {
        activeDot(snake[i].x, snake[i].y); // 身体方块
    }
}

            // Paint food
            drawFood(food.x, food.y);
            // Debug
            //document.getElementById("debug").innerHTML = snake_dir + " " + snake_next_dir + " " + snake[0].x + " " + snake[0].y;
            // Recursive call after speed delay, déjà vu
            setTimeout(mainLoop, snake_speed);
        }
        /* New Game setup */
        /////////////////////////////////////////////////////////////
        let newGame = function(){
            // snake game screen
            showScreen(SCREEN_SNAKE);
            screen_snake.focus();
            // game score to zero
            score = 0;
            altScore(score);
            // initial snake
            snake = [];
            snake.push({x: 0, y: 15});
            snake_next_dir = 1;
            // food on canvas
            addFood();
            // activate canvas event
            canvas.onkeydown = function(evt) {
                changeDir(evt.keyCode);
            }
            mainLoop();
        }
        /* Key Inputs and Actions */
        /////////////////////////////////////////////////////////////
       // 绑定键盘事件
window.addEventListener("keydown", function(evt) {
    if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"," "].includes(evt.key)) {
        evt.preventDefault();  // 阻止箭头和空格的默认行为（滚动/翻页）
    }
}, { passive:false });


// 修改 changeDir，让它支持 WASD + Arrow Keys
let changeDir = function(key){
    switch(key) {
        case 65: // A
        case 37: // Left Arrow
            if (snake_dir !== 1) // not right
                snake_next_dir = 3; // then switch left
            break;
        case 87: // W
        case 38: // Up Arrow
            if (snake_dir !== 2) // not down
                snake_next_dir = 0; // then switch up
            break;
        case 68: // D
        case 39: // Right Arrow
            if (snake_dir !== 3) // not left
                snake_next_dir = 1; // then switch right
            break;
        case 83: // S
        case 40: // Down Arrow
            if (snake_dir !== 0) // not up
                snake_next_dir = 2; // then switch down
            break;
    }
}

        /* Dot for Food or Snake part */
        /////////////////////////////////////////////////////////////
        // Snake color will be white
      // 蛇的方块
let activeDot = function(x, y){
    ctx.fillStyle = "#2E7D32"; // 蛇的颜色
    ctx.fillRect(x * BLOCK, y * BLOCK, BLOCK, BLOCK);
}
let drawSnakeHead = function(x, y, direction){
    let px = x * BLOCK;
    let py = y * BLOCK;

    // 🐍 蛇头主体（绿色）
    ctx.fillStyle = "#4CAF50"; // 小青蛇绿色
    ctx.strokeStyle = "#2E7D32";
    ctx.beginPath();

    if(direction === 0 || direction === 2){
        // 竖向椭圆，稍微尖
        ctx.ellipse(px + BLOCK/2, py + BLOCK/2, BLOCK/2, BLOCK*0.7/2, 0, 0, Math.PI*2);
    } else {
        // 横向椭圆，稍微尖
        ctx.ellipse(px + BLOCK/2, py + BLOCK/2, BLOCK*0.7/2, BLOCK/2, 0, 0, Math.PI*2);
    }
    ctx.fill();
    ctx.stroke();

    // 🐍 大眼睛（白色背景 + 黑色瞳孔）
    let eyeSize = BLOCK/3;
    ctx.fillStyle = "white";
    switch(direction){
        case 0: // Up
            ctx.beginPath();
            ctx.arc(px + BLOCK/4, py + BLOCK/3, eyeSize/2, 0, Math.PI*2);
            ctx.arc(px + 3*BLOCK/4, py + BLOCK/3, eyeSize/2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = "black";
            ctx.beginPath();
            ctx.arc(px + BLOCK/4, py + BLOCK/3, eyeSize/4, 0, Math.PI*2);
            ctx.arc(px + 3*BLOCK/4, py + BLOCK/3, eyeSize/4, 0, Math.PI*2);
            ctx.fill();
            break;
        case 1: // Right
            ctx.beginPath();
            ctx.arc(px + 2*BLOCK/3, py + BLOCK/4, eyeSize/2, 0, Math.PI*2);
            ctx.arc(px + 2*BLOCK/3, py + 3*BLOCK/4, eyeSize/2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = "black";
            ctx.beginPath();
            ctx.arc(px + 2*BLOCK/3, py + BLOCK/4, eyeSize/4, 0, Math.PI*2);
            ctx.arc(px + 2*BLOCK/3, py + 3*BLOCK/4, eyeSize/4, 0, Math.PI*2);
            ctx.fill();
            break;
        case 2: // Down
            ctx.beginPath();
            ctx.arc(px + BLOCK/4, py + 2*BLOCK/3, eyeSize/2, 0, Math.PI*2);
            ctx.arc(px + 3*BLOCK/4, py + 2*BLOCK/3, eyeSize/2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = "black";
            ctx.beginPath();
            ctx.arc(px + BLOCK/4, py + 2*BLOCK/3, eyeSize/4, 0, Math.PI*2);
            ctx.arc(px + 3*BLOCK/4, py + 2*BLOCK/3, eyeSize/4, 0, Math.PI*2);
            ctx.fill();
            break;
        case 3: // Left
            ctx.beginPath();
            ctx.arc(px + BLOCK/3, py + BLOCK/4, eyeSize/2, 0, Math.PI*2);
            ctx.arc(px + BLOCK/3, py + 3*BLOCK/4, eyeSize/2, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = "black";
            ctx.beginPath();
            ctx.arc(px + BLOCK/3, py + BLOCK/4, eyeSize/4, 0, Math.PI*2);
            ctx.arc(px + BLOCK/3, py + 3*BLOCK/4, eyeSize/4, 0, Math.PI*2);
            ctx.fill();
            break;
    }

    // 🐍 吐舌头（三角形小舌头）
    ctx.fillStyle = "red";
    ctx.beginPath();
    let tongueLen = BLOCK/2;
    switch(direction){
        case 0: // Up
            ctx.moveTo(px + BLOCK/2, py - tongueLen);
            ctx.lineTo(px + BLOCK/2 - 3, py);
            ctx.lineTo(px + BLOCK/2 + 3, py);
            break;
        case 1: // Right
            ctx.moveTo(px + BLOCK + tongueLen, py + BLOCK/2);
            ctx.lineTo(px + BLOCK, py + BLOCK/2 - 3);
            ctx.lineTo(px + BLOCK, py + BLOCK/2 + 3);
            break;
        case 2: // Down
            ctx.moveTo(px + BLOCK/2, py + BLOCK + tongueLen);
            ctx.lineTo(px + BLOCK/2 - 3, py + BLOCK);
            ctx.lineTo(px + BLOCK/2 + 3, py + BLOCK);
            break;
        case 3: // Left
            ctx.moveTo(px - tongueLen, py + BLOCK/2);
            ctx.lineTo(px, py + BLOCK/2 - 3);
            ctx.lineTo(px, py + BLOCK/2 + 3);
            break;
    }
    ctx.closePath();
    ctx.fill();
}


// 苹果：圆形
let drawFood = function(x, y){
    ctx.fillStyle = "#d13838ff"; // 苹果颜色
    ctx.beginPath();
    ctx.arc(
        x * BLOCK + BLOCK / 2,  // 圆心X（对齐格子中心）
        y * BLOCK + BLOCK / 2,  // 圆心Y
        BLOCK / 2.5,            // 半径
        0,
        Math.PI * 2
    );
    ctx.fill();
    ctx.closePath();

    // 🍏 小绿叶子（可选装饰）
    ctx.fillStyle = "#3fa34d";
    ctx.beginPath();
    ctx.arc(
        x * BLOCK + BLOCK / 2, 
        y * BLOCK + BLOCK / 2 - BLOCK / 2.5, 
        BLOCK / 6,
        0,
        Math.PI * 2
    );
    ctx.fill();
    ctx.closePath();
}

// 生成苹果
let addFood = function(){
    food.x = Math.floor(Math.random() * ((canvas.width / BLOCK) - 1));
    food.y = Math.floor(Math.random() * ((canvas.height / BLOCK) - 1));

    for(let i = 0; i < snake.length; i++){
        if(checkBlock(food.x, food.y, snake[i].x, snake[i].y)){
            addFood();
            return;
        }
    }
    drawFood(food.x, food.y);
}



        /* Collision Detection */
        /////////////////////////////////////////////////////////////
        let checkBlock = function(x, y, _x, _y){
            return (x === _x && y === _y);
        }
        /* Update Score */
        /////////////////////////////////////////////////////////////
        let altScore = function(score_val){
            ele_score.innerHTML = String(score_val);
        }
        /////////////////////////////////////////////////////////////
        // Change the snake speed...
        // 150 = slow
        // 100 = normal
        // 50 = fast
        let setSnakeSpeed = function(speed_value){
            snake_speed = speed_value;
        }
        /////////////////////////////////////////////////////////////
        let setWall = function(wall_value){
            wall = wall_value;
            if(wall === 0){screen_snake.style.borderColor = "#c83d9cff";}
            if(wall === 1){screen_snake.style.borderColor = "#c83d9cff";}
        }
    })();
</script>