DTA hacking ethical
<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DTA Battle Royale - Demo</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #0b0f19; color: #fff; overflow: hidden; height: 100vh; display: flex; flex-direction: column; align-items: center; justify-content: center; }
        
        #game-container { position: relative; width: 100%; max-width: 450px; height: 100%; max-height: 800px; background: linear-gradient(135deg, #111827, #1f2937); border: 2px solid #00ffcc; box-shadow: 0 0 20px rgba(0, 255, 204, 0.4); display: flex; flex-direction: column; justify-content: space-between; }

        /* Lobby Screen */
        #lobby-screen { position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(11, 15, 25, 0.95); display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 10; text-align: center; padding: 20px; }
        #lobby-screen h1 { color: #00ffcc; font-size: 28px; margin-bottom: 10px; text-shadow: 0 0 10px rgba(0,255,204,0.5); }
        #lobby-screen p { color: #9ca3af; margin-bottom: 30px; font-size: 14px; }
        .btn { background: #00ffcc; color: #0b0f19; border: none; padding: 12px 30px; font-size: 16px; font-weight: bold; border-radius: 25px; cursor: pointer; box-shadow: 0 0 15px rgba(0,255,204,0.4); transition: 0.3s; }
        .btn:hover { background: #00b388; box-shadow: 0 0 25px rgba(0,255,204,0.8); }

        /* Game HUD */
        #hud { display: none; padding: 15px; display: flex; justify-content: space-between; align-items: center; background: rgba(0,0,0,0.5); border-bottom: 1px solid #374151; z-index: 5; }
        .hud-item { font-size: 14px; color: #00ffcc; font-weight: bold; }

        /* Play Area / Canvas */
        #play-area { flex: 1; position: relative; overflow: hidden; background: #0d1b2a; }
        
        /* Airplane animation */
        #airplane { position: absolute; top: 50px; left: -100px; font-size: 30px; transition: 0.1s linear; z-index: 4; }
        
        /* Player */
        #player { position: absolute; width: 30px; height: 30px; background: #00ffcc; border-radius: 50%; box-shadow: 0 0 10px #00ffcc; display: none; align-items: center; justify-content: center; font-size: 12px; font-weight: bold; color: #000; z-index: 3; }

        /* Controls */
        #controls { display: none; padding: 15px; background: rgba(0,0,0,0.6); display: flex; justify-content: space-around; align-items: center; z-index: 5; }
        .control-btn { background: #1f2937; border: 1px solid #00ffcc; color: #00ffcc; width: 60px; height: 60px; border-radius: 50%; font-size: 18px; display: flex; align-items: center; justify-content: center; cursor: pointer; user-select: none; }
        .control-btn:active { background: #00ffcc; color: #0b0f19; }
        
        #message-box { position: absolute; top: 40%; width: 100%; text-align: center; font-size: 20px; font-weight: bold; color: #f59e0b; text-shadow: 0 0 10px rgba(0,0,0,0.8); z-index: 6; pointer-events: none; }
    </style>
</head>
<body>

    <div id="game-container">
        <!-- Lobby -->
        <div id="lobby-screen">
            <h1>DTA BATTLEGROUNDS</h1>
            <p>Free Fire Style Mini Demo</p>
            <button class="btn" onclick="startMatch()">MATCH START</button>
        </div>

        <!-- HUD -->
        <div id="hud" style="display:none;">
            <div class="hud-item">MAP: BERMUDA LITE</div>
            <div class="hud-item" id="status-text">LOBBY</div>
        </div>

        <!-- Play Area -->
        <div id="play-area">
            <div id="message-box"></div>
            <div id="airplane">✈️</div>
            <div id="player">DTA</div>
        </div>

        <!-- Controls -->
        <div id="controls" style="display:none;">
            <div class="control-btn" onclick="movePlayer('left')">⬅️</div>
            <div class="control-btn" onclick="jumpPlane()" id="action-btn">🪂 JUMP</div>
            <div class="control-btn" onclick="movePlayer('right')">➡️</div>
        </div>
    </div>

    <script>
        let gameState = 'lobby'; // lobby, flying, landed
        let playerX = 200;
        let playerY = 300;
        let planeX = -50;
        let planeInterval;

        function startMatch() {
            document.getElementById('lobby-screen').style.display = 'none';
            document.getElementById('hud').style.display = 'flex';
            document.getElementById('controls').style.display = 'flex';
            document.getElementById('player').style.display = 'flex';
            
            gameState = 'flying';
            document.getElementById('status-text').innerText = 'IN AIRPLANE ✈️';
            document.getElementById('action-btn').innerText = '🪂 JUMP';
            document.getElementById('message-box').innerText = 'जहाज उड़ रहा है! कूदने के लिए JUMP दबाएं।';

            startAirplaneFly();
        }

        function startAirplaneFly() {
            let plane = document.getElementById('airplane');
            planeX = -50;
            planeInterval = setInterval(() => {
                if (gameState === 'flying') {
                    planeX += 4;
                    plane.style.left = planeX + 'px';
                    if (planeX > 450) planeX = -50; // Loop flight
                }
            }, 30);
        }

        function jumpPlane() {
            if (gameState === 'flying') {
                clearInterval(planeInterval);
                document.getElementById('airplane').style.display = 'none';
                gameState = 'landed';
                document.getElementById('status-text').innerText = 'FIGHTING ZONE 🟢';
                document.getElementById('action-btn').innerText = '🔥 FIRE';
                document.getElementById('message-box').innerText = 'आप मैप पर उतर चुके हैं! अब आगे बढ़ें।';
                
                playerX = 200;
                playerY = 400;
                updatePlayerPos();
            } else if (gameState === 'landed') {
                // Fire action demo
                let msg = document.getElementById('message-box');
                msg.innerText = '💥 BOOM! DTA Attack!';
                setTimeout(() => { if(gameState==='landed') msg.innerText = ''; }, 1000);
            }
        }

        function movePlayer(dir) {
            if (gameState === 'landed') {
                if (dir === 'left' && playerX > 20) playerX -= 25;
                if (dir === 'right' && playerX < 400) playerX += 25;
                updatePlayerPos();
            }
        }

        function updatePlayerPos() {
            let player = document.getElementById('player');
            player.style.left = playerX + 'px';
            player.style.bottom = '50px';
        }
    </script>
</body>
</html>
