<canvas id="pongCanvas" width="800" height="500"></canvas>
<script>
const canvas = document.getElementById('pongCanvas');
const ctx = canvas.getContext('2d');

// Paddle settings
const paddleWidth = 8, paddleHeight = 80;
let player1Y = (canvas.height - paddleHeight) / 2;
let player2Y = (canvas.height - paddleHeight) / 2;
const paddleSpeed = 7;

// Ball settings
let ballX = canvas.width / 2;
let ballY = canvas.height / 2;
let ballSpeedX = Math.random() > 0.5 ? 5 : -5;
let ballSpeedY = (Math.random() * 4) - 2;
let ballRadius = 10;
let ballColor = "#fff";

// Score + game state
let player1Score = 0, player2Score = 0;
const winningScore = 10;
let gameOver = false;

// Modes
let gravityMode = false;
const gravityStrength = 0.3; // acceleration downwards

// Draw paddles
function drawPaddle(x, y) {
  ctx.fillStyle = "#fff";
  ctx.fillRect(x, y, paddleWidth, paddleHeight);
}

// Draw ball
function drawBall() {
  ctx.fillStyle = ballColor;
  ctx.beginPath();
  ctx.arc(ballX, ballY, ballRadius, 0, Math.PI * 2);
  ctx.fill();
}

// Reset ball after score
function resetBall() {
  if (player1Score >= winningScore || player2Score >= winningScore) {
    gameOver = true;
    return;
  }
  ballX = canvas.width / 2;
  ballY = canvas.height / 2;
  ballSpeedX = Math.random() > 0.5 ? 5 : -5;
  ballSpeedY = (Math.random() * 4) - 2;
}

// Draw everything
function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (gameOver) {
    ctx.fillStyle = "#fff";
    ctx.font = "40px Arial";
    ctx.fillText("Game Over", canvas.width / 2 - 100, canvas.height / 2);
    return;
  }

  drawPaddle(0, player1Y);
  drawPaddle(canvas.width - paddleWidth, player2Y);
  drawBall();

  // Scores
  ctx.fillStyle = "#fff";
  ctx.font = "20px Arial";
  ctx.fillText(player1Score, 100, 50);
  ctx.fillText(player2Score, canvas.width - 100, 50);
}

// Move everything
function move() {
  if (gameOver) return;

  ballX += ballSpeedX;
  ballY += ballSpeedY;

  // Gravity mode
  if (gravityMode) {
    ballSpeedY += gravityStrength;
  }

  // Bounce top/bottom
  if (ballY - ballRadius < 0 || ballY + ballRadius > canvas.height) {
    ballSpeedY = -ballSpeedY;
  }

  // Left paddle
  if (ballX - ballRadius < paddleWidth) {
    if (ballY > player1Y && ballY < player1Y + paddleHeight) {
      ballSpeedX = -ballSpeedX;
      let deltaY = ballY - (player1Y + paddleHeight / 2);
      ballSpeedY = deltaY * 0.3;
    } else {
      player2Score++;
      resetBall();
    }
  }

  // Right paddle
  if (ballX + ballRadius > canvas.width - paddleWidth) {
    if (ballY > player2Y && ballY < player2Y + paddleHeight) {
      ballSpeedX = -ballSpeedX;
      let deltaY = ballY - (player2Y + paddleHeight / 2);
      ballSpeedY = deltaY * 0.3;
    } else {
      player1Score++;
      resetBall();
    }
  }

  // Simple AI for player2
  if (player2Y + paddleHeight / 2 < ballY) {
    player2Y += paddleSpeed - 2;
  } else {
    player2Y -= paddleSpeed - 2;
  }
}

// Player controls
document.addEventListener("keydown", e => {
  switch (e.key) {
    case "w": player1Y -= paddleSpeed; break;
    case "s": player1Y += paddleSpeed; break;
    case "g": gravityMode = !gravityMode; break; // toggle Gravity Mode
  }
});

// Game loop
function gameLoop() {
  move();
  draw();
  requestAnimationFrame(gameLoop);
}
gameLoop();
</script>


Short Reflection:

What was the most challenging part of adding your feature?
  Figuring out how to describe what we wanted to do in code.

How did using commits help you track progress?
  Using commits helped us keep track of which change was made when and what it included. They kept our work organized.

What did you learn about debugging or OOP in this project?
  We learned about how to organize our game characters, such as the ball, the paddle, and the score using classes. 