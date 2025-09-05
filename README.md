
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Snake & Apple 🍎🐍 (Joystick)</title>
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

    /* Joystick */
    #joystick {
      position: absolute;
      bottom: 40px;
      left: 50%;
      transform: translateX(-50%);
      width: 120px;
      height: 120px;
      background: rgba(255,255,255,0.1);
      border: 3px solid #0066cc;
      border-radius: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      touch-action: none;
    }
    #stick {
      width: 50px;
      height: 50px;
      background: radial-gradient(circle, #00cc66, #0066cc);
      border-radius: 50%;
      position: absolute;
      transition: 0.1s;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="400"></canvas>

  <div id="scoreboard">Score: 0 | High Score: 0</div>
  <button id="restartBtn">🔄 Restart</button>

  <div id="leaf">🍃</div>
  <div id="ice">🧊</div>

  <!-- Joystick -->
  <div id="joystick">
    <div id="stick"></div>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const box = 20;
    let snake = [{x: 9 * box, y: 10 * box}];
    let direction;
    let apple = {x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box};
    let score = 0;
    let highScore = localStorage.getItem("snakeHighScore") || 0;

    const scoreboard = document.getElementById("scoreboard");
    function updateScore() {
      scoreboard.textContent = `Score: ${score} | High Score: ${highScore}`;
    }
    updateScore();

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
      }

      snake.unshift(newHead);
      updateScore();
    }

    // Restart button
    document.getElementById("restartBtn").onclick = () => {
      snake = [{x: 9 * box, y: 10 * box}];
      direction = undefined;
      score = 0;
      apple = {x: Math.floor(Math.random() * 20) * box, y: Math.floor(Math.random() * 20) * box};
      clearInterval(game);
      game = setInterval(draw, 100);
      updateScore();
    };

    // === Joystick Control ===
    const joystick = document.getElementById("joystick");
    const stick = document.getElementById("stick");

    let centerX, centerY;
    joystick.addEventListener("touchstart", (e) => {
      const rect = joystick.getBoundingClientRect();
      centerX = rect.left + rect.width / 2;
      centerY = rect.top + rect.height / 2;
    });

    joystick.addEventListener("touchmove", (e) => {
      const touch = e.touches[0];
      const dx = touch.clientX - centerX;
      const dy = touch.clientY - centerY;

      const angle = Math.atan2(dy, dx);
      const distance = Math.min(Math.sqrt(dx*dx + dy*dy), 40);

      stick.style.transform = `translate(${dx}px, ${dy}px)`;

      if (Math.abs(dx) > Math.abs(dy)) {
        if (dx > 0 && direction !== "LEFT") direction = "RIGHT";
        else if (dx < 0 && direction !== "RIGHT") direction = "LEFT";
      } else {
        if (dy > 0 && direction !== "UP") direction = "DOWN";
        else if (dy < 0 && direction !== "DOWN") direction = "UP";
      }
    });

    joystick.addEventListener("touchend", () => {
      stick.style.transform = "translate(0,0)";
    });

    let game = setInterval(draw, 100);
  </script>
</body>
</html>
