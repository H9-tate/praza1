<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>⚡ GIGANTIC SPEEDRUN RACER ⚡</title>
<style>
  body { margin:0; background:#000; overflow:hidden; font-family:Arial; }
  canvas { display:block; }
  #start, #over {
    position:fixed; inset:0; background:rgba(0,0,0,0.98); color:#0ff;
    display:flex; flex-direction:column; justify-content:center; align-items:center;
    text-align:center; z-index:999;
  }
  button {
    margin-top:30px; padding:20px 60px; font-size:40px; background:#000;
    color:#0ff; border:5px solid #0ff; cursor:pointer; box-shadow:0 0 30px #0ff;
  }
  button:hover { background:#0ff; color:#000; }
  #ui {
    position:fixed; top:10px; left:10px; color:#0f0; font:bold 24px Courier;
    text-shadow:0 0 10px #0f0; z-index:10;
  }
</style>
</head>
<body>

<div id="start">
  <h1>⚡ GIGANTIC RACER ⚡</h1>
  <p style="font-size:30px">Best Ever Speedrun Game</p>
  <button onclick="startGame()">START NOW</button>
  <p style="margin-top:50px; font-size:20px">
    ← → = Move &nbsp;&nbsp; SPACE or W = Nitro Boost<br>
    Collect coins • Avoid cars • Survive as long as possible!
  </p>
</div>

<div id="over" style="display:none">
  <h1>CRASHED!</h1>
  <h2>Time: <span id="finalTime">00.00</span>s</h2>
  <h2>Coins: <span id="finalCoins">0</span></h2>
  <button onclick="startGame()">PLAY AGAIN</button>
</div>

<div id="ui">
  Time: <span id="timer">0.00</span>s | 
  Speed: <span id="speed">0</span> | 
  Nitro: <span id="nitro">100</span>% | 
  Coins: <span id="coins">0</span>
</div>

<canvas id="c"></canvas>

<script>
// ========= 100% WORKING CLEAN VERSION =========
const canvas = document.getElementById('c');
const ctx = canvas.getContext('2d');
let w = canvas.width = innerWidth;
let h = canvas.height = innerHeight;

let running = false;
let time = 0, coins = 0, speed = 0, nitro = 100;
let playerX = w/2;
let cars = [], particles = [], roadOffset = 0;

const keys = {};
onkeydown = e => keys[e.key.toLowerCase()] = true;
onkeyup = e => keys[e.key.toLowerCase()] = false;

function startGame() {
  document.getElementById('start').style.display = 'none';
  document.getElementById('over').style.display = 'none';
  running = true;
  time = 0; coins = 0; nitro = 100; speed = 10;
  playerX = w/2;
  cars = []; particles = []; roadOffset = 0;
  requestAnimationFrame(loop);
}

function crash() {
  running = false;
  document.getElementById('finalTime').textContent = time.toFixed(2);
  document.getElementById('finalCoins').textContent = coins;
  document.getElementById('over').style.display = 'flex';
}

// Spawn traffic + coins
setInterval(() => {
  if (!running) return;
  let lane = 150 + Math.floor(Math.random() * 6) * 120;
  cars.push({ x: lane + Math.random()*40-20, y: -150 });
  if (Math.random() < 0.5) {
    cars.push({ x: lane + Math.random()*80-40, y: -100, coin: true });
  }
}, 800);

function loop() {
  if (!running) return;

  time += 1/60;
  document.getElementById('timer').textContent = time.toFixed(2);

  // Speed increase
  speed = 10 + time * 0.3;

  // Controls
  if (keys['arrowleft'] || keys['a']) playerX -= speed * 1.2;
  if (keys['arrowright'] || keys['d']) playerX += speed * 1.2;
  playerX = Math.max(100, Math.min(w-100, playerX));

  // Nitro
  if ((keys[' '] || keys['w']) && nitro > 0) {
    speed *= 2.2;
    nitro -= 1;
    for(let i=0; i<10; i++) {
      particles.push({
        x: playerX + Math.random()*40-20,
        y: h-100,
        vx: Math.random()*10-5,
        vy: -Math.random()*20-10,
        life: 1
      });
    }
  } else {
    nitro = Math.min(100, nitro + 0.4);
  }

  document.getElementById('nitro').textContent = Math.floor(nitro);
  document.getElementById('speed').textContent = Math.floor(speed*20);
  document.getElementById('coins').textContent = coins;

  // Road scroll
  roadOffset += speed;
  if (roadOffset > 400) roadOffset -= 400;

  // Draw background
  ctx.fillStyle = '#111';
  ctx.fillRect(0, 0, w, h);

  // Road lines
  ctx.strokeStyle = '#fff';
  ctx.lineWidth = 8;
  ctx.setLineDash([50, 50]);
  ctx.lineDashOffset = -roadOffset;
  for (let i = 1; i < 7; i++) {
    ctx.beginPath();
    ctx.moveTo(100 + i*120, 0);
    ctx.lineTo(100 + i*120, h);
    ctx.stroke();
  }
  ctx.setLineDash([]);

  // Traffic & coins
  for (let i = cars.length-1; i >= 0; i--) {
    let car = cars[i];
    car.y += speed * 0.9;

    if (car.coin) {
      ctx.fillStyle = '#ff0';
      ctx.fillRect(car.x-25, car.y-25, 50, 50);
      if (Math.hypot(car.x - playerX, car.y - (h-150)) < 60) {
        coins++;
        cars.splice(i, 1);
        continue;
      }
    } else {
      ctx.fillStyle = '#f33';
      ctx.fillRect(car.x-40, car.y-80, 80, 160);
      ctx.fillStyle = '#000';
      ctx.fillRect(car.x-30, car.y-70, 60, 100);
    }

    // Collision
    if (!car.coin && Math.hypot(car.x - playerX, car.y - (h-150)) < 90) {
      for(let j=0; j<100; j++) {
        particles.push({
          x: playerX,
          y: h-150,
          vx: Math.random()*40-20,
          vy: Math.random()*40-20,
          life: 1
        });
      }
      crash();
      return;
    }

    if (car.y > h+200) cars.splice(i, 1);
  }

  // Player car
  ctx.fillStyle = '#0ff';
  ctx.shadowBlur = 50;
  ctx.shadowColor = '#0ff';
  ctx.fillRect(playerX-40, h-230, 80, 160);
  ctx.fillStyle = '#000';
  ctx.fillRect(playerX-30, h-220, 60, 120);
  ctx.shadowBlur = 0;

  // Particles
  for (let i = particles.length-1; i >= 0; i--) {
    let p = particles[i];
    p.x += p.vx;
    p.y += p.vy;
    p.life -= 0.02;
    ctx.globalAlpha = p.life;
    ctx.fillStyle = '#0ff';
    ctx.fillRect(p.x-5, p.y-5, 10, 10);
    if (p.life <= 0) particles.splice(i, 1);
  }
  ctx.globalAlpha = 1;

  requestAnimationFrame(loop);
}

onresize = () => {
  w = canvas.width = innerWidth;
  h = canvas.height = innerHeight;
};
</script>
</body>
</html>
