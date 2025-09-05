# Dino
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>The Dino — LeafZuya Edition</title>
  <style>
    :root{
      --bg-top:#0fb26b;   /* hijau tebal */
      --bg-bottom:#3aa0f0; /* biru */
      --glow: rgba(255,230,130,0.12); /* sedikit kuning */
      --ground: #2b7a3a;
      --text: #0b2a1a;
      --accent: #ffd24d;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
    body{
      display:flex; align-items:center; justify-content:center;
      background: linear-gradient(180deg, var(--bg-top) 0%, var(--bg-bottom) 100%);
      overflow:hidden;
    }
    /* subtle yellow sun-glow */
    .sun {
      position: absolute;
      right:6%;
      top:6%;
      width:220px;height:220px;border-radius:50%;
      background: radial-gradient(circle at 30% 30%, var(--accent), rgba(255,210,90,0.08) 25%, transparent 60%);
      filter: blur(12px);
      pointer-events:none;
      opacity:.9;
    }

    .wrap{
      width: min(920px, 95vw);
      max-width:920px;
      margin: 20px;
      position: relative;
    }

    header.appbar{
      display:flex; align-items:center; justify-content:space-between;
      gap:12px; padding:14px 18px; color:white;
      text-shadow:0 2px 6px rgba(0,0,0,0.25);
    }
    header h1{font-size:1.1rem; margin:0; color:#fff;}
    header .controls{display:flex; gap:8px; align-items:center;}
    header .btn {
      background: linear-gradient(135deg,#4caf50,#2196f3);
      border:none;padding:8px 12px;border-radius:10px;color:#fff;font-weight:700;cursor:pointer;
      box-shadow:0 6px 14px rgba(0,0,0,0.18);
    }
    .stage {
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.06));
      border-radius:14px; padding:16px;
      box-shadow: 0 18px 50px rgba(0,0,0,0.18), inset 0 1px rgba(255,255,255,0.03);
    }

    /* canvas center */
    .game-area{
      display:flex; align-items:center; justify-content:center;
      gap:16px; position:relative;
      padding: 10px;
    }

    canvas#gameCanvas{
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.03));
      border-radius:12px;
      width:100%;
      max-width:820px;
      height:480px;
      display:block;
      box-shadow: 0 10px 30px rgba(0,0,0,0.2);
    }

    /* info overlay */
    .hud{
      position:absolute; left:18px; top:18px; color:#fff; text-shadow:0 1px 3px rgba(0,0,0,.5);
      display:flex; gap:10px; align-items:center; font-weight:700;
    }
    .hud .chip{ background: rgba(0,0,0,0.25); padding:6px 10px; border-radius:8px; font-size:0.95rem;}
    .centerOverlay{
      position:absolute; left:50%; top:50%; transform:translate(-50%,-50%); color:white; text-align:center; display:none;
    }
    .centerOverlay.active{display:block}
    .centerOverlay h2{margin:0 0 10px 0; font-size:1.8rem; letter-spacing:0.6px}
    .centerOverlay button{margin-top:12px;padding:10px 16px;border-radius:12px;border:none;background:#ffd24d;font-weight:800;cursor:pointer}

    footer{margin-top:12px;color:#052215; text-align:center; opacity:0.95; font-weight:700; font-size:.9rem}

    /* small responsive tweaks */
    @media (max-width:560px){
      canvas#gameCanvas{height:420px}
      header h1{font-size:1rem}
    }
  </style>
</head>
<body>
  <div class="sun" aria-hidden="true"></div>

  <div class="wrap">
    <header class="appbar">
      <h1>🌿 The Dino — LeafZuya Edition</h1>
      <div class="controls">
        <div style="color:white; font-weight:700; font-size:.95rem">Player: <span id="playerName">Guest</span></div>
        <button id="soundToggle" class="btn">🔊 On</button>
        <button id="resetHigh" class="btn" title="Reset highscore">♻ Reset HS</button>
      </div>
    </header>

    <div class="stage">
      <div class="game-area">
        <div style="position:relative; width:100%;">
          <div class="hud">
            <div class="chip">Score: <span id="score">0</span></div>
            <div class="chip">High: <span id="high">0</span></div>
            <div class="chip">Speed: <span id="spd">1.0x</span></div>
          </div>

          <canvas id="gameCanvas" width="820" height="480" aria-label="Game canvas"></canvas>

          <div class="centerOverlay" id="overlay">
            <h2 id="overlayTitle">Tap / Space untuk mulai</h2>
            <div id="overlaySub" style="opacity:.9">Hindari cactus dan burung (bird muncul setelah score ≥ 500)</div>
            <button id="startBtn">Start</button>
          </div>

        </div>
      </div>
    </div>

    <footer>Made with ❤️ by LeafZuya — Have Fun!</footer>
  </div>

  <script>
  /* =========================
     The Dino Engine (modular)
     - single-file, no assets required (uses simple drawing + emojis)
     ========================= */

  (function(){
    // Canvas + ctx
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    // HUD
    const scoreEl = document.getElementById('score');
    const highEl = document.getElementById('high');
    const spdEl = document.getElementById('spd');
    const overlay = document.getElementById('overlay');
    const overlayTitle = document.getElementById('overlayTitle');
    const startBtn = document.getElementById('startBtn');
    const soundToggle = document.getElementById('soundToggle');
    const resetHigh = document.getElementById('resetHigh');
    const playerNameEl = document.getElementById('playerName');

    // Dimensions
    let W = canvas.width, H = canvas.height;
    function resize(){

      // keep fixed logical size (we already set canvas), but support DPR for crisp
      const dpr = Math.max(1, window.devicePixelRatio || 1);
      canvas.width = W * dpr;
      canvas.height = H * dpr;
      canvas.style.width = W + 'px';
      canvas.style.height = H + 'px';
      ctx.setTransform(dpr,0,0,dpr,0,0);
    }
    resize();

    // Game state
    let state = 'idle'; // idle, running, gameover
    let score = 0;
    let high = Number(localStorage.getItem('dino_highscore') || 0);
    highEl.textContent = high;
    let speedMultiplier = 1; // scales over time
    let soundOn = true;

    // Ground
    const groundY = H - 80;

    // Dino (player)
    class Dino {
      constructor(){
        this.w = 52; this.h = 50;
        this.x = 90;
        this.y = groundY - this.h;
        this.vy = 0;
        this.gravity = 0.95;
        this.jumpForce = -18;
        this.grounded = true;
        this.ducking = false;
        this.frame = 0;
      }
      update(dt){
        this.vy += this.gravity * dt;
        this.y += this.vy * dt;
        if(this.y >= groundY - this.h){
          this.y = groundY - this.h;
          this.vy = 0; this.grounded = true;
        } else this.grounded = false;
        this.frame++;
      }
      jump(){
        if(this.grounded){
          this.vy = this.jumpForce;
          this.grounded = false;
          playFx('jump');
        }
      }
      draw(ctx){
        // body
        ctx.save();
        ctx.translate(this.x, this.y);
        // body rectangle
        ctx.fillStyle = '#fff';
        ctx.strokeStyle = '#0b2a1a';
        ctx.lineWidth = 3;
        roundRect(ctx, 0, 0, this.w, this.h, 8, true, true);
        // eye
        ctx.fillStyle = '#0b2a1a';
        ctx.beginPath(); ctx.arc(38, 18, 4, 0, Math.PI*2); ctx.fill();
        // little gradient highlight (yellow)
        const g = ctx.createLinearGradient(0,0,this.w,0); g.addColorStop(0,'rgba(255,230,130,0.14)'); g.addColorStop(1,'transparent');
        ctx.fillStyle = g; ctx.fillRect(0,0,this.w,this.h);
        ctx.restore();
      }
      bounds(){ return {x:this.x, y:this.y, w:this.w, h:this.h}; }
    }

    // Obstacles
    class Cactus {
      constructor(x, w, h){
        this.x = x; this.w = w; this.h = h; this.y = groundY - h;
        this.passed = false;
      }
      update(dt){
        this.x -= (220 * speedMultiplier) * dt;
      }
      draw(ctx){
        ctx.save();
        ctx.fillStyle = '#0f6b2f';
        roundRect(ctx, this.x, this.y, this.w, this.h, 6, true, true);
        // small arms
        ctx.fillStyle = '#15903b';
        ctx.fillRect(this.x - 4, this.y + this.h*0.3, 8, 6);
        ctx.fillRect(this.x + this.w -4, this.y + this.h*0.5, 8, 6);
        ctx.restore();
      }
      bounds(){ return {x:this.x, y:this.y, w:this.w, h:this.h}; }
    }

    class Bird {
      constructor(x,y, size=36){
        this.x = x; this.y = y; this.size = size;
        this.flap = 0;
      }
      update(dt){
        this.x -= (260 * speedMultiplier) * dt;
        this.flap += dt*12;
      }
      draw(ctx){
        ctx.save();
        ctx.translate(this.x, this.y);
        // body (emoji-like)
        ctx.fillStyle = '#fff';
        ctx.font = this.size + 'px serif';
        ctx.textAlign = 'center';
        ctx.textBaseline = 'middle';
        ctx.fillText('🕊️', 0, 0);
        ctx.restore();
      }
      bounds(){ return {x:this.x - this.size/2, y:this.y - this.size/2, w:this.size, h:this.size}; }
    }

    // helper: draw rounded rect
    function roundRect(ctx,x,y,w,h,r,fill,stroke){
      if(typeof r === 'undefined') r=5;
      ctx.beginPath();
      ctx.moveTo(x+r,y);
      ctx.arcTo(x+w,y, x+w,y+h, r);
      ctx.arcTo(x+w,y+h, x,y+h, r);
      ctx.arcTo(x,y+h, x,y, r);
      ctx.arcTo(x,y, x+w,y, r);
      ctx.closePath();
      if(fill) ctx.fill();
      if(stroke) ctx.stroke();
    }

    // particle sparks on jump/collision
    class Particle {
      constructor(x,y, vx,vy, life, color){
        this.x=x;this.y=y;this.vx=vx;this.vy=vy;this.life=life;this.color=color;
      }
      update(dt){ this.x+=this.vx*dt; this.y+=this.vy*dt; this.vy+=300*dt; this.life-=dt;}
      draw(ctx){ if(this.life>0){ ctx.fillStyle=this.color; ctx.globalAlpha=Math.max(0, this.life); ctx.fillRect(this.x, this.y, 3,3); ctx.globalAlpha=1; } }
    }

    // main globals
    const player = new Dino();
    let obstacles = []; // mix of cactus & bird
    let particles = [];
    let spawnTimer = 0;
    let rand = (a,b)=> a + Math.random()*(b-a);
    let lastTime = 0;

    // sounds (very small beep via WebAudio) — optional
    const AudioCtx = window.AudioContext || window.webkitAudioContext;
    const audioCtx = AudioCtx ? new AudioCtx() : null;
    let fxEnabled = true;
    function playFx(name){
      if(!fxEnabled || !audioCtx || !soundOn) return;
      const t = audioCtx.currentTime;
      if(name==='jump'){
        const o = audioCtx.createOscillator(); const g = audioCtx.createGain();
        o.frequency.value = 420; o.type='sine'; g.gain.value = 0.06;
        o.connect(g); g.connect(audioCtx.destination); o.start(); o.stop(t+0.08);
      } else if(name==='hit'){
        const o = audioCtx.createOscillator(); const g=audioCtx.createGain();
        o.type='square'; o.frequency.value = 120; g.gain.value=0.12;
        o.connect(g); g.connect(audioCtx.destination); o.start(); o.stop(t+0.18);
      }
    }

    // input handlers
    function onStart(){
      if(state==='idle'){ state='running'; overlay.classList.remove('active'); lastTime = performance.now(); requestAnimationFrame(loop); playFx('jump'); }
      else if(state==='gameover') reset();
    }
    function onJump(){
      if(state==='idle'){ onStart(); }
      if(state==='running'){ player.jump(); createJumpParticles(); }
    }
    function onRestart(){
      reset();
    }

    // register keyboard / touch / click
    window.addEventListener('keydown', (e)=>{
      if(e.code==='Space' || e.code==='ArrowUp') { e.preventDefault(); onJump(); }
      if(e.code==='KeyR' && state==='gameover') onRestart();
    });
    canvas.addEventListener('click', () => onJump());
    canvas.addEventListener('touchstart', (e)=>{ e.preventDefault(); onJump(); }, {passive:false});
    startBtn.addEventListener('click', onStart);
    soundToggle.addEventListener('click', ()=>{
      soundOn = !soundOn;
      soundToggle.textContent = soundOn ? '🔊 On' : '🔈 Off';
      // resume audio context on user gesture
      if(audioCtx && audioCtx.state === 'suspended' && soundOn) audioCtx.resume();
    });
    resetHigh.addEventListener('click', ()=>{
      localStorage.removeItem('dino_highscore'); high = 0; highEl.textContent = high;
    });

    // create jump particles
    function createJumpParticles(){
      for(let i=0;i<10;i++){
        const p = new Particle(player.x + rand(0, player.w), player.y + player.h, rand(-80,80), rand(-260,-120), 0.6 + Math.random()*0.3, 'rgba(255,220,120,0.9)');
        particles.push(p);
      }
    }

    // spawn logic
    function spawnObstacle(){
      // always spawn cactus
      const cw = Math.round(rand(18,38));
      const ch = Math.round(rand(36,68));
      const c = new Cactus(W + 40, cw, ch);
      obstacles.push(c);
      // sometimes spawn double cactus cluster
      if(Math.random() < 0.18){
        const c2 = new Cactus(W + 40 + cw + rand(12,36), Math.round(rand(14,28)), Math.round(rand(32,56)));
        obstacles.push(c2);
      }
      // spawn bird if score >= 500 with some probability
      if(score >= 500 && Math.random() < 0.32){
        const by = groundY - 160 - Math.random()*60;
        const b = new Bird(W + 80, by, 36);
        obstacles.push(b);
      }
    }

    // collision detection (AABB)
    function collides(a,b){
      return !(a.x + a.w < b.x || a.x > b.x + b.w || a.y + a.h < b.y || a.y > b.y + b.h);
    }

    // main loop
    function loop(ts){
      const dt = Math.min(0.033, (ts - (lastTime||ts))/1000) || 0.016; // cap dt
      lastTime = ts;

      // update when running
      if(state==='running'){
        // speed increases with score
        speedMultiplier = 1 + Math.min(2.4, score / 800);
        spdEl.textContent = speedMultiplier.toFixed(2) + 'x';

        // update player and obstacles
        player.update(dt*60);
        obstacles.forEach(o=> o.update(dt*60));
        particles.forEach(p=> p.update(dt));

        // spawn new obstacles periodically
        spawnTimer += dt*60;
        if(spawnTimer > Math.max(40 - speedMultiplier*6, 18)){
          spawnTimer = 0;
          spawnObstacle();
        }

        // remove offscreen obstacles
        obstacles = obstacles.filter(o => (o.x + (o.w||o.size) > -50));

        // collision check
        for(let ob of obstacles){
          const A = player.bounds();
          const B = ob.bounds();
          if(collides(A,B)){
            // collision!
            state = 'gameover';
            overlayTitle.textContent = 'Game Over';
            overlay.classList.add('active');
            overlay.querySelector('#overlaySub')?.remove?.(); // optional cleanup
            overlay.querySelector('button').textContent = 'Restart';
            overlay.style.display = 'block';
            playFx('hit');
            // update highscore
            if(score > high){ high = score; localStorage.setItem('dino_highscore', high); highEl.textContent = high; }
            break;
          }
        }

        // scoring: increase score when obstacles pass player
        for(let ob of obstacles){
          if(!ob.passed && (ob.x + (ob.w||ob.size) < player.x)){
            ob.passed = true;
            score += 10; // increment by 10 for bigger steps => reach 500 quicker
            scoreEl.textContent = score;
          }
        }

        // particles cleanup
        particles = particles.filter(p => p.life > 0);

      }

      // render
      render();

      if(state!=='gameover') requestAnimationFrame(loop);
    }

    // render function
    function render(){
      ctx.clearRect(0,0,W,H);

      // background gradient (soft)
      const grad = ctx.createLinearGradient(0,0,0,H);
      grad.addColorStop(0, 'rgba(255,255,255,0.04)');
      grad.addColorStop(0.2, 'rgba(255,255,255,0.01)');
      grad.addColorStop(1, 'rgba(0,0,0,0.05)');
      ctx.fillStyle = grad;
      ctx.fillRect(0,0,W,H);

      // ground shadow / parallax stripe
      ctx.fillStyle = '#114a2a';
      ctx.fillRect(0, groundY + 12, W, H - groundY - 12);

      // drawing ground line
      ctx.fillStyle = '#214f33';
      ctx.fillRect(0, groundY, W, 10);

      // draw cactus & birds
      obstacles.forEach(o => o.draw(ctx));

      // draw player
      player.draw(ctx);

      // particles
      particles.forEach(p => p.draw(ctx));

      // HUD (drawn on canvas as well)
      ctx.fillStyle = '#fff';
      ctx.font = '18px Inter, Arial';
      ctx.fillText('Score: ' + score, W - 160, 30);

      // small decorative falling emojis (draw directly to canvas occasionally)
      // (we also created DOM falling emojis below for fanciness)
    }

    // reset game
    function reset(){
      // reset variables
      obstacles = [];
      particles = [];
      score = 0;
      scoreEl.textContent = score;
      overlay.classList.remove('active');
      overlay.style.display = 'none';
      state = 'idle';
      player.y = groundY - player.h;
      player.vy = 0;
      speedMultiplier = 1;
      lastTime = performance.now();
      requestAnimationFrame(loop);
    }

    // DOM falling emojis decoration (leaf & ice)
    (function fallingEmojis(){
      const emojis = ['🍃','🧊'];
      setInterval(()=>{
        const el = document.createElement('div');
        el.className = 'falling';
        el.textContent = emojis[Math.floor(Math.random()*emojis.length)];
        el.style.left = Math.random()*(W - 40) + 'px';
        el.style.top = '-40px';
        el.style.fontSize = (12 + Math.random()*26) + 'px';
        el.style.animationDuration = (3 + Math.random()*4) + 's';
        document.body.appendChild(el);
        setTimeout(()=> el.remove(), 8000);
      }, 600);
    })();

    // initialize overlay
    overlay.classList.add('active');
    overlay.style.display = 'block';
    overlayTitle.textContent = 'Tap / Space untuk mulai';
    overlay.querySelector('button').textContent = 'Start';

    // expose small API for debug
    window.__dino = { reset, spawnObstacle };

    // start with idle state
    playerNameEl.textContent = 'LeafZuya';
    highEl.textContent = high;
    // ensure audio context resumes on user gesture
    window.addEventListener('click', ()=>{ if(audioCtx && audioCtx.state==='suspended') audioCtx.resume(); });

  })();
  </script>
</body>
</html>
