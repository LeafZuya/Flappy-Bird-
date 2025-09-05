# FLappy-Shiroko
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Flappy Shiroko 🐺 (By LeafZuya 🥳)</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: linear-gradient(to bottom, #00cc66, #3399ff);
      position: relative;
    }

    canvas {
      background: transparent;
      display: block;
    }

    /* Emoji jatuh */
    .falling {
      position: absolute;
      font-size: 24px;
      animation: fall linear forwards;
      opacity: 0.8;
    }
    @keyframes fall {
      0% { transform: translateY(-50px) rotate(0deg); opacity: 1; }
      100% { transform: translateY(110vh) rotate(360deg); opacity: 0; }
    }

    /* Tombol restart */
    #restartBtn {
      display: none;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 12px 24px;
      font-size: 20px;
      background: linear-gradient(135deg, #00cc66, #3399ff);
      border: none;
      border-radius: 12px;
      cursor: pointer;
      color: white;
      font-weight: bold;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    }
    #restartBtn:hover {
      transform: translate(-50%, -50%) scale(1.1);
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>
  <button id="restartBtn">🔄 Restart</button>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    const restartBtn = document.getElementById("restartBtn");

    // load gambar burung custom
    const birdImg = new Image();
    birdImg.src = "Iwak.jpg"; // ganti sesuai repo GitHub kamu

    let bird, pipes, frame, score, gameOver;

    function resetGame() {
      bird = { x: 50, y: 150, width: 40, height: 40, gravity: 0.6, lift: -10, velocity: 0 };
      pipes = [];
      frame = 0;
      score = 0;
      gameOver = false;
      restartBtn.style.display = "none";
      update();
    }

    function drawBird() {
      ctx.drawImage(birdImg, bird.x - bird.width/2, bird.y - bird.height/2, bird.width, bird.height);
    }

    function drawPipes() {
      ctx.fillStyle = "#006600";
      pipes.forEach(pipe => {
        ctx.fillRect(pipe.x, 0, pipe.width, pipe.top);
        ctx.fillRect(pipe.x, canvas.height - pipe.bottom, pipe.width, pipe.bottom);
      });
    }

    function update() {
      if (gameOver) return;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // burung
      bird.velocity += bird.gravity;
      bird.y += bird.velocity;
      drawBird();

      // tabrakan atas bawah
      if (bird.y + bird.height / 2 >= canvas.height || bird.y - bird.height / 2 <= 0) {
        endGame();
      }

      // pipes
      if (frame % 90 === 0) {
        let gap = 120;
        let top = Math.random() * (canvas.height - gap - 100) + 50;
        pipes.push({ x: canvas.width, width: 40, top: top, bottom: canvas.height - top - gap });
      }

      pipes.forEach((pipe, i) => {
        pipe.x -= 2;
        if (pipe.x + pipe.width < 0) {
          pipes.splice(i, 1);
          score++;
        }

        if (bird.x + bird.width / 2 > pipe.x &&
            bird.x - bird.width / 2 < pipe.x + pipe.width &&
            (bird.y - bird.height / 2 < pipe.top || bird.y + bird.height / 2 > canvas.height - pipe.bottom)) {
          endGame();
        }
      });

      drawPipes();

      // skor
      ctx.fillStyle = "white";
      ctx.font = "20px Arial";
      ctx.fillText("Score: " + score, 10, 25);

      frame++;
      requestAnimationFrame(update);
    }

    function endGame() {
      gameOver = true;
      ctx.fillStyle = "rgba(0,0,0,0.7)";
      ctx.fillRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "white";
      ctx.font = "30px Arial";
      ctx.fillText("Game Over!", 120, 250);
      ctx.fillText("Score: " + score, 150, 300);

      // tampilkan tombol restart
      restartBtn.style.display = "block";
    }

    // Kontrol burung
    function flap() {
      if (!gameOver) bird.velocity = bird.lift;
    }
    document.addEventListener("keydown", (e) => {
      if (e.code === "Space") flap();
    });
    document.addEventListener("click", flap);
    document.addEventListener("touchstart", flap);

    restartBtn.addEventListener("click", resetGame);

    // Emoji daun dan es jatuh
    const emojis = ["🍃", "🧊"];
    function createFallingEmoji() {
      const emoji = document.createElement("div");
      emoji.classList.add("falling");
      emoji.textContent = emojis[Math.floor(Math.random() * emojis.length)];
      emoji.style.left = Math.random() * window.innerWidth + "px";
      emoji.style.animationDuration = (3 + Math.random() * 3) + "s";
      document.body.appendChild(emoji);

      setTimeout(() => emoji.remove(), 5000);
    }
    setInterval(createFallingEmoji, 700);

    resetGame(); // mulai game
  </script>
</body>
</html>
