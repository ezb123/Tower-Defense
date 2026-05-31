# Tower-Defense
A fun strategic game where you defend your towers from waves of enemies by buying and upgrading towers.
<!DOCTYPE html>
<html>

<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>Tower Defense</title>

  <style>
    body {
    margin: 0;
    background: #121212;
    color: #fff;
    font-family: sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    user-select: none;
}

/* 🎬 Overlay & Layering Controls */
.overlay-screen {
    position: fixed;
    top: 0; left: 0; width: 100%; height: 100%;
    background: radial-gradient(circle, #1e272c 0%, #0f1416 100%);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 110;
    transition: opacity 0.2s ease;
}
canvas {
    max-width: 100%;
    max-height: 100vh;
    object-fit: contain; /* Prevents stretching or squishing */
    margin: auto;
    display: block;
}
.game-container {
    aspect-ratio: 16 / 9; /* Change to 9 / 16 for vertical mobile games */
    width: 100%;
    max-width: 800px;    /* Lock the maximum width */
    margin: 0 auto;      /* Center it horizontally */
}

#difficulty-screen {
    background: rgba(12, 12, 12, 0.96);
    z-index: 100;
}

.overlay-screen.hidden {
    display: none !important;
    pointer-events: none !important;
    opacity: 0;
    z-index: -999 !important;
}

#title-box, #difficulty-box, #victory-box {
    background: #1e1e1e;
    padding: 35px 45px;
    border-radius: 12px;
    border: 2px solid #333;
    text-align: center;
    box-shadow: 0 15px 35px rgba(0,0,0,0.8);
}

.game-logo {
    font-size: 50px;
    color: #ffd54f;
    text-shadow: 0 4px 10px rgba(255, 213, 79, 0.25);
    margin: 0 0 5px 0;
}

.game-subtitle {
    font-size: 15px;
    color: #b0bec5;
    margin-bottom: 25px;
}

.highscore-badge {
    background: #2c3e50;
    padding: 8px 20px;
    border-radius: 20px;
    display: inline-block;
    font-weight: bold;
    color: #ffd54f;
    margin-bottom: 30px;
}

/* 🗺️ Title Screen Map Selector */
#map-selection-container {
    margin-bottom: 35px;
    background: #252525;
    padding: 15px;
    border-radius: 8px;
    border: 1px solid #333;
}

#map-selection-container h3 { margin-top: 0; font-size: 16px; color: #bbb; }
.map-row { display: flex; gap: 10px; justify-content: center; }

.map-opt {
    background: #333; color: #fff; border: 2px solid transparent;
    padding: 8px 12px; border-radius: 6px; font-weight: bold; cursor: pointer;
}
.map-opt:hover { background: #444; }
.map-opt.selected { border-color: #2979ff; background: #1c2d3d; }

#start-game-btn {
    font-size: 22px; font-weight: bold; color: #fff; background: #2979ff;
    border: none; padding: 14px 45px; border-radius: 8px; cursor: pointer;
    box-shadow: 0 4px 15px rgba(41, 121, 255, 0.3);
}
#start-game-btn:hover { background: #2962ff; transform: translateY(-1px); }

.diff-btn {
    display: block; width: 320px; padding: 14px; margin: 12px auto;
    font-size: 15px; font-weight: bold; color: white; background: #2c3e50;
    border: none; border-radius: 6px; cursor: pointer; text-align: left;
}
.diff-btn:hover { background: #34495e; }

/* 🎮 Game Core Layout Workspace */
#game-container {
    display: flex; gap: 20px; background: #1a1a1a; padding: 20px;
    border-radius: 12px; border: 2px solid #2d2d2d;
}

#ui {
    margin-bottom: 12px; font-size: 20px; font-weight: bold;
    display: flex; gap: 25px; align-items: center;
}

#health-wrapper { display: flex; align-items: center; gap: 8px; }

#health-bar-container {
    width: 100px; height: 14px; background: #333;
    border-radius: 10px; overflow: hidden; border: 1px solid #444;
}
#health-fill { width: 100%; height: 100%; background: #4caf50; transition: width 0.2s; }
.hud-highscore { color: #ffd54f; font-size: 18px; margin-left: auto; }

canvas { background: #243425; border: 4px solid #333; border-radius: 6px; }

#phase-control { margin-top: 12px; display: flex; justify-content: center; gap: 15px; }
#next-wave-btn, #speed-btn {
    color: white; border: none; padding: 10px 25px;
    font-size: 16px; font-weight: bold; border-radius: 6px; cursor: pointer;
}
#next-wave-btn { background: #2e7d32; }
#next-wave-btn:hover { background: #1b5e20; }
#speed-btn { background: #455a64; }

/* 🛒 Sidebar Context Shopping Panels */
#shop-sidebar {
    width: 250px; height: 635px; background: #222; padding: 15px;
    border-radius: 8px; border: 2px solid #333;
    display: flex; flex-direction: column; overflow-y: auto;
}
#shop-sidebar h2 { margin-top: 0; text-align: center; border-bottom: 2px solid #333; padding-bottom: 8px; }
.shop-hint { font-size: 11px; color: #888; text-align: center; margin-bottom: 12px; }

.shop-item {
    background: #2d2d2d; padding: 8px 12px; margin-bottom: 8px;
    border-radius: 6px; border: 2px solid transparent; cursor: pointer;
}
.shop-item h3 { margin: 0 0 3px 0; font-size: 14px; }
.shop-item p { margin: 0 0 5px 0; font-size: 11px; color: #aaa; }
.shop-item .price { font-weight: bold; color: #ffd54f; font-size: 12px; }
.shop-item.active { border-color: #2979ff; background: #1c2d3d; }

/* 🔺 Upgrade Card Section Layout Overlay */
#upgrade-panel {
    margin-top: auto; background: #1c2d3d; padding: 12px;
    border-radius: 6px; border: 2px solid #2979ff; box-shadow: inset 0 0 10px rgba(0,0,0,0.5);
}
#upgrade-panel h3 { margin: 0 0 10px 0; font-size: 14px; text-align: center; color: #fff; }
.panel-buttons { display: flex; gap: 8px; }
#upgrade-btn, #sell-btn {
    flex: 1; border: none; padding: 8px; font-size: 12px;
    font-weight: bold; color: white; border-radius: 4px; cursor: pointer;
}
#upgrade-btn { background: #e67e22; }
#sell-btn { background: #c62828; }

/* 🏆 Victory Modal Screen Alert Elements */
#victory-screen {
    position: fixed; top: 0; left: 0; width: 100%; height: 100%;
    background: rgba(0,0,0,0.85); z-index: 120;
    display: flex; justify-content: center; align-items: center;
}
#victory-box h2 { color: #ffd54f; margin-top: 0; font-size: 28px; }
.victory-choices { display: flex; gap: 15px; margin-top: 20px; }
.victory-btn {
    background: #2979ff; color: white; border: none; padding: 10px 15px;
    font-weight: bold; border-radius: 4px; cursor: pointer; font-size: 14px;
}
.victory-btn:hover { background: #2962ff; }

/* 🔄 Game Over Layout Button Hook */
#game-over-restart {
    position: fixed; top: 56%; left: 50%; transform: translate(-50%, -50%);
    z-index: 130; display: flex; justify-content: center;
}
#game-over-restart button {
    background: #c62828; color: white; border: none; padding: 12px 25px;
    font-size: 16px; font-weight: bold; border-radius: 6px; cursor: pointer;
    box-shadow: 0 4px 15px rgba(0,0,0,0.5);
}

  </style>

  
</head>
<body>
  <link rel="stylesheet" href="style.css">

    <!-- 🎬 MAIN TITLE SCREEN OVERLAY -->
    <div id="title-screen" class="overlay-screen">
        <div id="title-box">
            <h1 class="game-logo">👑 TOWER DEFENSE</h1>
            <p class="game-subtitle">Defend the path. Upgrade your strategy.</p>
            <div class="highscore-badge">🏆 High Score: Wave <span id="title-highscore">0</span></div>
            
            <!-- 🗺️ MAP SELECTION BOX -->
            <div id="map-selection-container">
                <h3>Select Battlefield Map Layout:</h3>
                <div class="map-row">
                    <button class="map-opt selected" data-map="linear">🗺️ Map A: Choke Path</button>
                    <button class="map-opt" data-map="zigzag">🗺️ Map B: S-Curves</button>
                    <button class="map-opt" data-map="spiral">🗺️ Map C: Spiral Core</button>
                </div>
            </div>

            <button id="start-game-btn">⚔️ Start Game</button>
        </div>
    </div>

    <!-- 🟢 DIFFICULTY SELECTION OVERLAY -->
    <div id="difficulty-screen" class="overlay-screen hidden">
        <div id="difficulty-box">
            <h1>Select Difficulty</h1>
            <p>Choose your challenge level to begin the match:</p>
            <button class="diff-btn" data-mode="easy">🟢 Easy (More Gold, Weaker Enemies)</button>
            <button class="diff-btn" data-mode="normal">🟡 Normal (Standard Rules)</button>
            <button class="diff-btn" data-mode="hard">🔴 Hard (Fair Challenge, Stronger Enemies)</button>
        </div>
    </div>

    <!-- 🎮 MAIN GAME WORKSPACE -->
    <div id="game-container" style="display: none;">
        <div id="main-area">
            <div id="ui">
                <div id="health-wrapper">
                    ❤️ Lives: <span id="lives">10</span>
                    <div id="health-bar-container">
                        <div id="health-fill"></div>
                    </div>
                </div>
                <div>💰 Gold: $<span id="gold">220</span></div>
                <div>⚔️ Wave: <span id="wave-number">0</span></div>
                <div class="hud-highscore">🏆 Record: Wave <span id="game-highscore">0</span></div>
            </div>

            <canvas id="gameCanvas" width="800" height="600"></canvas>

            <div id="phase-control">
                <button id="next-wave-btn">▶️ Start Next Wave</button>
                <button id="speed-btn">⏩ Speed: 1x</button>
            </div>
        </div>

        <!-- Shop Sidebar (Ordered by ascending Cost) -->
        <div id="shop-sidebar">
            <h2>Tower Shop</h2>
            <p class="shop-hint">Select a defender, preview its range on the grass, and click to place.</p>
            
            <div class="shop-item active" data-type="scout">
                <h3>🎯 Scout Post</h3>
                <p>Cheap, weak, ultra-fast fire rate.</p>
                <span class="price">Cost: $40</span>
            </div>

            <div class="shop-item" data-type="basic">
                <h3>🏹 Archer Tower</h3>
                <p>Balanced speed and range.</p>
                <span class="price">Cost: $60</span>
            </div>

            <div class="shop-item" data-type="frost">
                <h3>❄️ Frost Mage</h3>
                <p>Low damage, slows enemies down.</p>
                <span class="price">Cost: $80</span>
            </div>

            <div class="shop-item" data-type="giga">
                <h3>🪨 Giga Launcher</h3>
                <p>Heavy boulder with medium splash range.</p>
                <span class="price">Cost: $95</span>
            </div>

            <div class="shop-item" data-type="cannon">
                <h3>💣 Cannon Tower</h3>
                <p>Slow fire rate, massive splash damage.</p>
                <span class="price">Cost: $110</span>
            </div>

            <div class="shop-item" data-type="tesla">
                <h3>⚡ Tesla Coil</h3>
                <p>Rapid chain-shocks close range targets.</p>
                <span class="price">Cost: $135</span>
            </div>

            <div class="shop-item" data-type="laser">
                <h3>🧬 Plasma Beam</h3>
                <p>Extremely high range and output.</p>
                <span class="price">Cost: $160</span>
            </div>

            <div class="shop-item" data-type="sniper">
                <h3>👁️ Railgun Sniper</h3>
                <p>Enormous range, extreme reload cooldown.</p>
                <span class="price">Cost: $210</span>
            </div>

            <!-- ⚙️ UPGRADE & SELL CONTEXT PANEL MODAL -->
            <div id="upgrade-panel" style="display: none;">
                <h3 id="tower-name">ARCHER TOWER</h3>
                <div class="panel-buttons">
                    <button id="upgrade-btn">🔺 Upgrade (<span id="upgrade-cost">$50</span>)</button>
                    <button id="sell-btn">💰 Sell (+<span id="sell-value">$25</span>)</button>
                </div>
            </div>
        </div>
    </div>

    <!-- 🏆 VICTORY CONGRATULATIONS PANEL MODAL -->
    <div id="victory-screen" style="display: none;">
        <div id="victory-box">
            <h2>🏆 VICTORY!</h2>
            <p>You successfully defended the path through Wave 15!</p>
            <div class="victory-choices">
                <button id="victory-continue-btn" class="victory-btn">♾️ Enter Endless Survival</button>
                <button class="victory-btn restart-trigger-btn">🔄 Main Menu / Reset</button>
            </div>
        </div>
    </div>

    <!-- 🔄 GAME OVER RESTART INTERFACE PANEL HOOK -->
    <div id="game-over-restart" style="display: none;">
        <button class="restart-trigger-btn">🔄 Return to Title Main Menu</button>
    </div>

    <script src="script.js"></script>

  <script>
    const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Game State Variables
let gold = 220;
let lives = 10;
const maxLives = 10;
let wave = 0;
let isWaveActive = false; 
let gameSpeed = 1; 

// Endless mode tracking modifiers
let isVictoryOpen = false;
let hasShownVictory = false;
let damageFlashAlpha = 0; 
let enemyHpMultiplier = 1.0;
let endWaveBonus = 40;

// Hover placement tracking targets
let mouseX = -999;
let mouseY = -999;
let isMouseInCanvas = false;
let selectedTowerType = "scout"; 
let selectedTower = null; 

// 8 Sorted Towers configurations
const towerConfigs = {
    scout:  { cost: 40,  range: 100, fireRate: 15, color: "#1abc9c" },
    basic:  { cost: 60,  range: 135, fireRate: 30, color: "#3498db" },
    frost:  { cost: 80,  range: 150, fireRate: 45, color: "#9b59b6" },
    giga:   { cost: 95,  range: 120, fireRate: 50, color: "#95a5a6" },
    cannon: { cost: 110, range: 110, fireRate: 70, color: "#e67e22" },
    tesla:  { cost: 135, range: 95,  fireRate: 10, color: "#f1c40f" },
    laser:  { cost: 160, range: 180, fireRate: 25, color: "#e74c3c" },
    sniper: { cost: 210, range: 350, fireRate: 140, color: "#16a085" }
};

// UI Element Handles (FIXED MISMATCHED ID STRINGS EXPLICITLY)
const goldEl = document.getElementById("gold");
const livesTextEl = document.getElementById("lives"); 
const waveEl = document.getElementById("wave-number");
const nextWaveBtn = document.getElementById("next-wave-btn");
const speedBtn = document.getElementById("speed-btn");
const shopItems = document.querySelectorAll(".shop-item");
const healthFillEl = document.getElementById("health-fill");
const upgradePanel = document.getElementById("upgrade-panel");
const towerNameEl = document.getElementById("tower-name");
const upgradeBtn = document.getElementById("upgrade-btn");
const upgradeCostEl = document.getElementById("upgrade-cost");
const sellBtn = document.getElementById("sell-btn");
const sellValueEl = document.getElementById("sell-value");

// High Score handles fixed matching spelling attributes values
const highScoreTitleEl = document.getElementById("title-highscore"); 
const highScoreGameEl = document.getElementById("game-highscore");  
const mapOptions = document.querySelectorAll(".map-opt");

// Multi Map Layout Array Coordinates Database Libraries
let selectedMapKey = "linear"; 
const maps = {
    linear: [
        {x: 0, y: 300}, {x: 250, y: 300}, {x: 250, y: 100},
        {x: 550, y: 100}, {x: 550, y: 450}, {x: 800, y: 450}
    ],
    zigzag: [
        {x: 0, y: 150}, {x: 200, y: 150}, {x: 200, y: 450},
        {x: 400, y: 450}, {x: 400, y: 150}, {x: 600, y: 150},
        {x: 600, y: 450}, {x: 800, y: 450}
    ],
    spiral: [
        {x: 0, y: 50}, {x: 750, y: 50}, {x: 750, y: 550},
        {x: 100, y: 550}, {x: 100, y: 150}, {x: 650, y: 150},
        {x: 650, y: 450}, {x: 250, y: 450}, {x: 250, y: 280},
        {x: 500, y: 280}, {x: 500, y: 330}, {x: 800, y: 330}
    ]
};
let path = maps[selectedMapKey]; 
const roadWidth = 40;

// Main Tracking Arrays
const enemies = [];
const towers = [];
const projectiles = [];
const particles = [];       
const floatingAlerts = [];   
const decorations = [];      

let enemiesToSpawn = 0;
let spawnTimer = 0;
const baseSpawnInterval = 50; 

function updateHighScoreDisplays() {
    const savedScore = localStorage.getItem("td_highest_wave") || 0;
    if (highScoreTitleEl) highScoreTitleEl.innerText = savedScore;
    if (highScoreGameEl) highScoreGameEl.innerText = savedScore;
}

function updateHealthBarUI() {
    if (!healthFillEl) return;
    const pct = Math.max(0, (lives / maxLives) * 100);
    healthFillEl.style.width = pct + "%";
    healthFillEl.style.background = pct < 35 ? "#ff3333" : "#4caf50";
}

// BUGFIX: Scenery decorations will never overwrite paths or block click hitboxes
function generateDecorations() {
    decorations.length = 0;
    for (let i = 0; i < 45; i++) {
        let rx = Math.random() * canvas.width;
        let ry = Math.random() * canvas.height;
        if (!checkPointNearTrack(rx, ry, 22)) {
            decorations.push(new Decoration(rx, ry, Math.random() > 0.4 ? "grass" : "tree"));
        }
    }
}

function checkPointNearTrack(px, py, buffer) {
    for (let i = 0; i < path.length - 1; i++) {
        let p1 = path[i]; let p2 = path[i+1];
        let l2 = Math.pow(p2.x - p1.x, 2) + Math.pow(p2.y - p1.y, 2);
        let t = ((px - p1.x) * (p2.x - p1.x) + (py - p1.y) * (p2.y - p1.y)) / l2;
        t = Math.max(0, Math.min(1, t));
        let closestX = p1.x + t * (p2.x - p1.x);
        let closestY = p1.y + t * (p2.y - p1.y);
        if (Math.hypot(px - closestX, py - closestY) < (roadWidth / 2) + buffer) return true;
    }
    return false;
}

function isPointOnTrack(px, py) {
    return checkPointNearTrack(px, py, 12);
}

function checkPlacementValid(x, y) {
    if (isPointOnTrack(x, y)) return false;
    for (let t of towers) {
        if (Math.hypot(t.x - x, t.y - y) < 34) return false;
    }
    return true;
}

// --- WEB AUDIO ARCADE SOUND SYNTHESIZER ---
let audioCtx = null;
function playSynthSound(type) {
    try {
        if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        if (audioCtx.state === "suspended") audioCtx.resume();

        let osc = audioCtx.createOscillator();
        let gainNode = audioCtx.createGain();
        osc.connect(gainNode); gainNode.connect(audioCtx.destination);
        const now = audioCtx.currentTime;

        if (type === "shoot") {
            osc.type = "square"; osc.frequency.setValueAtTime(600, now);
            osc.frequency.exponentialRampToValueAtTime(150, now + 0.08);
            gainNode.gain.setValueAtTime(0.03, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.08);
            osc.start(now); osc.stop(now + 0.08);
        } else if (type === "hit") {
            osc.type = "triangle"; osc.frequency.setValueAtTime(180, now);
            gainNode.gain.setValueAtTime(0.05, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.04);
            osc.start(now); osc.stop(now + 0.04);
        } else if (type === "explosion") {
            osc.type = "sawtooth"; osc.frequency.setValueAtTime(110, now);
            osc.frequency.linearRampToValueAtTime(30, now + 0.25);
            gainNode.gain.setValueAtTime(0.1, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.25);
            osc.start(now); osc.stop(now + 0.25);
        } else if (type === "place") {
            osc.type = "sine"; osc.frequency.setValueAtTime(320, now);
            osc.frequency.setValueAtTime(480, now + 0.06);
            gainNode.gain.setValueAtTime(0.07, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.12);
            osc.start(now); osc.stop(now + 0.12);
        } else if (type === "upgrade") {
            osc.type = "sine"; osc.frequency.setValueAtTime(420, now);
            osc.frequency.exponentialRampToValueAtTime(850, now + 0.2);
            gainNode.gain.setValueAtTime(0.06, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.2);
            osc.start(now); osc.stop(now + 0.2);
        } else if (type === "sell") {
            osc.type = "sine"; osc.frequency.setValueAtTime(450, now);
            osc.frequency.exponentialRampToValueAtTime(220, now + 0.15);
            gainNode.gain.setValueAtTime(0.07, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.15);
            osc.start(now); osc.stop(now + 0.15);
        } else if (type === "damage") {
            osc.type = "sawtooth"; osc.frequency.setValueAtTime(85, now);
            osc.frequency.setValueAtTime(65, now + 0.1);
            gainNode.gain.setValueAtTime(0.15, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.22);
            osc.start(now); osc.stop(now + 0.22);
        } else if (type === "error") {
            osc.type = "sawtooth"; osc.frequency.setValueAtTime(120, now);
            gainNode.gain.setValueAtTime(0.08, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.15);
            osc.start(now); osc.stop(now + 0.15);
        } else if (type === "start") {
            osc.type = "sine"; osc.frequency.setValueAtTime(261.6, now); 
            osc.frequency.setValueAtTime(329.6, now + 0.08); 
            osc.frequency.setValueAtTime(392.0, now + 0.16); 
            osc.frequency.setValueAtTime(523.3, now + 0.24); 
            gainNode.gain.setValueAtTime(0.06, now); gainNode.gain.exponentialRampToValueAtTime(0.001, now + 0.45);
            osc.start(now); osc.stop(now + 0.45);
        }
    } catch(e) { console.log("Audio pipeline muted context initialization failed."); }
}
// --- EXPLOSION PARTICLE CLASS ---
class Particle {
    constructor(x, y, color) {
        this.x = x;
        this.y = y;
        this.color = color;
        this.radius = Math.random() * 3 + 2;
        const angle = Math.random() * Math.PI * 2;
        const speed = Math.random() * 3 + 1;
        this.vx = Math.cos(angle) * speed;
        this.vy = Math.sin(angle) * speed;
        this.alpha = 1.0;
        this.decay = Math.random() * 0.02 + 0.015;
    }
    update() {
        for (let step = 0; step < gameSpeed; step++) {
            this.x += this.vx;
            this.y += this.vy;
            this.alpha -= this.decay;
        }
    }
    draw() {
        ctx.save();
        ctx.globalAlpha = Math.max(0, this.alpha);
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.restore();
    }
}

// --- FLOATING TEXT ALERT CLASS ---
class FloatingAlert {
    constructor(x, y, text) {
        this.x = x;
        this.y = y;
        this.text = text;
        this.alpha = 1.0;
        this.speedY = -1.2;
    }
    update() {
        for (let step = 0; step < gameSpeed; step++) {
            this.y += this.speedY;
            this.alpha -= 0.025;
        }
    }
    draw() {
        ctx.save();
        ctx.globalAlpha = Math.max(0, this.alpha);
        ctx.fillStyle = "#ff4444";
        ctx.font = "bold 16px sans-serif";
        ctx.textAlign = "center";
        ctx.fillText(this.text, this.x, this.y);
        ctx.restore();
    }
}

// --- BACKGROUND DECORATION ELEMENT CLASS ---
class Decoration {
    constructor(x, y, type) {
        this.x = x;
        this.y = y;
        this.type = type; 
        this.baseSize = type === "tree" ? Math.random() * 5 + 8 : Math.random() * 3 + 4;
        this.size = this.baseSize;
        this.pulseTimer = Math.random() * Math.PI * 2;
        this.color = type === "tree" ? "#1e4620" : "#2a4d2c";
    }
    update() {
        this.pulseTimer += 0.015 * gameSpeed;
        this.size = this.baseSize + Math.sin(this.pulseTimer) * 0.6;
    }
    draw() {
        ctx.beginPath();
        if (this.type === "tree") {
            ctx.fillStyle = "#5c4033";
            ctx.fillRect(this.x - 2, this.y, 4, this.size);
            ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
            ctx.fillStyle = this.color;
        } else {
            ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
            ctx.fillStyle = this.color;
        }
        ctx.fill();
        ctx.closePath();
    }
}

// --- ENEMY VECTOR ARCHETYPE CLASS ---
class Enemy {
    constructor(waveNumber) {
        this.x = path[0].x; 
        this.y = path[0].y; 
        this.radius = 12;
        
        // Scale health curves dynamically
        this.maxHp = (45 + (waveNumber * 40)) * enemyHpMultiplier; 
        if (waveNumber > 15) {
            this.maxHp *= (1 + (waveNumber - 15) * 0.35); // Endless mode tuning
        }
        this.hp = this.maxHp;
        
        this.speed = 1.4 + (waveNumber * 0.10); 
        this.baseSpeed = this.speed; 
        this.waypointIndex = 0;
        this.slowDuration = 0;
        this.goldReward = 6; 

        if (waveNumber <= 2)       this.baseColor = "#ff4444"; 
        else if (waveNumber <= 4)  this.baseColor = "#4caf50"; 
        else if (waveNumber <= 6)  this.baseColor = "#00bcd4"; 
        else if (waveNumber <= 9)  this.baseColor = "#e91e63"; 
        else                       this.baseColor = "#ffeb3b"; 
    }

    update() {
        for (let step = 0; step < gameSpeed; step++) {
            if (this.slowDuration > 0) {
                this.slowDuration--; this.speed = this.baseSpeed * 0.45; 
            } else { this.speed = this.baseSpeed; }

            let target = path[this.waypointIndex + 1];
            if (!target) return;
            let dx = target.x - this.x; let dy = target.y - this.y;
            let distance = Math.hypot(dx, dy);

            if (distance < this.speed) {
                this.waypointIndex++;
                if (this.waypointIndex >= path.length - 1) {
                    lives--; 
                    livesTextEl.innerText = lives; 
                    updateHealthBarUI();
                    damageFlashAlpha = 0.5; 
                    playSynthSound("damage"); 
                    this.hp = 0; return;
                }
            } else {
                this.x += (dx / distance) * this.speed; this.y += (dy / distance) * this.speed;
            }
        }
    }

    draw() {
        ctx.beginPath(); ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = this.slowDuration > 0 ? "#85c1e9" : this.baseColor;
        ctx.fill(); ctx.closePath();
        ctx.fillStyle = "#000"; ctx.fillRect(this.x - 15, this.y - 22, 30, 5);
        ctx.fillStyle = "#00ff00"; ctx.fillRect(this.x - 15, this.y - 22, 30 * Math.max(0, this.hp / this.maxHp), 5);
    }
}

// --- TOWER STRUCTURE ASSET CLASS ---
class Tower {
    constructor(x, y, type) {
        this.x = x;
        this.y = y;
        this.type = type;
        this.radius = 16;
        this.level = 1;
        this.range = towerConfigs[type].range;
        this.fireRate = towerConfigs[type].fireRate;
        this.color = towerConfigs[type].color;
        this.cooldown = 0;
    }

    getUpgradeCost() {
        return Math.floor(towerConfigs[this.type].cost * 0.8 * this.level);
    }

    getSellValue() {
        let baseCost = towerConfigs[this.type].cost;
        let totalInvested = baseCost;
        for (let i = 1; i < this.level; i++) {
            totalInvested += Math.floor(baseCost * 0.8 * i);
        }
        return Math.floor(totalInvested * 0.5);
    }

    upgrade() {
        this.level++;
        this.range = Math.floor(this.range * 1.15);
        this.fireRate = Math.max(5, Math.floor(this.fireRate * 0.9));
        playSynthSound("upgrade"); 
    }

    update() {
        for (let step = 0; step < gameSpeed; step++) {
            if (this.cooldown > 0) this.cooldown--;
            if (this.cooldown === 0) {
                let target = null;
                for (let enemy of enemies) {
                    if (Math.hypot(enemy.x - this.x, enemy.y - this.y) <= this.range) {
                        target = enemy;
                        break;
                    }
                }
                if (target) {
                    projectiles.push(new Projectile(this.x, this.y, target, this.type, this.level));
                    this.cooldown = this.fireRate;
                    playSynthSound("shoot"); 
                }
            }
        }
    }

    draw() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.lineWidth = 2;
        ctx.strokeStyle = "#fff";
        ctx.stroke();
        ctx.closePath();

        ctx.fillStyle = "#fff";
        ctx.font = "10px sans-serif";
        ctx.textBaseline = "middle";
        ctx.textAlign = "center";
        ctx.fillText(this.level, this.x, this.y);
    }
}
// --- PROJECTILE CLASS WITH SPLASH ENGINE DAMAGE MATH ---
class Projectile {
    constructor(x, y, target, towerType, towerLevel) {
        this.x = x;
        this.y = y;
        this.target = target;
        this.towerType = towerType;
        this.speed = 8;
        this.active = true;
        this.radius = 4;

        if (towerType === "scout") { this.damage = 12; this.speed = 11; this.radius = 3; }
        else if (towerType === "frost") { this.damage = 10; this.speed = 9; }
        else if (towerType === "giga") { this.damage = 45; this.speed = 7; this.radius = 6; }
        else if (towerType === "cannon") { this.damage = 75; this.radius = 7; this.speed = 6; }
        else if (towerType === "tesla") { this.damage = 15; this.speed = 13; this.radius = 3; }
        else if (towerType === "laser") { this.damage = 40; this.speed = 14; this.radius = 5; }
        else if (towerType === "sniper") { this.damage = 180; this.speed = 22; this.radius = 4; }
        else { this.damage = 25; }

        this.damage = Math.floor(this.damage * (1 + (towerLevel - 1) * 0.25));
    }

    update() {
        for (let step = 0; step < gameSpeed; step++) {
            if (!this.active) return;
            if (this.target.hp <= 0) {
                this.active = false;
                return;
            }
            let dx = this.target.x - this.x;
            let dy = this.target.y - this.y;
            let dist = Math.hypot(dx, dy);

            if (dist < this.speed) {
                this.active = false;

                // Execute Area-of-Effect Explosive Splash logic calculations
                if (this.towerType === "cannon" || this.towerType === "giga") {
                    const splashRadius = this.towerType === "cannon" ? 75 : 55;
                    const splashDamage = this.damage;
                    let soundPlayed = false;

                    for (let enemy of enemies) {
                        let blastDist = Math.hypot(enemy.x - this.x, enemy.y - this.y);
                        if (blastDist <= splashRadius) {
                            enemy.hp -= splashDamage;
                            
                            for (let p = 0; p < 4; p++) {
                                particles.push(new Particle(enemy.x, enemy.y, "#ff6700"));
                            }

                            if (!soundPlayed) {
                                playSynthSound("explosion"); 
                                soundPlayed = true;
                            }

                            if (enemy.hp <= 0) {
                                gold += enemy.goldReward;
                                goldEl.innerText = gold;
                            }
                        }
                    }
                } else {
                    // Standard Single-Target direct damage processor paths
                    this.target.hp -= this.damage;
                    if (this.towerType === "frost") this.target.slowDuration = 140;
                    playSynthSound("hit"); 

                    if (this.target.hp <= 0) {
                        gold += this.target.goldReward;
                        goldEl.innerText = gold;
                        for (let p = 0; p < 10; p++) {
                            particles.push(new Particle(this.target.x, this.target.y, this.target.baseColor));
                        }
                    }
                }
            } else {
                this.x += (dx / dist) * this.speed;
                this.y += (dy / dist) * this.speed;
            }
        }
    }

    draw() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = this.towerType === "frost" ? "#aed6f1" : (this.towerType === "laser" ? "#e74c3c" : "#ffeb3b");
        ctx.fill();
        ctx.closePath();
    }
}

// --- ROAD MAP LAYOUT RENDERING ---
function drawPath() {
    ctx.beginPath();
    ctx.moveTo(path[0].x, path[0].y); 
    for (let i = 1; i < path.length; i++) {
        ctx.lineTo(path[i].x, path[i].y);
    }
    ctx.lineWidth = roadWidth;
    ctx.strokeStyle = "#cca671"; 
    ctx.lineCap = "round";
    ctx.lineJoin = "round";
    ctx.stroke();
    ctx.closePath();
}

function drawTowerPreview() {
    if (!isMouseInCanvas || isVictoryOpen || selectedTower) return;
    const config = towerConfigs[selectedTowerType];
    const isValid = checkPlacementValid(mouseX, mouseY) && (gold >= config.cost);

    ctx.beginPath();
    ctx.arc(mouseX, mouseY, config.range, 0, Math.PI * 2);
    ctx.fillStyle = isValid ? "rgba(40, 167, 69, 0.1)" : "rgba(220, 53, 69, 0.15)";
    ctx.fill();
    ctx.strokeStyle = isValid ? "rgba(40, 167, 69, 0.4)" : "rgba(220, 53, 69, 0.5)";
    ctx.lineWidth = 2;
    ctx.stroke();
    ctx.closePath();

    ctx.beginPath();
    ctx.arc(mouseX, mouseY, 16, 0, Math.PI * 2);
    ctx.fillStyle = config.color;
    ctx.save();
    ctx.globalAlpha = 0.5;
    ctx.fill();
    ctx.restore();
    ctx.lineWidth = 2;
    ctx.strokeStyle = "#fff";
    ctx.stroke();
    ctx.closePath();
}

// --- INTERACTIVE EVENT LISTENERS ---
canvas.addEventListener("mousemove", (e) => {
    const rect = canvas.getBoundingClientRect();
    mouseX = e.clientX - rect.left;
    mouseY = e.clientY - rect.top;
    isMouseInCanvas = true;
});

canvas.addEventListener("mouseleave", () => {
    isMouseInCanvas = false;
});

shopItems.forEach(item => {
    item.addEventListener("click", () => {
        selectedTower = null;
        upgradePanel.style.display = "none";
        
        shopItems.forEach(i => i.classList.remove("active"));
        item.classList.add("active");
        selectedTowerType = item.getAttribute("data-type");
    });
});

canvas.addEventListener("click", () => {
    if (isVictoryOpen) return;

    let clickedTower = null;
    for (let t of towers) {
        if (Math.hypot(t.x - mouseX, t.y - mouseY) < 18) {
            clickedTower = t;
            break;
        }
    }

    if (clickedTower) {
        selectedTower = clickedTower;
        shopItems.forEach(i => i.classList.remove("active")); 
        
        upgradePanel.style.display = "block";
        towerNameEl.innerText = selectedTower.type.toUpperCase() + " (Lv. " + selectedTower.level + ")";
        upgradeCostEl.innerText = selectedTower.getUpgradeCost();
        sellValueEl.innerText = selectedTower.getSellValue();
        return;
    }

    if (selectedTower) {
        selectedTower = null;
        upgradePanel.style.display = "none";
        document.querySelector(`[data-type="${selectedTowerType}"]`).classList.add("active");
        return;
    }

    const currentCost = towerConfigs[selectedTowerType].cost;
    if (gold < currentCost) {
        floatingAlerts.push(new FloatingAlert(mouseX, mouseY - 15, "❌ Insufficient Gold!"));
        playSynthSound("error");
        return;
    }
    if (!checkPlacementValid(mouseX, mouseY)) return;
    
    towers.push(new Tower(mouseX, mouseY, selectedTowerType));
    gold -= currentCost;
    goldEl.innerText = gold;
    playSynthSound("place");
});

upgradeBtn.addEventListener("click", () => {
    if (!selectedTower) return;
    const cost = selectedTower.getUpgradeCost();
    
    if (gold < cost) {
        floatingAlerts.push(new FloatingAlert(selectedTower.x, selectedTower.y - 20, "❌ Need More Gold!"));
        playSynthSound("error");
        return;
    }

    gold -= cost;
    goldEl.innerText = gold;
    selectedTower.upgrade();

    towerNameEl.innerText = selectedTower.type.toUpperCase() + " (Lv. " + selectedTower.level + ")";
    upgradeCostEl.innerText = selectedTower.getUpgradeCost();
    sellValueEl.innerText = selectedTower.getSellValue();
});

sellBtn.addEventListener("click", () => {
    if (!selectedTower) return;
    const returnGold = selectedTower.getSellValue();
    
    gold += returnGold;
    goldEl.innerText = gold;
    playSynthSound("sell");

    const index = towers.indexOf(selectedTower);
    if (index > -1) towers.splice(index, 1);

    selectedTower = null;
    upgradePanel.style.display = "none";
    document.querySelector(`[data-type="${selectedTowerType}"]`).classList.add("active");
});

nextWaveBtn.addEventListener("click", () => {
    if (isWaveActive || isVictoryOpen) return;
    wave++;
    waveEl.innerText = wave;
    enemiesToSpawn = 6 + (wave * 2);
    isWaveActive = true;
    nextWaveBtn.style.display = "none";
});

speedBtn.addEventListener("click", () => {
    if (gameSpeed === 1) {
        gameSpeed = 2;
        speedBtn.innerText = "⏩ Speed: 2x";
        speedBtn.style.background = "#e67e22";
    } else {
        gameSpeed = 1;
        speedBtn.innerText = "⏩ Speed: 1x";
        speedBtn.style.background = "#455a64";
    }
});

mapOptions.forEach(opt => {
    opt.addEventListener("click", () => {
        mapOptions.forEach(o => o.classList.remove("selected"));
        opt.classList.add("selected");
        selectedMapKey = opt.getAttribute("data-map");
        path = maps[selectedMapKey]; 
    });
});

document.getElementById("start-game-btn").addEventListener("click", () => {
    document.getElementById("title-screen").classList.add("hidden");
    document.getElementById("difficulty-screen").classList.remove("hidden");
});

document.getElementById("victory-continue-btn").addEventListener("click", () => {
    document.getElementById("victory-screen").style.display = "none";
    isVictoryOpen = false;
    hasShownVictory = true;
    nextWaveBtn.style.display = "block";
    nextWaveBtn.innerText = `▶️ Start Wave ${wave + 1}`;
});

function handleRecordTrackingCheck() {
    const currentHighScore = localStorage.getItem("td_highest_wave") || 0;
    if (wave > currentHighScore) {
        localStorage.setItem("td_highest_wave", wave);
        updateHighScoreDisplays();
    }
}

document.querySelectorAll(".restart-trigger-btn").forEach(btn => {
    btn.addEventListener("click", () => {
        handleRecordTrackingCheck();
        
        document.getElementById("victory-screen").style.display = "none";
        document.getElementById("game-container").style.display = "none";
        
        document.getElementById("title-screen").classList.remove("hidden");
        document.getElementById("difficulty-screen").classList.add("hidden");
        
        towers.length = 0;
        enemies.length = 0;
        projectiles.length = 0;
        particles.length = 0;
        floatingAlerts.length = 0;
        
        selectedTower = null;
        upgradePanel.style.display = "none";
        
        wave = 0;
        isWaveActive = false;
        isVictoryOpen = false;
        hasShownVictory = false;
        gameSpeed = 1;
        speedBtn.innerText = "⏩ Speed: 1x";
        speedBtn.style.background = "#455a64";
        nextWaveBtn.style.display = "block"; 
        nextWaveBtn.innerText = "▶️ Start Next Wave";
        waveEl.innerText = wave;
    });
});

document.querySelectorAll(".diff-btn").forEach(btn => {
    btn.addEventListener("click", () => {
        const mode = btn.getAttribute("data-mode");
        if (mode === "easy") { gold = 350; enemyHpMultiplier = 0.7; endWaveBonus = 55; }
        else if (mode === "hard") { gold = 200; enemyHpMultiplier = 1.2; endWaveBonus = 35; }
        else { gold = 240; enemyHpMultiplier = 1.0; endWaveBonus = 40; } 

        lives = maxLives; 
        goldEl.innerText = gold;
        livesTextEl.innerText = lives;
        updateHealthBarUI();

        document.getElementById("difficulty-screen").classList.add("hidden");
        document.getElementById("game-container").style.display = "flex"; 
        
        document.querySelectorAll(".shop-item").forEach(i => i.classList.remove("active"));
        selectedTowerType = "scout";
        document.querySelector('[data-type="scout"]').classList.add("active");
        
        generateDecorations();
        playSynthSound("start"); 
        gameLoop(); 
    });
});

updateHighScoreDisplays();

// --- CORE ENGINE LOOP ---
function gameLoop() {
    if (lives <= 0) {
        handleRecordTrackingCheck();
        ctx.fillStyle = "rgba(0, 0, 0, 0.85)";
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = "#ff4444";
        ctx.font = "bold 48px sans-serif";
        ctx.textAlign = "center";
        ctx.fillText("GAME OVER", canvas.width / 2, canvas.height / 2);
        document.getElementById("game-over-restart").style.display = "block";
        return;
    } else {
        document.getElementById("game-over-restart").style.display = "none";
    }

    if (isVictoryOpen) {
        requestAnimationFrame(gameLoop);
        return;
    }

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    for (let d of decorations) { d.update(); d.draw(); }
    drawPath();

    if (isWaveActive && enemiesToSpawn > 0) {
        spawnTimer += gameSpeed;
        if (spawnTimer >= baseSpawnInterval) {
            enemies.push(new Enemy(wave));
            enemiesToSpawn--;
            spawnTimer = 0;
        }
    }

    for (let tower of towers) tower.update();
    for (let tower of towers) tower.draw();

    if (selectedTower) {
        ctx.beginPath();
        ctx.arc(selectedTower.x, selectedTower.y, selectedTower.range, 0, Math.PI * 2);
        ctx.strokeStyle = "rgba(255, 255, 255, 0.4)";
        ctx.lineWidth = 3;
        ctx.stroke();
        ctx.closePath();
    }

    for (let i = enemies.length - 1; i >= 0; i--) {
        enemies[i].update();
        if (enemies[i].hp <= 0) enemies.splice(i, 1); else enemies[i].draw();
    }

    if (isWaveActive && enemiesToSpawn === 0 && enemies.length === 0) {
        isWaveActive = false;
        projectiles.length = 0;
        gold += endWaveBonus;
        goldEl.innerText = gold;
        
        nextWaveBtn.style.display = "block";
        if (wave === 15 && !hasShownVictory) {
            isVictoryOpen = true; 
            document.getElementById("victory-screen").style.display = "flex";
        } else {
            nextWaveBtn.innerText = `▶️ Start Wave ${wave + 1}`;
        }
    }

    for (let i = projectiles.length - 1; i >= 0; i--) {
        projectiles[i].update();
        if (!projectiles[i].active) projectiles.splice(i, 1); else projectiles[i].draw();
    }

    for (let i = particles.length - 1; i >= 0; i--) {
        particles[i].update();
        if (particles[i].alpha <= 0) particles.splice(i, 1); else particles[i].draw();
    }

    for (let i = floatingAlerts.length - 1; i >= 0; i--) {
        floatingAlerts[i].update();
        if (floatingAlerts[i].alpha <= 0) floatingAlerts.splice(i, 1); else floatingAlerts[i].draw();
    }

    drawTowerPreview();

    if (damageFlashAlpha > 0) {
        ctx.save();
        ctx.fillStyle = `rgba(255, 0, 0, ${damageFlashAlpha})`;
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.restore();
        damageFlashAlpha -= 0.02 * gameSpeed; 
    }

    requestAnimationFrame(gameLoop);
}

  </script>
</body>
</html>
