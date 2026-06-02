const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

let gameActive = false;
let distanceScore = 0;
let bananasCollected = 0;
let scrollSpeed = 5.5;
const baseSpeed = 5.5;
let runStartTime = 0;

// Horizontal Climbing Vine Positions
const vineYPositions = [150, 270, 390];
let currentLane = 1;

// Entity Mechanics
let monkey = {
    x: 340,
    y: vineYPositions[1],
    targetY: vineYPositions[1],
    width: 50,
    height: 45,
    animTime: 0
};

let chaser = {
    x: 90,
    y: vineYPositions[1],
    targetY: vineYPositions[1],
    width: 45,
    height: 90,
    inputQueue: [] // Tracks targeted jumps with time marks for a 1-second step delay
};

// Lists of Scrolling Environment Elements
let backgroundTrees = [];
let horizontalVines = [];
let hangingVines = [];
let bananaCache = [];
let canopyLeaves = [];

// DOM Interface Attachments
const scoreVal = document.getElementById('scoreVal');
const bananaCountVal = document.getElementById('bananaCount');
const finalScore = document.getElementById('finalScore');
const finalBananas = document.getElementById('finalBananas');
const startScreen = document.getElementById('startScreen');
const gameOverScreen = document.getElementById('gameOverScreen');

// Run world generation layout loop
initJungleWorld();
renderMenuLoop();

function initJungleWorld() {
    backgroundTrees = [];
    horizontalVines = [];
    hangingVines = [];
    bananaCache = [];
    canopyLeaves = [];

    // Background tree trunks colored brown for a forest look
    for (let i = 0; i < 9; i++) {
        backgroundTrees.push({
            x: i * 130 + Math.random() * 40,
            width: 45 + Math.random() * 30,
            color: "#5a3a22", 
            speedFactor: 0.25
        });
        backgroundTrees.push({
            x: i * 160 + 20,
            width: 25 + Math.random() * 20,
            color: "#422b19", 
            speedFactor: 0.15
        });
    }

    // Continuous horizontal stringing vines lines rows
    for (let i = 0; i < 4; i++) {
        vineYPositions.forEach(y => {
            horizontalVines.push({ x: i * 320, y: y, width: 330 });
        });
    }

    // Decorative vertical vines hanging down directly from the top ceiling canopy
    for (let i = 0; i < 7; i++) {
        hangingVines.push({
            x: i * 160 + Math.random() * 50,
            y: 0,
                      length: 120 + Math.random() * 160
        });
    }

    // Canopy leaf clusters filling the top ceiling
    for (let i = 0; i < 15; i++) {
        canopyLeaves.push({
            x: Math.random() * canvas.width,
            y: Math.random() * 35,
            radius: 50 + Math.random() * 25
        });
    }
}

function renderStaticScene() {
    ctx.fillStyle = "#0c1712";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Render Background Trees
    backgroundTrees.forEach(tree => {
        ctx.fillStyle = tree.color;
        ctx.fillRect(tree.x, 0, tree.width, canvas.height);
        ctx.fillStyle = "rgba(0,0,0,0.15)";
      ctx.fillRect(tree.x + 5, 0, tree.width * 0.15, canvas.height);
        ctx.fillRect(tree.x + tree.width - 8, 0, 4, canvas.height);
    });

    // Render Hanging Vertical Vines
    ctx.strokeStyle = "#1b4d22";
    ctx.lineWidth = 3.5;
    hangingVines.forEach(hv => {
        ctx.beginPath();
        ctx.moveTo(hv.x, hv.y);
        ctx.quadraticCurveTo(hv.x - 10, hv.y + hv.length * 0.5, hv.x, hv.y + hv.length);
        ctx.stroke();

        ctx.fillStyle = "#27ae60";
        ctx.beginPath();
        ctx.arc(hv.x - 6, hv.y + hv.length * 0.4, 5, 0, Math.PI * 2);
        ctx.arc(hv.x + 5, hv.y + hv.length * 0.7, 6, 0, Math.PI * 2);
        ctx.fill();
    });

    // Render Horizontal Path Vines
    ctx.strokeStyle = "#385e38";
    ctx.lineWidth = 10;
    horizontalVines.forEach(v => {
        ctx.beginPath();
        ctx.moveTo(v.x, v.y);
        ctx.lineTo(v.x + v.width, v.y);
        ctx.stroke();

        ctx.strokeStyle = "#1e381e";
        ctx.lineWidth = 2;
        ctx.beginPath();
        ctx.moveTo(v.x, v.y + 2);
        ctx.quadraticCurveTo(v.x + v.width * 0.5, v.y - 3, v.x + v.width, v.y + 2);
        ctx.stroke();
        ctx.strokeStyle = "#385e38"; 
        ctx.lineWidth = 10;
    });
  
    // Top Foliage Ceiling
    ctx.fillStyle = "#092412";
    canopyLeaves.forEach(leaf => {
        ctx.beginPath();
        ctx.arc(leaf.x, leaf.y, leaf.radius, 0, Math.PI * 2);
        ctx.fill();
    });
}

function handleInputEngine(e) {
    if (!gameActive) return;

    let oldLane = currentLane;
    if (e.key === "ArrowUp" && currentLane > 0) {
        currentLane--;
    }
    if (e.key === "ArrowDown" && currentLane < 2) {
        currentLane++;
    }
    
    if (oldLane !== currentLane) {
        monkey.targetY = vineYPositions[currentLane];
        
        // Log the jump instruction with a 1-second (1000ms) delay stamp for Michael
        chaser.inputQueue.push({
            targetY: vineYPositions[currentLane],
            executeAt: Date.now() + 1000
        });
    }
}

function spawnEntities() {
    const randomLane = Math.floor(Math.random() * 3);
    const targetY = vineYPositions[randomLane];
      const spawnX = canvas.width + 80;

    if (Math.random() > 0.4) {
        // High density bananas
        bananaCache.push({ x: spawnX, y: targetY - 18, width: 22, height: 22 });
        if (Math.random() > 0.3) {
            bananaCache.push({ x: spawnX + 35, y: targetY - 18, width: 22, height: 22 });
        }
    } else {
        // High density gap obstacles
        if (!horizontalVines.some(v => v.isGap && Math.abs(v.x - spawnX) < 130)) {
            horizontalVines.push({
                x: spawnX,
                y: targetY,
                width: 110, 
                isGap: true
            });
        }
    }
}

function drawRealisticMonkey(ctx, m) {
    m.animTime += 0.24;
    const limbSwing = Math.sin(m.animTime);

    ctx.save();
    ctx.translate(m.x, m.y - 18);

    // High-Arching Tail
    ctx.strokeStyle = "#52361b"; 
    ctx.lineWidth = 4;
    ctx.lineCap = "round";
    ctx.beginPath();
    ctx.moveTo(-16, 2);
    ctx.bezierCurveTo(-26, -18, -12, -32, -28, -26);
    ctx.stroke();
  
    // Climbing Limbs
  ctx.fillStyle = "#3d2712"; 
    ctx.fillRect(-12 - limbSwing * 4, 12, 6, 10); 
    ctx.fillRect(10 + limbSwing * 4, 12, 6, 10);  
    ctx.fillRect(-6 + limbSwing * 3, -12, 6, 9);  
    ctx.fillRect(4 - limbSwing * 3, -10, 6, 9);   

    // Torso Body
    ctx.fillStyle = "#52361b";
    ctx.beginPath();
    ctx.ellipse(0, 2, 17, 12, 0.1, 0, Math.PI * 2);
    ctx.fill();
    ctx.fillStyle = "#664422";
    ctx.beginPath();
    ctx.ellipse(-3, 0, 12, 8, 0, 0, Math.PI * 2);
    ctx.fill();

    // Head profile with ears
    ctx.fillStyle = "#52361b";
    ctx.beginPath(); ctx.arc(-8, -10, 4.5, 0, Math.PI * 2); ctx.fill();
    ctx.fillStyle = "#d1a884"; 
    ctx.beginPath(); ctx.arc(-8, -10, 2.5, 0, Math.PI * 2); ctx.fill();

    ctx.fillStyle = "#52361b";
    ctx.beginPath(); ctx.arc(6, -6, 10, 0, Math.PI * 2); ctx.fill();

    ctx.fillStyle = "#d1a884";
    ctx.beginPath();
    ctx.ellipse(8, -4, 7, 5.5, 0, 0, Math.PI * 2); 
    ctx.ellipse(5, -8, 5, 4.5, 0, 0, Math.PI * 2); 
    ctx.fill();

    ctx.fillStyle = "#111111";
    ctx.beginPath(); ctx.arc(6, -8, 1.5, 0, Math.PI * 2); ctx.fill();

    ctx.fillStyle = "#2d1b0d";
    ctx.fillRect(12, -5, 2, 2);
    ctx.restore();
}

function drawMichaelJacksonChaser(ctx, c, showSpeechBubble = false) {
    ctx.save();
    ctx.translate(c.x, c.y - 82);

    const stepFactor = Math.sin(Date.now() * (0.003 * scrollSpeed));

    // 1. Black Pants & Moving Legs
    ctx.fillStyle = "#111111";
    ctx.fillRect(-10, 52, 9, 32 + stepFactor * 4);
    ctx.fillRect(2, 52, 9, 32 - stepFactor * 4);
    
    // White Socks and Shoes
    ctx.fillStyle = "#ffffff";
    ctx.fillRect(-10, 50, 9, 3);
    ctx.fillRect(2, 50, 9, 3);
    ctx.fillStyle = "#111111";
    ctx.fillRect(-12, 84 + stepFactor * 4, 12, 6);
    ctx.fillRect(0, 84 - stepFactor * 4, 12, 6);

    // 2. Red Leather Jacket with V-Neck Zipper Trim
    ctx.fillStyle = "#b31e1e"; 
    ctx.fillRect(-14, 2, 28, 52);
    
    // Upward V-Neck Zipper Accent
    ctx.fillStyle = "#111111";
    ctx.beginPath();
    ctx.moveTo(-9, 2);   
    ctx.lineTo(0, 24);   
    ctx.lineTo(9, 2);    
    ctx.lineTo(5, 2);    
    ctx.lineTo(0, 17);   
    ctx.lineTo(-5, 2);   
    ctx.closePath();
    ctx.fill();
  

    // White Shirt fill
    ctx.fillStyle = "#ffffff";
    ctx.beginPath();
    ctx.moveTo(-4, 2);
    ctx.lineTo(4, 2);
    ctx.lineTo(0, 13);
    ctx.closePath();
    ctx.fill();

    // 3. Side Arms (With iconic single white glove)
    ctx.fillStyle = "#b31e1e";
    ctx.fillRect(-17, 8, 6, 25 - stepFactor * 3); 
    ctx.fillRect(12, 8, 6, 25 + stepFactor * 3);  
    ctx.fillStyle = "#8a5a36"; 
    ctx.fillRect(-17, 33 - stepFactor * 3, 6, 5); 
    ctx.fillStyle = "#ffffff";
    ctx.fillRect(12, 33 + stepFactor * 3, 6, 5);  

    // 4. Head Profile (Darker skin tone, NO HAT)
    ctx.fillStyle = "#8a5a36"; 
    ctx.beginPath();
    ctx.roundRect(-9, -24, 18, 26, [5, 5, 2, 2]);
    ctx.fill();

    // 5. SHORTER CURLY BROWN HAIR
    ctx.fillStyle = "#5c3a21"; 
    ctx.beginPath();
    // Compact crop clusters that frame the neck tightly instead of dropping down to the shoulders
    ctx.arc(-9, -18, 6, 0, Math.PI * 2);
    ctx.arc(-10, -10, 6, 0, Math.PI * 2);
    ctx.arc(-8, -3, 5.5, 0, Math.PI * 2);
    ctx.arc(-4, -22, 5, 0, Math.PI * 2);
    ctx.arc(2, -24, 4, 0, Math.PI * 2);
    ctx.fill();

    // Face features
    ctx.strokeStyle = "#6b4426";
    ctx.lineWidth = 1.5;
    ctx.beginPath(); ctx.moveTo(7, -10); ctx.lineTo(11, -10); ctx.stroke(); 

    // 6. REALISTIC BLACK SUNGLASSES LAYER
    ctx.fillStyle = "#161616"; // Lens body
    ctx.beginPath();
    ctx.roundRect(1, -17, 10, 6, 2);
    ctx.fill();
    // Metal glass frame bridge bar over the nose
    ctx.strokeStyle = "#080808";
    ctx.lineWidth = 1.8;
    ctx.beginPath();
    ctx.moveTo(1, -14);
    ctx.lineTo(-9, -13); // Glasses arm wrapping back behind ear curls
    ctx.stroke();

    // 7. Dynamic Speech Bubble Engine
    if (showSpeechBubble) {
        ctx.restore(); 
        ctx.save();
        ctx.translate(c.x + 30, c.y - 125);

        ctx.fillStyle = "#ffffff";
        ctx.strokeStyle = "#111111";
        ctx.lineWidth = 2.5;
        ctx.beginPath();
        ctx.roundRect(0, 0, 195, 38, 8);
        ctx.fill();
        ctx.stroke();

        ctx.beginPath();
        ctx.moveTo(35, 38);
        ctx.lineTo(25, 48);
ctx.lineTo(45, 38);
        ctx.closePath();
        ctx.fill();
        ctx.stroke();
        
        ctx.strokeStyle = "#ffffff";
        ctx.beginPath(); ctx.moveTo(32, 38); ctx.lineTo(43, 38); ctx.stroke();

        ctx.fillStyle = "#111111";
        ctx.font = "bold 14px 'Segoe UI', Arial, sans-serif";
        ctx.fillText("Bubbles, come back here!", 12, 24);
        ctx.restore();
        ctx.save();
        ctx.translate(c.x, c.y - 82);
    }

    ctx.restore();
}

function drawBanana(ctx, b) {
    ctx.save();
    ctx.translate(b.x, b.y);
    ctx.strokeStyle = "#f1c40f";
    ctx.lineWidth = 4.5;
    ctx.lineCap = "round";
    ctx.beginPath();
    ctx.arc(0, 0, 9, 0.3, Math.PI - 0.3);
    ctx.stroke();
    ctx.fillStyle = "#4a310d";
    ctx.fillRect(6, 5, 2.5, 2.5);
    ctx.restore();
}

function renderMenuLoop() {
    if (gameActive) return;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    renderStaticScene();

    drawMichaelJacksonChaser(ctx, chaser, true); 
    drawRealisticMonkey(ctx, monkey);

    requestAnimationFrame(renderMenuLoop);
}

function startGame() {
    gameActive = true;
    distanceScore = 0;
    bananasCollected = 0;
    scrollSpeed = baseSpeed;
    currentLane = 1;
    runStartTime = Date.now(); // Mark active run start timestamp

    monkey.y = vineYPositions[1];
    monkey.targetY = vineYPositions[1];
    chaser.y = vineYPositions[1];
    chaser.targetY = vineYPositions[1];
    chaser.inputQueue = []; // Reset jump memory log
    
    scoreVal.innerText = distanceScore;
    bananaCountVal.innerText = bananasCollected;

    initJungleWorld();

    startScreen.style.display = 'none';
    gameOverScreen.style.display = 'none';

    window.addEventListener('keydown', handleInputEngine);
    requestAnimationFrame(gameLoop);
}

function triggerGameOver() {
    gameActive = false;
    finalScore.innerText = Math.floor(distanceScore);
    finalBananas.innerText = bananasCollected;
    gameOverScreen.style.display = 'flex';
    
    setTimeout(() => {
        if (!gameActive) {
            renderMenuLoop();
        }
    }, 50);
}

function gameLoop() {
    if (!gameActive) return;

    const currentTime = Date.now();
  
    // Process Michael's Delayed Step Input Queue (1-second time offset filter)
    if (chaser.inputQueue.length > 0) {
        if (currentTime >= chaser.inputQueue[0].executeAt) {
            chaser.targetY = chaser.inputQueue[0].targetY;
            chaser.inputQueue.shift(); // Remove action once triggered
        }
    }

    // Smooth vertical transitions to the current target vine lane
    monkey.y += (monkey.targetY - monkey.y) * 0.24;
    chaser.y += (chaser.targetY - chaser.y) * 0.18; // Slightly faster snap to feel like a jump landing

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    renderStaticScene();

    // Scroll Background Lists of Trees 
    backgroundTrees.forEach(tree => {
        tree.x -= scrollSpeed * tree.speedFactor;
        if (tree.x < -tree.width) tree.x = canvas.width + Math.random() * 60;
    });
  
    // Scroll Ceiling Canopy Foliage
    canopyLeaves.forEach(leaf => {
        leaf.x -= scrollSpeed * 0.2;
        if (leaf.x < -leaf.radius) leaf.x = canvas.width + leaf.radius;
    });

    // Scroll Hanging Ceilings Vines
    hangingVines.forEach(hv => {
        hv.x -= scrollSpeed * 0.35;
        if (hv.x < -30) hv.x = canvas.width + 50 + Math.random() * 40;
    });

    // Generation frequency loop
    if (Math.random() < 0.05 && bananaCache.length < 15) {
        spawnEntities();
    }

    // Scroll & Track Vine Lanes (Process gaps mechanics)
    let monkeyFellIntoGap = false;

    for (let i = horizontalVines.length - 1; i >= 0; i--) {
        let v = horizontalVines[i];
        v.x -= scrollSpeed;
      
        if (v.isGap) {
            ctx.fillStyle = "#0c1712"; 
            ctx.fillRect(v.x, v.y - 8, v.width, 16);

            // COLLISION MATRIX
            if (monkey.targetY === v.y) {
                if (monkey.x > v.x - 12 && monkey.x < v.x + v.width - 12) {
                    monkeyFellIntoGap = true;
                }
            }
        }

        if (v.x < -350) {
            horizontalVines.splice(i, 1);
            if (!v.isGap) {
                horizontalVines.push({ x: canvas.width, y: v.y, width: 320 });
            }
        }
    }

    if (monkeyFellIntoGap) {
        triggerGameOver();
        return;
    }
