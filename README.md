<!-- PAGE 4: FLOWERS (MIC & TOUCH INTERACTIVE) -->
<div class="page page-flowers" id="page4">
  <h3 style="color: var(--accent-color); margin-bottom: 5px;">Make a Wish</h3>
  <div class="flower-canvas-container" id="flowerContainer" style="cursor: pointer;">
    
    <!-- SVG Flowers (Reliable, no external network link required) -->
    <svg id="realFlowers" viewBox="0 0 200 200" width="80%" height="80%" style="position: absolute; opacity: 0; transform: scale(0.8); transition: all 1.2s ease;">
      <g transform="translate(100,100)">
        <!-- Stem & Leaves -->
        <path d="M0,20 Q-10,60 0,90" stroke="#4CAF50" stroke-width="4" fill="none"/>
        <path d="M-5,50 Q-25,40 -20,30 Q-5,35 -5,50" fill="#4CAF50"/>
        <path d="M2,65 Q22,55 17,45 Q2,50 2,65" fill="#4CAF50"/>
        <!-- Flower Petals -->
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

    <!-- Interactive Petal Canvas -->
    <canvas id="petalCanvas"></canvas>
  </div>
  <div class="mic-instruction" id="micText">🎙️ Blow into mic OR tap the flowers to clear petals!</div>
</div>
