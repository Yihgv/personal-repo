--- 
layout: opencs 
title: Snake Game 
permalink: /snake 
---

<style>
    body {
        background-color: #1e1e1e; /* Dark background */
    }
    .wrap {
        margin-left: auto;
        margin-right: auto;
    }
    canvas {
        display: block;
        border-style: dotted;
        border-width: 10px;
        border-color: #26c62e6b;
    }
    canvas:focus {
        outline: none;
    }
    /* All screens style */
    #gameover p, #setting p, #menu p {
        font-size: 40px;
    }
    #gameover a, #setting a, #menu a {
        font-size: 60px;
        display: block;
    }
    #gameover a:hover, #setting a:hover, #menu a:hover {
        cursor: pointer;
    }
    #gameover a:hover::before, #setting a:hover::before, #menu a:hover::before {
        content: ">";
        margin-right: 20px;
    }
    #menu {
        display: block;
    }
    #gameover {
        display: none;
    }
    #setting {
        display: none;
    }
    #setting input {
        display: none;
    }
    #setting label {
        cursor: pointer;
    }
    #setting input:checked + label {
        background-color: #881313ff;
        color: #000000;
    }
</style>

<h2>Snake</h2>
<div class="container">
    <p class="fs-4">Score: <span id="score_value">0</span></p>
    <div class="container bg-secondary" style="text-align:center;">
        <!-- Main Menu -->
        <div id="menu" class="py-4 text-light">
            <p>Welcome to Snake, press <span style="background-color: #b62727ff; color: #000000">space</span> to begin</p>
            <a id="new_game" class="link-alert">new game</a>
            <a id="setting_menu" class="link-alert">settings</a>
        </div>
        <!-- Game Over -->
        <div id="gameover" class="py-4 text-light">
            <p>Game Over, press <span style="background-color: #b31111ff; color: #000000">space</span> to try again</p>
            <a id="new_game1" class="link-alert">new game</a>
            <a id="setting_menu1" class="link-alert">settings</a>
        </div>
        <!-- Play Screen -->
        <canvas id="snake" class="wrap" width="320" height="320" tabindex="1"></canvas>
        <!-- Settings Screen -->
        <div id="setting" class="py-4 text-light">
            <p>Settings Screen, press <span style="background-color: #574242ff; color: #000000">space</span> to go back to playing</p>
            <a id="new_game2" class="link-alert">new game</a>
            <br>
            <p>Speed:
                <input id="speed1" type="radio" name="speed" value="240" checked/>
                <label for="speed1">Slow</label>
                <input id="speed2" type="radio" name="speed" value="75"/>
                <label for="speed2">Normal</label>
                <input id="speed3" type="radio" name="speed" value="35"/>
                <label for="speed3">Fast</label>
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

<script>
    (function(){
        /* Attributes of Game */
        const canvas = document.getElementById("snake");
        const ctx = canvas.getContext("2d");
        const SCREEN_SNAKE = 0;
        const screen_snake = document.getElementById("snake");
        const ele_score = document.getElementById("score_value");
        const speed_setting = document.getElementsByName("speed");
        const wall_setting = document.getElementsByName("wall");
        const SCREEN_MENU = -1, SCREEN_GAME_OVER=1, SCREEN_SETTING=2;
        const screen_menu = document.getElementById("menu");
        const screen_game_over = document.getElementById("gameover");
        const screen_setting = document.getElementById("setting");
        const button_new_game = document.getElementById("new_game");
        const button_new_game1 = document.getElementById("new_game1");
        const button_new_game2 = document.getElementById("new_game2");
        const button_setting_menu = document.getElementById("setting_menu");
        const button_setting_menu1 = document.getElementById("setting_menu1");

        const BLOCK = 10; // size of block rendering
        let SCREEN = SCREEN_MENU;
        let snake;
        let snake_dir;
        let snake_next_dir;
        let snake_speed;
        let food = {x: 0, y: 0};
        let score;
        let wall;

        // Change colors for background, snake, and food
        const BACKGROUND_COLOR = "#2c2f36";  // Dark background
        const SNAKE_COLOR = "#00ff00";       // Green snake color
        const FOOD_COLOR = "#ff0000";        // Red food color

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

        window.onload = function(){
            button_new_game.onclick = function(){newGame();};
            button_new_game1.onclick = function(){newGame();};
            button_new_game2.onclick = function(){newGame();};
            button_setting_menu.onclick = function(){showScreen(SCREEN_SETTING);};
            button_setting_menu1.onclick = function(){showScreen(SCREEN_SETTING);};
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
            window.addEventListener("keydown", function(evt) {
                if(evt.code === "Space" && SCREEN !== SCREEN_SNAKE)
                    newGame();
            }, true);
        }

        let mainLoop = function(){
            let _x = snake[0].x;
            let _y = snake[0].y;
            snake_dir = snake_next_dir;
            switch(snake_dir){
                case 0: _y--; break;
                case 1: _x++; break;
                case 2: _y++; break;
                case 3: _x--; break;
            }
            snake.pop();
            snake.unshift({x: _x, y: _y});
            if(wall === 1){
                if (snake[0].x < 0 || snake[0].x >= canvas.width / BLOCK || snake[0].y < 0 || snake[0].y >= canvas.height / BLOCK) {
                    return gameOver();
                }
            }else{
                if(snake[0].x < 0) snake[0].x = (canvas.width / BLOCK) - 1;
                if(snake[0].x >= canvas.width / BLOCK) snake[0].x = 0;
                if(snake[0].y < 0) snake[0].y = (canvas.height / BLOCK) - 1;
                if(snake[0].y >= canvas.height / BLOCK) snake[0].y = 0;
            }
            if(snake[0].x === food.x && snake[0].y === food.y){
                score += 10;
                let new_growth = Math.floor(Math.random() * 5) + 1;
                for(let i = 0; i < new_growth; i++) snake.push({x: snake[snake.length - 1].x, y: snake[snake.length - 1].y});
                randomizeFood();
            }
            // Check collision with itself
            for(let i = 1; i < snake.length; i++){
                if(snake[0].x === snake[i].x && snake[0].y === snake[i].y) return gameOver();
            }
            render();
        }

        let render = function(){
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = BACKGROUND_COLOR;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.shadowBlur = 15; // Glow effect
            ctx.shadowColor = "#00ff00"; // Green glow for the snake
            ctx.fillStyle = SNAKE_COLOR;
            for(let i = 0; i < snake.length; i++){
                ctx.fillRect(snake[i].x * BLOCK, snake[i].y * BLOCK, BLOCK, BLOCK);
            }
            ctx.fillStyle = FOOD_COLOR; // Red food color
            ctx.fillRect(food.x * BLOCK, food.y * BLOCK, BLOCK, BLOCK);
            ele_score.textContent = score;
        }

        let newGame = function(){
            showScreen(SCREEN_SNAKE);
            snake = [{x: 10, y: 10}];
            snake_dir = 0;
            snake_next_dir = 0;
            score = 0;
            randomizeFood();
            window.setInterval(mainLoop, snake_speed);
        }

        let randomizeFood = function(){
            food.x = Math.floor(Math.random() * canvas.width / BLOCK);
            food.y = Math.floor(Math.random() * canvas.height / BLOCK);
        }

        let setSnakeSpeed = function(speed){
            snake_speed = speed;
        }

        let gameOver = function(){
            showScreen(SCREEN_GAME_OVER);
        }

        let setWall = function(val){
            wall = val;
        }
    })();
</script>
