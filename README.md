# eggcracker
its a website game about cracking an egg and yolk comes out
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Egg Crack Game</title>
  <style>
    body {
      background: linear-gradient(180deg, #cceaff, #e6f7ff);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      overflow: hidden;
      font-family: Arial, sans-serif;
    }

    h1 {
      position: absolute;
      top: 40px;
      font-size: 2.5rem;
      text-align: center;
      color: #333;
    }

    canvas {
      background: none;
      border-radius: 50%;
      touch-action: none;
    }

    button {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 1.2rem;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      background: #ffcc00;
      transition: background 0.3s;
    }

    button:hover {
      background: #ffdb4d;
    }
  </style>
</head>
<body>
  <h1 id="counter">Taps: 0</h1>
  <canvas id="eggCanvas" width="300" height="400"></canvas>
  <button id="resetBtn">Reset</button>

  <script>
    const canvas = document.getElementById('eggCanvas');
    const ctx = canvas.getContext('2d');
    const counter = document.getElementById('counter');
    const resetBtn = document.getElementById('resetBtn');

    let taps = 0;
    let cracks = [];
    let cracked = false;

    function drawEgg() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw egg shape
      ctx.fillStyle = '#fffaf0';
      ctx.beginPath();
      ctx.ellipse(150, 200, 80, 110, 0, 0, Math.PI * 2);
      ctx.fill();
      ctx.closePath();

      // Draw cracks
      if (!cracked) {
        ctx.save();
        ctx.clip();
        ctx.strokeStyle = '#555';
        ctx.lineWidth = 2;
        cracks.forEach(line => {
          ctx.beginPath();
          ctx.moveTo(line.x1, line.y1);
          ctx.lineTo(line.x2, line.y2);
          ctx.stroke();
        });
        ctx.restore();
      }
    }

    function addCrack() {
      const angle = Math.random() * Math.PI * 2;
      const radius = Math.random() * 70;
      const cx = 150 + radius * Math.cos(angle);
      const cy = 200 + radius * Math.sin(angle);
      const length = Math.random() * 20 + 10;
      cracks.push({
        x1: cx,
        y1: cy,
        x2: cx + Math.cos(angle) * length,
        y2: cy + Math.sin(angle) * length
      });
    }

    function yolkSplash() {
      const particles = [];
      for (let i = 0; i < 50; i++) {
        particles.push({
          x: 150,
          y: 200,
          radius: Math.random() * 10 + 5,
          color: `rgba(255, ${200 + Math.random() * 55}, 0, 1)`,
          dx: (Math.random() - 0.5) * 8,
          dy: (Math.random() - 0.5) * 8,
          life: 60 + Math.random() * 30
        });
      }

      function animate() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#fffaf0';
        ctx.beginPath();
        ctx.ellipse(150, 200, 80, 110, 0, 0, Math.PI * 2);
        ctx.fill();
        ctx.closePath();

        for (let p of particles) {
          ctx.beginPath();
          ctx.fillStyle = p.color;
          ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
          ctx.fill();
          p.x += p.dx;
          p.y += p.dy;
          p.dy += 0.2;
          p.life--;
        }

        for (let i = particles.length - 1; i >= 0; i--) {
          if (particles[i].life <= 0) particles.splice(i, 1);
        }

        if (particles.length > 0) {
          requestAnimationFrame(animate);
        } else {
          drawEgg();
        }
      }

      animate();
    }

    function handleTap() {
      if (cracked) return;

      taps++;
      counter.textContent = `Taps: ${taps}`;

      addCrack();
      drawEgg();

      if (taps >= 70) {
        cracked = true;
        cracks = [];
        drawEgg();
        yolkSplash();
      }
    }

    resetBtn.addEventListener('click', () => {
      taps = 0;
      cracks = [];
      cracked = false;
      counter.textContent = 'Taps: 0';
      drawEgg();
    });

    canvas.addEventListener('pointerdown', e => {
      e.preventDefault();
      handleTap();
    });

    drawEgg();
  </script>
</body>
</html>
