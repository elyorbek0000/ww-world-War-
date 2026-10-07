<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Generals RTS Game</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; user-select: none; }
        body { background-color: #111; color: #fff; font-family: sans-serif; overflow: hidden; }
        #game-container { position: relative; width: 100vw; height: 100vh; }
        canvas { display: block; background-color: #2e3b23; cursor: crosshair; }
        #ui-bar {
            position: absolute; bottom: 0; left: 0; width: 100%; height: 70px;
            background: rgba(0, 0, 0, 0.85); border-top: 2px solid #555;
            display: flex; justify-content: space-between; align-items: center; padding: 0 20px;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <canvas id="gameCanvas"></canvas>
        <div id="ui-bar">
            <div id="unit-info">Birlik tanlanmagan</div>
            <div id="resources">Pul: $1000 | Elektr: 100kW</div>
        </div>
    </div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        class Tank {
            constructor(x, y, team, name) {
                this.x = x; this.y = y; this.targetX = x; this.targetY = y;
                this.team = team; this.name = name; this.speed = 2;
                this.isSelected = false; this.radius = 18;
            }
            update() {
                let dx = this.targetX - this.x, dy = this.targetY - this.y;
                let dist = Math.hypot(dx, dy);
                if (dist > 3) {
                    this.x += (dx / dist) * this.speed;
                    this.y += (dy / dist) * this.speed;
                }
            }
            draw() {
                ctx.save();
                if (this.isSelected) {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.radius + 6, 0, Math.PI * 2);
                    ctx.strokeStyle = '#00ff00'; ctx.lineWidth = 3; ctx.stroke();
                }
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fillStyle = this.team === 'USA' ? '#3b82f6' : '#ef4444';
                ctx.fill(); ctx.lineWidth = 2; ctx.strokeStyle = '#fff'; ctx.stroke();
                ctx.restore();
            }
        }

        let units = [
            new Tank(100, 200, 'USA', 'Paladin Tank'),
            new Tank(100, 300, 'USA', 'Crusader Tank'),
            new Tank(280, 250, 'China', 'Overlord Tank')
        ];

        canvas.addEventListener('touchstart', (e) => {
            let touch = e.touches[0];
            let touchX = touch.clientX, touchY = touch.clientY;
            let selectedAny = false;

            units.forEach(u => {
                let dist = Math.hypot(u.x - touchX, u.y - touchY);
                if (dist < u.radius + 15) { u.isSelected = true; selectedAny = true; }
            });

            if (!selectedAny) {
                units.forEach(u => { if (u.isSelected) { u.targetX = touchX; u.targetY = touchY; } });
            }
        });

        function gameLoop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            units.forEach(u => { u.update(); u.draw(); });
            requestAnimationFrame(gameLoop);
        }
        gameLoop();
    </script>
</body>
</html>
