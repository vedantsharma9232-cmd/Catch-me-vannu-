<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Catch Me! // Vannu & Vedant</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; user-select: none; }
        body {
            background-color: #020205;
            color: #00ffcc;
            font-family: 'Orbitron', sans-serif;
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
        }
        canvas { position: absolute; top: 0; left: 0; z-index: 1; }
        .hud {
            position: relative;
            z-index: 2;
            width: 100%;
            display: flex;
            justify-content: space-between;
            padding: 15px 20px;
            background: rgba(0, 0, 0, 0.85);
            border-bottom: 2px solid #ff0055;
            box-shadow: 0 0 15px rgba(255, 0, 85, 0.4);
            font-size: 0.9rem;
        }
        .hud span { color: #ff0055; font-weight: bold; }
        .game-title {
            position: relative;
            z-index: 2;
            text-align: center;
            margin-top: 10px;
            pointer-events: none;
        }
        .game-title h1 {
            font-size: 1.3rem;
            color: #fff;
            text-shadow: 0 0 10px #00ffcc, 0 0 20px #ff0055;
            letter-spacing: 2px;
        }
        .footer-credit {
            position: relative;
            z-index: 2;
            padding: 12px;
            font-size: 0.8rem;
            background: rgba(0, 0, 0, 0.85);
            width: 100%;
            text-align: center;
            border-top: 1px dashed #00ffcc;
        }
        .footer-credit a { color: #ff0055; text-decoration: none; }
        
        .overlay {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(2, 2, 5, 0.94);
            z-index: 10;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
            padding: 25px;
        }
        .overlay h2 { font-size: 1.8rem; color: #ff0055; margin-bottom: 12px; text-shadow: 0 0 15px #ff0055; }
        .overlay p { font-size: 0.95rem; color: #00ffcc; margin-bottom: 25px; line-height: 1.6; }
        .start-btn {
            background: #ff0055;
            color: #fff;
            border: none;
            padding: 14px 35px;
            font-family: 'Orbitron', sans-serif;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            border-radius: 8px;
            box-shadow: 0 0 20px #ff0055;
            transition: 0.2s;
        }
        .start-btn:active { transform: scale(0.95); }
    </style>
</head>
<body>

    <div class="overlay" id="start-screen">
        <h2>CATCH ME! 🎯</h2>
        <p>Vannu, Jaldi se falling hearts aur special items catch karo aur Vedant ka score cross karke dikhao!<br><br><b>Tap & Drag to Move!</b></p>
        <button class="start-btn" onclick="startGame()">START GAME 🚀</button>
    </div>

    <div class="hud">
        <div>SCORE: <span id="score">0</span></div>
        <div>VANNU & VEDANT</div>
    </div>

    <div class="game-title">
        <h1>CATCH ME SPECIAL</h1>
    </div>

    <canvas id="gameCanvas"></canvas>

    <div class="footer-credit">
        Creator: <b>Vedant</b>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let width = canvas.width = window.innerWidth;
        let height = canvas.height = window.innerHeight - 120;

        window.addEventListener('resize', () => {
            width = canvas.width = window.innerWidth;
            height = canvas.height = window.innerHeight - 120;
        });

        let score = 0;
        let isPlaying = false;

        let player = {
            x: width / 2 - 45,
            y: height - 60,
            w: 90,
            h: 22,
            color: '#ff0055'
        };

        let items = [];

        function startGame() {
            document.getElementById('start-screen').style.display = 'none';
            isPlaying = true;
            score = 0;
            items = [];
            loop();
        }

        window.addEventListener('mousemove', (e) => {
            player.x = e.clientX - player.w / 2;
        });
        window.addEventListener('touchmove', (e) => {
            player.x = e.touches[0].clientX - player.w / 2;
        });

        function spawnItem() {
            if (Math.random() < 0.045) {
                const types = ['❤️', '💖', '🎁', '✨'];
                items.push({
                    x: Math.random() * (width - 30),
                    y: -30,
                    size: 28,
                    speed: Math.random() * 3.5 + 2,
                    text: types[Math.floor(Math.random() * types.length)]
                });
            }
        }

        function update() {
            if (!isPlaying) return;
            spawnItem();
            for (let i = items.length - 1; i >= 0; i--) {
                items[i].y += items[i].speed;
                if (
                    items[i].x > player.x - 10 &&
                    items[i].x < player.x + player.w + 10 &&
                    items[i].y + items[i].size > player.y &&
                    items[i].y < player.y + player.h + 10
                ) {
                    score += 10;
                    document.getElementById('score').innerText = score;
                    items.splice(i, 1);
                    continue;
                }
                if (items[i].y > height) {
                    items.splice(i, 1);
                }
            }
        }

        function draw() {
            ctx.fillStyle = 'rgba(2, 2, 5, 0.35)';
            ctx.fillRect(0, 0, width, height);

            ctx.shadowBlur = 18;
            ctx.shadowColor = '#ff0055';
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.w, player.h);

            ctx.shadowBlur = 12;
            ctx.shadowColor = '#00ffcc';
            ctx.font = '26px sans-serif';
            for (let item of items) {
                ctx.fillText(item.text, item.x, item.y);
            }
            ctx.shadowBlur = 0;
        }

        function loop() {
            if (!isPlaying) return;
            update();
            draw();
            requestAnimationFrame(loop);
        }
    </script>
</body>
</html>
