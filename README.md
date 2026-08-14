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
      transition: transform 0.3s ease, box-shadow 0.3s ease, opacity 0.3s ease;
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
      cursor: pointer;
    }

    #realFlowers {
      position: absolute;
      width: 85%;
      height: 85%;
      opacity: 0;
      transform: scale(0.7);
      transition: opacity 1.2s ease, transform 1.2s cubic-bezier(0.175, 0.885, 0.32, 1.27);
      z-index: 1;
    }

    #petalCanvas {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 2;
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

      <!-- PAGE 4: FLOWERS (MIC & TOUCH INTERACTIVE) -->
      <div class="page page-flowers" id="page4">
        <h3 style="color: var(--accent-color); margin-bottom: 5px;">Make a Wish</h3>
        <div class="flower-canvas-container" id="flowerContainer">
          
          <!-- Vector Flowers Revealed Beneath -->
          <svg id="realFlowers" viewBox="0 0 200 200">
            <g transform="translate(100,100)">
              <!-- Stem & Leaves -->
              <path d="M0,20 Q-10,60 0,90" stroke="#4CAF50" stroke-width="5" fill="none" stroke-linecap="round"/>
              <path d="M-5,50 Q-25,40 -20,30 Q-5,35 -5,50" fill="#4CAF50"/>
              <path d="M2,65 Q22,55 17,45 Q2,50 2,65" fill="#4CAF50"/>
              <!-- Inner Bloomed Flower -->
              <circle cx="0" cy="-25" r="16" fill="#FF8A80" />
              <circle cx="25" cy="0" r="16" fill="#FF8A80" />
              <circle cx="0" cy="25" r="16" fill="#FF8A80" />
              <circle cx="-25" cy="0" r="16" fill="#FF8A80" />
              <circle cx="-18" cy="-18" r="16" fill="#FF5252" />
              <circle cx="18" cy="-18" r="16" fill="#FF5252" />
              <circle cx="18" cy="18" r="16" fill="#FF5252" />
              <circle cx="-18" cy="18" r="16" fill="#FF5252" />
              <!-- Center -->
              <circle cx="0" cy="0" r="14" fill="#FFD54F" />
            </g>
          </svg>

          <!-- Interactive Flower Heads / Petals Canvas -->
          <canvas id="petalCanvas"></canvas>
        </div>
        <div class="mic-instruction" id="micText">🎙️ Blow into your mic (or tap) to scatter the flowers!</div>
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

    /* Page 4: Interactive Flower Canvas & Dynamic Mic Blow Logic */
    const canvas = document.getElementById('petalCanvas');
    const ctx = canvas.getContext('2d');
    const flowerContainer = document.getElementById('flowerContainer');
    let width, height;
    let petals = [];
    let currentBlowStrength = 0;
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
        // Start petals clustered around the center like flower heads
        const angle = Math.random() * Math.PI * 2;
        const radius = Math.random() * 45;
        this.x = width / 2 + Math.cos(angle) * radius;
        this.y = height / 2 + Math.sin(angle) * radius;
        
        this.size = Math.random() * 10 + 8;
        this.color = ['#ff8a80', '#ff5252', '#ffb7b2', '#ffdac1', '#e2f0cb'][Math.floor(Math.random() * 5)];
        
        // Blow directional physics outwards from the center
        const dirAngle = Math.atan2(this.y - height / 2, this.x - width / 2);
        this.baseVx = Math.cos(dirAngle) * (Math.random() * 2 + 1);
        this.baseVy = Math.sin(dirAngle) * (Math.random() * 2 + 1) - 1; // Slight upward drift
        
        this.vx = 0;
        this.vy = 0;
        this.alpha = 1;
        this.isBlown = false;
      }

      update(blowIntensity) {
        if (blowIntensity > 0.1) {
          this.isBlown = true;
          // Dynamically scale speed based on how hard you blow into the microphone
          this.vx += this.baseVx * blowIntensity * 1.5;
          this.vy += this.baseVy * blowIntensity * 1.5;
        }

        if (this.isBlown) {
          this.x += this.vx;
          this.y += this.vy;
          this.alpha -= 0.015;
        }
      }

      draw() {
        if (this.alpha <= 0) return;
        ctx.save();
        ctx.globalAlpha = this.alpha;
        ctx.fillStyle = this.color;
        ctx.beginPath();
        // Draw petal/flower head shapes
        ctx.ellipse(this.x, this.y, this.size, this.size * 0.7, Math.PI / 4, 0, Math.PI * 2);
        ctx.fill();
        ctx.restore();
      }
    }

    function initPetals() {
      resizeCanvas();
      petals = [];
      for (let i = 0; i < 70; i++) {
        petals.push(new Petal());
      }
    }

    function renderPetals() {
      ctx.clearRect(0, 0, width, height);
      let remainingPetals = 0;

      petals.forEach(petal => {
        petal.update(currentBlowStrength);
        petal.draw();
        if (petal.alpha > 0) remainingPetals++;
      });

      // Gradually decay blow intensity frame by frame
      if (currentBlowStrength > 0) {
        currentBlowStrength *= 0.92;
      }

      // Reveal the underlying bloomed vector flowers when enough petals blow away
      if (remainingPetals < 45) {
        const flowers = document.getElementById('realFlowers');
        if (flowers) {
          flowers.style.opacity = '1';
          flowers.style.transform = 'scale(1)';
        }
        document.getElementById('micText').innerText = "🌸 Happy Birthday! 🌸";
      }

      requestAnimationFrame(renderPetals);
    }

    // Touch/Click Fallback to manually trigger a blow effect
    flowerContainer.addEventListener('click', () => {
      currentBlowStrength = 0.8;
    });

    /* Web Audio API - Detect Audio Level from Phone/Desktop Mic */
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

        function checkMicLevel() {
          analyser.getByteFrequencyData(dataArray);
          let sum = 0;
          for (let i = 0; i < dataArray.length; i++) {
            sum += dataArray[i];
          }
          let average = sum / dataArray.length;

          // Sound threshold: when blowing noise is detected, set blow strength proportionally
          if (average > 25) {
            currentBlowStrength = Math.min(1.0, (average - 25) / 40);
          }

          requestAnimationFrame(checkMicLevel);
        }

        checkMicLevel();
      } catch (err) {
        document.getElementById('micText').innerText = "Tap the flowers to clear them! 🌸";
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
