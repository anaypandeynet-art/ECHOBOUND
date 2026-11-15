// DOM Node Architecture
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const container = document.getElementById('canvas-container');
const menuOverlay = document.getElementById('menu-overlay');
const victoryOverlay = document.getElementById('victory-overlay');
const msgOverlay = document.getElementById('msg-overlay');
const msgTitle = document.getElementById('msg-title');
const msgBody = document.getElementById('msg-body');
const terminalText = document.getElementById('terminal-text');
const hudShards = document.getElementById('hud-shards');
const hudEchoes = document.getElementById('hud-echoes');
const hudStatus = document.getElementById('hud-status');

// UI Buttons
const btnStart = document.getElementById('btn-start');
const btnRestart = document.getElementById('btn-restart');
const btnMobileEcho = document.getElementById('btn-mobile-echo');

// Game Matrix & Configuration
// 0 = Empty Path, 1 = Wall Structure, 2 = Fragment Shard, 3 = Final Escape Gate
const GRID_SIZE = 15;
const GAME_MAP = [
  [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
  [1,0,0,0,1,0,0,0,0,0,1,0,0,2,1],
  [1,0,1,0,1,0,1,1,1,0,1,0,1,1,1],
  [1,0,1,0,0,0,0,0,1,0,0,0,0,0,1],
  [1,0,1,1,1,1,1,0,1,1,1,1,1,0,1],
  [1,0,0,0,0,0,1,0,0,0,1,0,0,0,1],
  [1,1,1,0,1,0,1,1,1,0,1,0,1,1,1],
  [1,2,1,0,1,0,0,3,0,0,1,0,1,0,1],
  [1,0,1,0,1,1,1,1,1,1,1,0,1,0,1],
  [1,0,0,0,1,0,0,0,0,0,0,0,0,0,1],
  [1,0,1,1,1,0,1,1,1,1,1,1,1,0,1],
  [1,0,1,0,0,0,1,2,0,0,0,0,1,0,1],
  [1,0,1,0,1,1,1,1,1,1,1,0,1,0,1],
  [1,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
  [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
];

// Dynamic Tracking Coordinates and Visibilities
let cellVisibility = Array(GRID_SIZE).fill().map(() => Array(GRID_SIZE).fill(0));
let mapObjects = []; 
let player = { x: 1, y: 1, targetX: 1, targetY: 1, animProgress: 1 };
let echoes = [];
let particles = [];
let shardsCollected = 0;
let gameState = 'MENU'; // MENU, PLAYING, DIALOGUE, ESCAPED
let msgTimeout = null;

// Narrative Records
const narrativeTimeline = [
  { trigger: 'start', text: "SYSTEM STATUS: Echo Module synchronized. Vision field collapsed to zero percent. Rely on acoustic feedback vectors to locate exit parameters." },
  { trigger: 'shard_1', title: "LOG LINK RECOVERED [1/3]", text: "Entry #408: 'The absolute dark is breathing. The geometric layout alters whenever I stay motionless. Must keep pinging the surroundings...'" },
  { trigger: 'shard_2', title: "LOG LINK RECOVERED [2/3]", text: "Entry #512: 'The sound pulses are reflecting off dense spatial walls. Three resonance anchors power the main escape nexus. Found two.'" },
  { trigger: 'shard_3', title: "LOG LINK RECOVERED [3/3]", text: "Entry #771: 'All anchors verified! The extraction portal in the absolute center of the sector is online. I hear the frequency tearing open!'" },
  { trigger: 'gate_locked', title: "INTERLOCK WARNING", text: "The primary escape vector is offline. Collect all 3 structural shards to unlock the core frequencies." }
];

// Responsive Sizing Architecture
function resizeCanvas() {
  const size = Math.min(container.offsetWidth, container.offsetHeight, 540);
  canvas.width = size;
  canvas.height = size;
}
window.addEventListener('resize', resizeCanvas);
resizeCanvas();

// Game Core Initialization
function initGame() {
  shardsCollected = 0;
  echoes = [];
  particles = [];
  player = { x: 1, y: 1, targetX: 1, targetY: 1, animProgress: 1 };
  
  // Re-populate map objects from standard layout template
  mapObjects = [];
  for (let r = 0; r < GRID_SIZE; r++) {
    for (let c = 0; c < GRID_SIZE; c++) {
      cellVisibility[r][c] = 0.05; // Base faint awareness
      if (GAME_MAP[r][c] === 2) {
        mapObjects.push({ type: 'shard', r: r, c: c, active: true });
      } else if (GAME_MAP[r][c] === 3) {
        mapObjects.push({ type: 'gate', r: r, c: c });
      }
    }
  }
  
  hudShards.innerText = "0 / 3";
  hudStatus.innerText = "BOUND";
  hudStatus.className = "text-amber-400 font-bold";
  
  terminalText.innerHTML = '';
  appendTerminalFeed(narrativeTimeline[0].text);
  
  // Illuminate spawn room surroundings
  triggerEchoPulse(1, 1, 120);
}

// Terminal Logging Console helper
function appendTerminalFeed(msg) {
  const p = document.createElement('p');
  p.className = "border-l-2 border-cyan-500 pl-2 py-0.5 animate-fadeIn";
  p.innerHTML = `<span class="text-slate-500">[${new Date().toLocaleTimeString()}]</span> ${msg}`;
  terminalText.appendChild(p);
  terminalText.scrollTop = terminalText.scrollHeight;
}

// Modal Notification HUD System
function showNotification(title, text) {
  if (msgTimeout) clearTimeout(msgTimeout);
  msgTitle.innerText = title;
  msgBody.innerText = text;
  msgOverlay.className = "absolute bottom-6 left-6 right-6 bg-slate-900/95 border border-cyan-500 rounded-lg p-4 transform translate-y-0 opacity-100 transition-all duration-300 z-30 pointer-events-none glow-cyan";
  
  msgTimeout = setTimeout(() => {
    msgOverlay.className = "absolute bottom-6 left-6 right-6 bg-slate-900/95 border border-cyan-500 rounded-lg p-4 transform translate-y-24 opacity-0 transition-all duration-500 z-30 pointer-events-none";
  }, 5000);
}

// Sound Echolocation Wave Dispatcher
function triggerEchoPulse(gridX, gridY, customRadius = 250) {
  const cellSize = canvas.width / GRID_SIZE;
  const startX = (gridX + 0.5) * cellSize;
  const startY = (gridY + 0.5) * cellSize;
  
  echoes.push({
    x: startX,
    y: startY,
    currentRadius: 10,
    maxRadius: customRadius,
    speed: 4.5,
    opacity: 1.0
  });

  hudEchoes.innerText = "CHARGING";
  hudEchoes.className = "text-magenta-400 font-bold";
  setTimeout(() => {
    if (gameState === 'PLAYING') {
      hudEchoes.innerText = "READY";
      hudEchoes.className = "text-cyan-400 font-bold";
    }
  }, 800);
}

// Particle Burst Engine
function spawnExplosionParticles(x, y, color) {
  for (let i = 0; i < 15; i++) {
    particles.push({
      x: x,
      y: y,
      vx: (Math.random() - 0.5) * 4,
      vy: (Math.random() - 0.5) * 4,
      radius: Math.random() * 2 + 1,
      life: 1.0,
      decay: Math.random() * 0.03 + 0.02,
      color: color
    });
  }
}

// Linear Input Handling Intersect Validation
function requestMove(dx, dy) {
  if (gameState !== 'PLAYING') return;
  if (player.animProgress < 1) return; // Wait for slide animation

  let targetX = player.x + dx;
  let targetY = player.y + dy;

  if (targetX >= 0 && targetX < GRID_SIZE && targetY >= 0 && targetY < GRID_SIZE) {
    if (GAME_MAP[targetY][targetX] !== 1) {
      player.targetX = targetX;
      player.targetY = targetY;
      player.animProgress = 0;
      
      // Slight automatic continuous tracking revelation
      cellVisibility[targetY][targetX] = Math.max(cellVisibility[targetY][targetX], 0.4);
    } else {
      // Hit wall feedback ripple
      const cellSize = canvas.width / GRID_SIZE;
      const hitX = (targetX + 0.5) * cellSize;
      const hitY = (targetY + 0.5) * cellSize;
      spawnExplosionParticles(hitX, hitY, '#ef4444');
      cellVisibility[targetY][targetX] = 1.0; 
    }
  }
}

// Event Registration - Keyboard Map Mechanics
window.addEventListener('keydown', (e) => {
  if (gameState !== 'PLAYING') return;
  
  switch (e.key.toLowerCase()) {
    case 'w': case 'arrowup':    requestMove(0, -1); e.preventDefault(); break;
    case 's': case 'arrowdown':  requestMove(0, 1);  e.preventDefault(); break;
    case 'a': case 'arrowleft':  requestMove(-1, 0); e.preventDefault(); break;
    case 'd': case 'arrowright': requestMove(1, 0);  e.preventDefault(); break;
    case ' ': 
      if (hudEchoes.innerText === "READY") {
        triggerEchoPulse(player.x, player.y);
      }
      e.preventDefault();
      break;
  }
});

// Mobile Virtual Interface Binding
document.getElementById('pad-up').addEventListener('click', () => requestMove(0, -1));
document.getElementById('pad-down').addEventListener('click', () => requestMove(0, 1));
document.getElementById('pad-left').addEventListener('click', () => requestMove(-1, 0));
document.getElementById('pad-right').addEventListener('click', () => requestMove(1, 0));

const triggerMobileEcho = () => {
  if (gameState === 'PLAYING' && hudEchoes.innerText === "READY") {
    triggerEchoPulse(player.x, player.y);
  }
};
btnMobileEcho.addEventListener('click', triggerMobileEcho);

// Frame Engine Logic Updates
function updateGameEngine() {
  const cellSize = canvas.width / GRID_SIZE;

  // Handle Smooth Player Translation
  if (player.animProgress < 1) {
    player.animProgress += 0.15;
    if (player.animProgress >= 1) {
      player.animProgress = 1;
      player.x = player.targetX;
      player.y = player.targetY;
      
      // Post-step interactive intersection calculations
      evaluateTileIntersections();
    }
  }

  // Smooth decay of environmental visual memory (Fog)
  for (let r = 0; r < GRID_SIZE; r++) {
    for (let c = 0; c < GRID_SIZE; c++) {
      if (cellVisibility[r][c] > 0.05) {
        cellVisibility[r][c] -= 0.006; // Slow illumination fade
        if (cellVisibility[r][c] < 0.05) cellVisibility[r][c] = 0.05;
      }
    }
  }

  // Track expand acoustic ripples
  for (let i = echoes.length - 1; i >= 0; i--) {
    let echo = echoes[i];
    echo.currentRadius += echo.speed;
    echo.opacity = 1.0 - (echo.currentRadius / echo.maxRadius);

    // Light up map blocks intersecting the expanding shockwave circle perimeter
    for (let r = 0; r < GRID_SIZE; r++) {
      for (let c = 0; c < GRID_SIZE; c++) {
        let blockCenterX = (c + 0.5) * cellSize;
        let blockCenterY = (r + 0.5) * cellSize;
        
        let dist = Math.hypot(blockCenterX - echo.x, blockCenterY - echo.y);
        
        if (Math.abs(dist - echo.currentRadius) < (cellSize * 0.8)) {
          // Mathematical falloff intensity modulation
          let brightness = echo.opacity * 1.3;
          if (brightness > cellVisibility[r][c]) {
            cellVisibility[r][c] = Math.min(brightness, 1.0);
          }
        }
      }
    }

    if (echo.currentRadius >= echo.maxRadius) {
      echoes.splice(i, 1);
    }
  }

  // Update Particle dynamics
  for (let i = particles.length - 1; i >= 0; i--) {
    let p = particles[i];
    p.x += p.vx;
    p.y += p.vy;
    p.life -= p.decay;
    if (p.life <= 0) {
      particles.splice(i, 1);
    }
  }
}

// Process overlapping entity vectors
function evaluateTileIntersections() {
  const cellSize = canvas.width / GRID_SIZE;
  const currentCenterX = (player.x + 0.5) * cellSize;
  const currentCenterY = (player.y + 0.5) * cellSize;

  mapObjects.forEach(obj => {
    if (obj.type === 'shard' && obj.active && obj.r === player.y && obj.c === player.x) {
      obj.active = false;
      shardsCollected++;
      hudShards.innerText = `${shardsCollected} / 3`;
      
      spawnExplosionParticles(currentCenterX, currentCenterY, '#f59e0b');
      
      let logData = narrativeTimeline.find(n => n.trigger === `shard_${shardsCollected}`);
      if (logData) {
        appendTerminalFeed(logData.text);
        showNotification(logData.title, logData.text);
      }

      if (shardsCollected === 3) {
        hudStatus.innerText = "UNBOUND";
        hudStatus.className = "text-emerald-400 font-bold animate-pulse";
        appendTerminalFeed("SYSTEM CRITICAL CRACK: Gateway frequencies matched. Interlock system open.");
      }
    }

    if (obj.type === 'gate' && obj.r === player.y && obj.c === player.x) {
      if (shardsCollected >= 3) {
        gameState = 'ESCAPED';
        victoryOverlay.classList.remove('hidden');
      } else {
        let lockData = narrativeTimeline.find(n => n.trigger === 'gate_locked');
        showNotification(lockData.title, lockData.text);
        appendTerminalFeed("ACCESS DENIED: Insufficient core shard telemetry blocks detected.");
        // Repel player backward gently
        player.x = 7; player.y = 8;
        player.targetX = 7; player.targetY = 8;
      }
    }
  });
}

// Graphic Layer Layout Painter
function renderGraphicsEngine() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  const cellSize = canvas.width / GRID_SIZE;

  // Layer 1: Render structural configuration coordinates
  for (let r = 0; r < GRID_SIZE; r++) {
    for (let c = 0; c < GRID_SIZE; c++) {
      let alpha = cellVisibility[r][c];
      let blockType = GAME_MAP[r][c];
      
      if (alpha > 0) {
        if (blockType === 1) { // Drawing Wall Geometry
          ctx.fillStyle = `rgba(14, 116, 144, ${alpha * 0.35})`;
          ctx.fillRect(c * cellSize + 1, r * cellSize + 1, cellSize - 2, cellSize - 2);
          
          ctx.strokeStyle = `rgba(34, 211, 238, ${alpha})`;
          ctx.lineWidth = 1.5;
          ctx.strokeRect(c * cellSize + 1, r * cellSize + 1, cellSize - 2, cellSize - 2);
        } else { // Floor Grid references
          ctx.strokeStyle = `rgba(30, 41, 59, ${alpha * 0.25})`;
          ctx.lineWidth = 0.5;
          ctx.strokeRect(c * cellSize, r * cellSize, cellSize, cellSize);
        }
      }
    }
  }

  // Layer 2: Render Interactive Level Entites (Shards / Nexus Gates)
  mapObjects.forEach(obj => {
    let alpha = cellVisibility[obj.r][obj.c];
    let x = (obj.c + 0.5) * cellSize;
    let y = (obj.r + 0.5) * cellSize;
    
    if (alpha > 0.05) {
      if (obj.type === 'shard' && obj.active) {
        // Draw Shard Mesh geometry
        ctx.save();
        ctx.shadowBlur = 10 * alpha;
        ctx.shadowColor = '#f59e0b';
        ctx.fillStyle = `rgba(245, 158, 11, ${alpha * 0.5 + 0.5})`;
        
        ctx.beginPath();
        ctx.moveTo(x, y - cellSize * 0.3);
        ctx.lineTo(x + cellSize * 0.2, y);
        ctx.lineTo(x, y + cellSize * 0.3);
        ctx.lineTo(x - cellSize * 0.2, y);
        ctx.closePath();
        ctx.fill();
        ctx.restore();
      } else if (obj.type === 'gate') {
        // Unbound Nexus Gate Drawing
        ctx.save();
        ctx.shadowBlur = 15 * alpha;
        ctx.shadowColor = shardsCollected >= 3 ? '#10b981' : '#ef4444';
        
        ctx.strokeStyle = shardsCollected >= 3 ? `rgba(16, 185, 129, ${alpha})` : `rgba(239, 68, 68, ${alpha})`;
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.arc(x, y, cellSize * 0.35, 0, Math.PI * 2);
        ctx.stroke();
        
        // Core spinning sync ring simulation
        ctx.strokeStyle = `rgba(255, 255, 255, ${alpha * 0.4})`;
        ctx.lineWidth = 1;
        ctx.setLineDash([4, 4]);
        ctx.beginPath();
        ctx.arc(x, y, cellSize * 0.2, Date.now() * 0.003, Date.now() * 0.003 + Math.PI * 2);
        ctx.stroke();
        ctx.restore();
      }
    }
  });

  // Layer 3: Render Player Node Vector position
  let renderX = (player.x + (player.targetX - player.x) * player.animProgress + 0.5) * cellSize;
  let renderY = (player.y + (player.targetY - player.y) * player.animProgress + 0.5) * cellSize;
  
  ctx.save();
  ctx.shadowBlur = 14;
  ctx.shadowColor = '#d946ef';
  ctx.fillStyle = '#ec4899';
  ctx.beginPath();
  ctx.arc(renderX, renderY, cellSize * 0.25, 0, Math.PI * 2);
  ctx.fill();
  
  // Secondary directional core locator ring
  ctx.strokeStyle = '#ffffff';
  ctx.lineWidth = 1.5;
  ctx.beginPath();
  ctx.arc(renderX, renderY, cellSize * 0.12, 0, Math.PI * 2);
  ctx.stroke();
  ctx.restore();

  // Layer 4: Render active sound expanding wavefront profiles
  echoes.forEach(echo => {
    ctx.save();
    ctx.strokeStyle = `rgba(6, 182, 212, ${echo.opacity})`;
    ctx.lineWidth = 2.5;
    ctx.shadowBlur = 8 * echo.opacity;
    ctx.shadowColor = '#06b6d4';
    ctx.beginPath();
    ctx.arc(echo.x, echo.y, echo.currentRadius, 0, Math.PI * 2);
    ctx.stroke();
    ctx.restore();
  });

  // Layer 5: Render Spark Particles
  particles.forEach(p => {
    ctx.fillStyle = p.color;
    ctx.globalAlpha = p.life;
    ctx.beginPath();
    ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
    ctx.fill();
  });
  ctx.globalAlpha = 1.0; // Reset canvas context state integrity global profile
}

// Master System Loop Loop Iteration Engine
function loop() {
  if (gameState === 'PLAYING' || gameState === 'DIALOUGE') {
    updateGameEngine();
  }
  renderGraphicsEngine();
  requestAnimationFrame(loop);
}

// UI Click Event Bindings
btnStart.addEventListener('click', () => {
  menuOverlay.classList.add('hidden');
  gameState = 'PLAYING';
  initGame();
});

btnRestart.addEventListener('click', () => {
  victoryOverlay.classList.add('hidden');
  gameState = 'PLAYING';
  initGame();
});

// Start Loop Profile Rendering Sequence Immediately
requestAnimationFrame(loop);
