<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Interactive 3D Card & Flower Bloom</title>
  <style>
    :root {
      --bg-color: #fbeee0;
      --card-bg: #fffbf0;
      --accent-color: #d96071;
      --gold-color: #d4af37;
      --text-color: #4a3e3d;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      background: linear-gradient(135deg, #fce4ec 0%, #fbeee0 100%);
      font-family: 'Georgia', serif;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      overflow: hidden;
      color: var(--text-color);
    }

    /* Card Perspective Wrapper */
    .card-container {
      width: 320px;
      height: 480px;
      perspective: 1500px;
      position: relative;
    }

    .card {
      width: 100%;
      height: 100%;
      position: relative;
      transform-style: preserve-3d;
      transition: transform 1.2s cubic-bezier(0.4, 0, 0.2, 1);
      box-shadow: 0 15px 35px rgba(0, 0, 0, 0.15);
      border-radius: 12px;
    }

    /* Pages */
    .page {
      position: absolute;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      border-radius: 12px;
      background: var(--card-bg);
      backface-visibility: hidden;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
      border: 1px solid #ebd3c2;
    }

    /* Page 1: Cover */
    .page-cover {
      z-index: 4;
      background: linear-gradient(135deg, #f7d6d0, #fbeee0);
      cursor: pointer;
      text-align: center;
      border: 2px solid var(--gold-color);
    }

    .cover-frame {
      border: 2px dashed var(--accent-color);
      padding: 20px;
      border-radius: 8px;
      width: 90%;
      height: 90%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 15px;
    }

    .cover-title {
      font-size: 1.8rem;
      color: var(--accent-color);
      font-family: 'Playfair Display', serif;
    }

    .cover-subtitle {
      font-size: 0.9rem;
      color: #7a6263;
      margin-top: 10px;
    }

    .flip-btn {
      margin-top: 20px;
      padding: 10px 20px;
      background: var(--accent-color);
      color: white;
      border: none;
      border-radius: 20px;
      font-size: 0.9rem;
      cursor: pointer;
      box-shadow: 0 4px 10px rgba(217, 96, 113, 0.3);
      animation: pulse 2s infinite;
    }

    /* Page 2: Letter with Wax Seal */
    .page-letter {
      z-index: 3;
      text-align: center;
      background: #fdfaf6;
    }

    .wax-seal {
      width: 70px;
      height: 70px;
      background: var(--accent-color);
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: white;
      font-size: 1.8rem;
      cursor: pointer;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
      transition: transform 0.3s ease, box-shadow 0.3s ease;
      position: absolute;
    }

    .wax-seal:active {
      transform: scale(0.9);
    }

    .letter-content {
      opacity: 0;
      transform: translateY(20px);
      transition: all 0.8s ease;
      font-size: 0.95rem;
      line-height: 1.6;
      color: #4a3e3d;
      padding: 10px;
      text-align: left;
    }

    .letter-content.open {
      opacity: 1;
      transform: translateY(0);
    }

    /* Page 3: Memory Photos Gallery */
    .page-memories {
      z-index: 2;
      display: flex;
      flex-direction: column;
      gap: 15px;
    }

    .photo-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      width: 100%;
    }

    .photo-card {
      background: white;
      padding: 8px;
      border-radius: 6px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
      transform: rotate(calc(var(--r) * 1deg));
      animation: float 4s ease-in-out infinite alternate;
      animation-delay: var(--delay);
      text-align: center;
    }

    .photo-card img {
      width: 100%;
      height: 90px;
      object-fit: cover;
      border-radius: 4px;
    }

    .photo-caption {
      font-size: 0.75rem;
      margin-top: 5px;
      color: #666;
    }

    /* Page 4: Interactive Flower Blow Page */
    .page-flowers {
      z-index: 1;
      position: relative;
    }

    .flower-canvas-container {
      position: relative;
      width: 100%;
      height: 80%;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    canvas {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }

    #realFlowers {
      position: absolute;
      width: 80%;
      height: 80%;
      object-fit: contain;
      opacity: 0;
      transform: scale(0.8);
      transition: all 1.5s cubic-bezier(0.175, 0.885, 0.32, 1.27);
    }

    #realFlowers.revealed {
      opacity: 1;
      transform: scale(1);
    }

    .mic-instruction {
      font-size: 0.85rem;
      color: var(--accent-color);
      margin-top: 10px;
      text-align: center;
      font-weight: bold;
    }

    /* Controls for Page Navigation */
    .nav-controls {
      margin-top: 20px;
      display: flex;
      gap: 15px;
      z-index: 10;
    }

    .nav-btn {
      padding: 8px 16px;
      background: white;
      border: 1px solid var(--accent-color);
      color: var(--accent-color);
      border-radius: 20px;
      cursor: pointer;
      font-size: 0.85rem;
      transition: all 0.3s ease;
    }

    .nav-btn:hover {
      background: var(--accent-color);
      color: white;
    }

    /* Keyframe Animations */
    @keyframes pulse {
      0% { transform: scale(1); }
      50% { transform: scale(1.05); }
      100% { transform: scale(1); }
    }

    @keyframes float {
      0% { transform: translateY(0px) rotate(calc(var(--r) * 1deg)); }
      100% { transform: translateY(-6px) rotate(calc(var(--r) * 1deg)); }
    }

    /* Card Rotation Flip Classes */
    .card.turn-p1 { transform: rotateY(0deg); }
    .card.turn-p2 { transform: rotateY(-180deg); }
    .card.turn-p3 { transform: rotateY(-360deg); }
    .card.turn-p4 { transform: rotateY(-540deg); }
  </style>
</head>
<body>

  <div class="card-container">
    <div class="card" id="card">
      
      <!-- PAGE 1: COVER -->
      <div class="page page-cover" id="page1">
        <div class="cover-frame">
          <div style="font-size: 2.5rem;">💌</div>
          <h1 class="cover-title">A Special Note</h1>
          <p class="cover-subtitle">For Someone Wonderful</p>
          <button class="flip-btn" onclick="goToPage(2)">Open Card ➔</button>
        </div>
      </div>

      <!-- PAGE 2: LETTER -->
      <div class="page page-letter" id="page2">
        <div class="wax-seal" id="waxSeal" onclick="openLetter()">✉️</div>
        <div class="letter-content" id="letterContent">
          <h2 style="color: var(--accent-color); margin-bottom: 10px;">Dearest,</h2>
          <p>Every moment shared with you is like a blooming flower in my heart. Thank you for bringing so much light, warmth, and joy into my world.</p>
          <p style="margin-top: 10px;">Flip over to see some of our favorite memories...</p>
          <p style="margin-top: 15px; font-weight: bold; text-align: right;">— Always Yours</p>
        </div>
      </div>

      <!-- PAGE 3: MEMORIES -->
      <div class="page page-memories" id="page3">
        <h3 style="color: var(--accent-color); font-family: 'Playfair Display', serif;">Sweet Memories</h3>
        <div class="photo-grid">
          <div class="photo-card" style="--r: -3; --delay: 0s;">
            <img src="https://picsum.photos/200/200?random=1" alt="Memory 1" />
            <div class="photo-caption">Day Trip ✨</div>
          </div>
          <div class="photo-card" style="--r: 4; --delay: 0.5s;">
            <img src="https://picsum.photos/200/200?random=2" alt="Memory 2" />
            <div class="photo-caption">Smiles 🌸</div>
          </div>
          <div class="photo-card" style="--r: -2; --delay: 1s;">
            <img src="https://picsum.photos/200/200?random=3" alt="Memory 3" />
            <div class="photo-caption">Laughter 💛</div>
          </div>
          <div class="photo-card" style="--r: 3; --delay: 1.5s;">
            <img src="https://picsum.photos/200/200?random=4" alt="Memory 4" />
            <div class="photo-caption">Together 🥂</div>
          </div>
        </div>
      </div>

      <!-- PAGE 4: FLOWERS (MIC INTERACTIVE) -->
      <div class="page page-flowers" id="page4">
        <h3 style="color: var(--accent-color); margin-bottom: 5px;">Make a Wish</h3>
        <div class="flower-canvas-container">
          <!-- Real Flowers (Revealed behind petals) -->
          <img id="realFlowers" src="https://images.unsplash.com/photo-1563241527-3004b7be0ffd?auto=format&fit=crop&w=600&q=80" alt="Real Blooming Flowers" />
          <!-- Interactive Particle Petal Overlay -->
          <canvas id="petalCanvas"></canvas>
        </div>
        <div class="mic-instruction" id="micText">🎙️ Blow into your phone's microphone to clear the petals!</div>
      </div>

    </div>
  </div>

  <!-- Navigation Buttons -->
  <div class="nav-controls">
    <button class="nav-btn" onclick="prevPage()">‹ Prev</button>
    <button class="nav-btn" onclick="nextPage()">Next ›</button>
  </div>

  <script>
    let currentPage = 1;
    const totalPages = 4;
    const card = document.getElementById('card');

    function goToPage(page) {
      currentPage = page;
      card.className = `card turn-p${currentPage}`;

      // Initialize mic on Page 4 automatically
      if (currentPage === 4) {
        initMicrophone();
      }
    }

    function nextPage() {
      if (currentPage < totalPages) goToPage(currentPage + 1);
    }

    function prevPage() {
      if (currentPage > 1) goToPage(currentPage - 1);
    }

    /* Page 2: Wax Seal & Letter Reveal */
    function openLetter() {
      const seal = document.getElementById('waxSeal');
      const letter = document.getElementById('letterContent');
      
      seal.style.transform = 'scale(0) rotate(180deg)';
      seal.style.opacity = '0';
      
      setTimeout(() => {
        seal.style.display = 'none';
        letter.classList.add('open');
      }, 300);
    }

    /* Page 4: Interactive Petal Canvas & Microphone Blow Logic */
    const canvas = document.getElementById('petalCanvas');
    const ctx = canvas.getContext('2d');
    let width, height;
    let petals = [];
    let isBlowing = false;
    let micInitialized = false;

    function resizeCanvas() {
      const rect = canvas.parentElement.getBoundingClientRect();
      canvas.width = width = rect.width;
      canvas.height = height = rect.height;
    }

    class Petal {
      constructor() {
        this.reset();
      }

      reset() {
        this.x = Math.random() * width;
        this.y = Math.random() * height;
        this.size = Math.random() * 8 + 6;
        this.color = ['#ffb7b2', '#ffdac1', '#e2f0cb', '#b5ead7'][Math.floor(Math.random() * 4)];
        this.vx = (Math.random() - 0.5) * 0.5;
        this.vy = (Math.random() - 0.5) * 0.5;
        this.alpha = 1;
        this.blown = false;
      }

      update(intensity) {
        if (intensity > 0.15) {
          this.blown = true;
        }

        if (this.blown) {
          this.vx += (Math.random() + 0.5) * intensity * 5;
          this.vy -= (Math.random() + 0.5) * intensity * 5;
          this.alpha -= 0.015;
        }

        this.x += this.vx;
        this.y += this.vy;
      }

      draw() {
        if (this.alpha <= 0) return;
        ctx.save();
        ctx.globalAlpha = this.alpha;
        ctx.fillStyle = this.color;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      }
    }

    function initPetals() {
      resizeCanvas();
      petals = [];
      for (let i = 0; i < 60; i++) {
        petals.push(new Petal());
      }
    }

    function renderPetals(intensity = 0) {
      ctx.clearRect(0, 0, width, height);
      let remainingPetals = 0;

      petals.forEach(petal => {
        petal.update(intensity);
        petal.draw();
        if (petal.alpha > 0) remainingPetals++;
      });

      // Reveal the real flower photo when petals scatter
      if (remainingPetals < 15) {
        document.getElementById('realFlowers').classList.add('revealed');
        document.getElementById('micText').innerText = "🌸 Beautiful flowers for you! 🌸";
      }

      requestAnimationFrame(() => renderPetals(isBlowing ? 0.3 : 0));
    }

    /* Web Audio API for Microphone Detection */
    async function initMicrophone() {
      if (micInitialized) return;
      
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true, video: false });
        const audioContext = new (window.AudioContext || window.webkitAudioContext)();
        const analyser = audioContext.createAnalyser();
        const microphone = audioContext.createMediaStreamSource(stream);

        analyser.fftSize = 256;
        microphone.connect(analyser);

        const dataArray = new Uint8Array(analyser.frequencyBinCount);
        micInitialized = true;

        function detectBlow() {
          analyser.getByteFrequencyData(dataArray);
          let sum = 0;
          for (let i = 0; i < dataArray.length; i++) {
            sum += dataArray[i];
          }
          let average = sum / dataArray.length;

          // Detect volume/blowing sound threshold
          if (average > 40) {
            isBlowing = true;
            renderPetals(average / 100);
          } else {
            isBlowing = false;
          }

          requestAnimationFrame(detectBlow);
        }

        detectBlow();
      } catch (err) {
        document.getElementById('micText').innerText = "Tap on the petals to blow them away!";
        // Fallback touch interaction if mic permissions are denied
        canvas.addEventListener('click', () => {
          renderPetals(0.5);
        });
      }
    }

    // Window Setup
    window.addEventListener('resize', resizeCanvas);
    window.onload = () => {
      initPetals();
      renderPetals();
    };
  </script>
</body>
</html>
