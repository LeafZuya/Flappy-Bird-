<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Snake & Apple 🍎🐍(By LeafZuya)</title>
  <style>
    body {
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: linear-gradient(135deg, #00cc66, #3399ff);
      font-family: Arial, sans-serif;
      position: relative;
      overflow: hidden;
    }

    canvas {
      background: rgba(255,255,255,0.2);
      border: 3px solid #004d26;
      border-radius: 10px;
    }

    #scoreboard {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 18px;
      color: white;
      text-shadow: 1px 1px 3px black;
    }

    #restartBtn {
      position: absolute;
      top: 10px;
      right: 10px;
      padding: 10px 15px;
      font-size: 16px;
      border: none;
      border-radius: 10px;
      background: linear-gradient(135deg, #00cc99, #3399ff);
      color: white;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0,0,0,0.4);
      display: none;
    }

    /* Emoji dekorasi */
    #leaf {
      position: absolute;
      top: 5px;
      right: 5px;
      font-size: 40px;
    }
    #ice {
      position: absolute;
      bottom: 5px;
      left: 5px;
      font-size: 40px;
    }

    /* Control Panel */
    #controls, #joystick {
      position: absolute;
      bottom: 20px;
      display: none;
    }

    #controls {
      grid-template-columns: repeat(3, 60px);
      grid-template-rows: repeat(2, 60px);
      gap: 10px;
      justify-items: center;
      align-items: center;
      left: 50%;
      transform: translateX(-50%);
    }

    #controls button {
      width: 60px;
      height: 60px;
      font-size: 24px;
      border: none;
      border-radius: 10px;
      background: linear-gradient(135deg, #00994d, #0066cc);
      color: white;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0,0,0,0.3);
    }

    /* Joystick */
    #joystick {
      width: 120px;
      height: 120px;
      border-radius: 50%;
      background: rgba(0,0,0,0.2);
      left: 50%;
      transform: translateX(-50%);
      touch-action: none;
    }

    /* Menu */
    #menu {
      position: absolute;
      background: rgba(0,0,0,0.8);
      color: white;
      padding: 20px;
      border-radius: 15px;
      text-align: center;
      box-shadow: 0 4px 10px rgba(0,0,0,0.5);
    }

    #menu button {
      margin: 10px;
      padding: 10px 20px;
      font-size: 18px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      background: linear-gradient(135deg, #00cc99, #3399ff);
      color: white;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="400"></canvas>

  <div id="scoreboard">Score: 0 | High Score: 0</div>
  <button id="restartBtn">🔄 Restart</button>

  <!-- Pad -->
  <div id="controls">
    <div></div>
    <button id="up">⬆️</button>
    <div></div>
    <button id="left">⬅️</button>
    <button id="down">⬇️</button>
    <button id="right">➡️</button>
  </div>

  <!-- Joystick -->
  <div id="joystick"></div>

  <div id="leaf">🍃</div>
  <div id="ice">❄️</div>

  <!-- Menu -->
  <div id="menu">
    <h2>Pilih Kontrol</h2>
    <button onclick="startGame('pad')">🎮 GamePad</button>
    <button onclick="startGame('joystick')">🕹️ Joystick</button>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const box = 20;
    let snake, direction, apple, score, highScore, game;
    let controlType = null;

    const scoreboard = document.getElementById("scoreboard");
    const restartBtn = document.getElementById("restartBtn");
    const menu = document.getElementById("menu");
    const controls = document.getElementById("controls");
    const joystick = document.getElementById("joystick");

    function resetGame() {
      snake = [{x: 9 * box, y: 10 * box}];
      direction = undefined;
      score = 0;
      apple = {x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box};
      highScore = localStorage.getItem("snakeHighScore") || 0;
      updateScore();
    }

    function updateScore() {
      scoreboard.textContent = `Score: ${score} | High Score: ${highScore}`;
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw snake
      for (let i = 0; i < snake.length; i++) {
        ctx.fillStyle = i === 0 ? "#004d26" : "#00cc66";
        ctx.fillRect(snake[i].x, snake[i].y, box, box);
        ctx.strokeStyle = "#003300";
        ctx.strokeRect(snake[i].x, snake[i].y, box, box);
      }

      // Draw apple
      ctx.fillStyle = "red";
      ctx.beginPath();
      ctx.arc(apple.x + box/2, apple.y + box/2, box/2 - 2, 0, 2 * Math.PI);
      ctx.fill();

      // Old head position
      let snakeX = snake[0].x;
      let snakeY = snake[0].y;

      if (direction === "LEFT") snakeX -= box;
      if (direction === "UP") snakeY -= box;
      if (direction === "RIGHT") snakeX += box;
      if (direction === "DOWN") snakeY += box;

      // Eat apple
      if (snakeX === apple.x && snakeY === apple.y) {
        score++;
        if (score > highScore) {
          highScore = score;
          localStorage.setItem("snakeHighScore", highScore);
        }
        apple = {x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box};
      } else {
        snake.pop();
      }

      const newHead = {x: snakeX, y: snakeY};

      // Game over conditions
      if (
        snakeX < 0 || snakeY < 0 || 
        snakeX >= canvas.width || snakeY >= canvas.height ||
        snake.some(segment => segment.x === newHead.x && segment.y === newHead.y)
      ) {
        clearInterval(game);
        alert("Game Over! Score: " + score);
        restartBtn.style.display = "block";
      }

      snake.unshift(newHead);
      updateScore();
    }

    // Direction control
    document.addEventListener("keydown", (event) => {
      if (event.keyCode === 37 && direction !== "RIGHT") direction = "LEFT";
      else if (event.keyCode === 38 && direction !== "DOWN") direction = "UP";
      else if (event.keyCode === 39 && direction !== "LEFT") direction = "RIGHT";
      else if (event.keyCode === 40 && direction !== "UP") direction = "DOWN";
    });

    // Pad buttons
    document.getElementById("up").onclick = () => { if(direction !== "DOWN") direction = "UP"; };
    document.getElementById("down").onclick = () => { if(direction !== "UP") direction = "DOWN"; };
    document.getElementById("left").onclick = () => { if(direction !== "RIGHT") direction = "LEFT"; };
    document.getElementById("right").onclick = () => { if(direction !== "LEFT") direction = "RIGHT"; };

    // Joystick control
    let joyActive = false;
    joystick.addEventListener("touchstart", (e) => {
      joyActive = true;
    });
    joystick.addEventListener("touchend", () => {
      joyActive = false;
    });
    joystick.addEventListener("touchmove", (e) => {
      if (!joyActive) return;
      const rect = joystick.getBoundingClientRect();
      const x = e.touches[0].clientX - (rect.left + rect.width/2);
      const y = e.touches[0].clientY - (rect.top + rect.height/2);
      if (Math.abs(x) > Math.abs(y)) {
        if (x > 0 && direction !== "LEFT") direction = "RIGHT";
        else if (x < 0 && direction !== "RIGHT") direction = "LEFT";
      } else {
        if (y > 0 && direction !== "UP") direction = "DOWN";
        else if (y < 0 && direction !== "DOWN") direction = "UP";
      }
    });

    // Start game
    function startGame(type) {
      controlType = type;
      menu.style.display = "none";
      controls.style.display = (type === "pad") ? "grid" : "none";
      joystick.style.display = (type === "joystick") ? "block" : "none";
      restartBtn.style.display = "none";
      resetGame();
      clearInterval(game);
      game = setInterval(draw, 100);
    }

    // Restart button
    restartBtn.onclick = () => {
      menu.style.display = "block";
      controls.style.display = "none";
      joystick.style.display = "none";
      restartBtn.style.display = "none";
    };
  </script>
</body>
</html>
