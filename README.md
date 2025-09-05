# Flappy-Bird-
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
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="400" height="600"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // load gambar burung custom (pakai link GitHub / lokal)
    const birdImg = new Image();
    birdImg.src = "Iwak.jpg"; 
    // ganti dengan nama file kamu di repo GitHub, misalnya "shiroko.png"

    let bird = { x: 50, y: 150, width: 40, height: 40, gravity: 0.6, lift: -10, velocity: 0 };
    let pipes = [];
    let frame = 0;
    let score = 0;
    let gameOver = false;

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
    }

    document.addEventListener("keydown", (e) => {
      if (e.code === "Space") {
        bird.velocity = bird.lift;
      }
    });

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

    update();
  </script>
</body>
</html>
