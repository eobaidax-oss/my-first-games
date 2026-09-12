<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة السلم والثعبان</title>
    <style>
        * { box-sizing: border-box; }
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #1e1e2e;
            color: white;
            margin: 0;
            padding: 10px;
        }

        #game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        #board {
            position: relative;
            width: 320px;
            height: 320px;
            display: grid;
            grid-template-columns: repeat(10, 1fr);
            grid-template-rows: repeat(10, 1fr);
            border: 3px solid #ffcc00;
            border-radius: 8px;
            background-color: #2b2b3d;
        }

        .cell {
            border: 1px solid #444;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 10px;
            font-weight: bold;
            position: relative;
        }

        .even-cell { background-color: #3b3b52; color: #ddd; }
        .odd-cell { background-color: #4a4a68; color: #fff; }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            pointer-events: none;
            z-index: 1;
        }

        .player {
            width: 18px;
            height: 18px;
            border-radius: 50%;
            position: absolute;
            z-index: 2;
            transition: all 0.4s ease;
            border: 2px solid white;
            box-shadow: 0 0 5px rgba(0,0,0,0.8);
        }

        #p1 { background-color: #ff3366; }
        #p2 { background-color: #33ccff; }

        #controls {
            margin-top: 15px;
            background: #2b2b3d;
            padding: 15px;
            border-radius: 10px;
            width: 320px;
        }

        button {
            background-color: #ffcc00;
            color: #121212;
            border: none;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 5px;
            cursor: pointer;
        }

        #status { margin-top: 10px; font-size: 14px; }
        #dice-result { font-size: 24px; margin: 5px 0; }
    </style>
</head>
<body>

    <h2>🐍 لعبة السلم والثعبان 🪜</h2>
    
    <div id="game-container">
        <div id="board">
            <canvas id="canvas" width="320" height="320"></canvas>
            <div id="p1" class="player"></div>
            <div id="p2" class="player"></div>
        </div>

        <div id="controls">
            <div id="status">دور: <span id="current-player" style="color: #ff3366;">اللاعب 1 (الأحمر)</span></div>
            <div id="dice-result">🎲 0</div>
            <button onclick="rollDice()">إرمِ النرد!</button>
        </div>
    </div>

    <script>
        const boardSize = 10;
        const cellSize = 32;
        const board = document.getElementById("board");
        const canvas = document.getElementById("canvas");
        const ctx = canvas.getContext("2d");

        // مواقع الثعابين (من -> إلى)
        const snakes = { 99: 54, 70: 55, 52: 29, 25: 2, 95: 72 };
        // مواقع السلالام (من -> إلى)
        const ladders = { 6: 27, 11: 40, 21: 59, 35: 87, 60: 82 };

        let positions = [1, 1]; // مواضع اللاعبين
        let turn = 0; // 0 للاعب 1، 1 للاعب 2

        // إنشاء لوحة المربعات
        for (let row = 0; row < boardSize; row++) {
            for (let col = 0; col < boardSize; col++) {
                let cellNum;
                let actualRow = 9 - row;
                if (actualRow % 2 === 0) {
                    cellNum = actualRow * 10 + col + 1;
                } else {
                    cellNum = actualRow * 10 + (9 - col) + 1;
                }

                const cell = document.createElement("div");
                cell.className = "cell " + ((row + col) % 2 === 0 ? "even-cell" : "odd-cell");
                cell.innerText = cellNum;
                cell.id = "cell-" + cellNum;
                board.appendChild(cell);
            }
        }

        // إرجاع الإحداثيات الشاشة للمربع
        function getCellPos(num) {
            let row = Math.floor((num - 1) / 10);
            let col = (num - 1) % 10;
            if (row % 2 !== 0) col = 9 - col;
            let x = col * cellSize + cellSize / 2;
            let y = (9 - row) * cellSize + cellSize / 2;
            return { x, y };
        }

        // رسم السلالم والثعابين
        function drawBoardElements() {
            ctx.clearRect(0, 0, 320, 320);

            // رسم السلالم (خطوط خضراء ومدرجة)
            for (let start in ladders) {
                let p1 = getCellPos(start);
                let p2 = getCellPos(ladders[start]);

                ctx.strokeStyle = "#4caf50";
                ctx.lineWidth = 6;
                ctx.beginPath();
                ctx.moveTo(p1.x, p1.y);
                ctx.lineTo(p2.x, p2.y);
                ctx.stroke();

                // عوارض السلم
                ctx.strokeStyle = "#81c784";
                ctx.lineWidth = 2;
                for (let i = 0; i <= 1; i += 0.2) {
                    let rx = p1.x + (p2.x - p1.x) * i;
                    let ry = p1.y + (p2.y - p1.y) * i;
                    ctx.beginPath();
                    ctx.arc(rx, ry, 4, 0, Math.PI * 2);
                    ctx.stroke();
                }
            }

            // رسم الثعابين (خطوط حمراء مموجة)
            for (let start in snakes) {
                let p1 = getCellPos(start);
                let p2 = getCellPos(snakes[start]);

                ctx.strokeStyle = "#f44336";
                ctx.lineWidth = 5;
                ctx.beginPath();
                ctx.moveTo(p1.x, p1.y);
                let midX = (p1.x + p2.x) / 2 + 15;
                let midY = (p1.y + p2.y) / 2 - 15;
                ctx.quadraticCurveTo(midX, midY, p2.x, p2.y);
                ctx.stroke();

                // رأس الثعبان
                ctx.fillStyle = "#d32f2f";
                ctx.beginPath();
                ctx.arc(p1.x, p1.y, 6, 0, Math.PI * 2);
                ctx.fill();
            }
        }

        // تحديث أرقام ومواضع بيادق اللاعبين
        function updatePlayers() {
            for (let i = 0; i < 2; i++) {
                let pos = getCellPos(positions[i]);
                let pElem = document.getElementById("p" + (i + 1));
                pElem.style.left = (pos.x - 9 + (i * 4)) + "px";
                pElem.style.top = (pos.y - 9) + "px";
            }
        }

        function rollDice() {
            let dice = Math.floor(Math.random() * 6) + 1;
            document.getElementById("dice-result").innerText = "🎲 " + dice;

            let currentPos = positions[turn] + dice;

            if (currentPos <= 100) {
                positions[turn] = currentPos;

                // التثبت من وجود سلم أو ثعبان
                if (snakes[positions[turn]]) {
                    positions[turn] = snakes[positions[turn]];
                } else if (ladders[positions[turn]]) {
                    positions[turn] = ladders[positions[turn]];
                }
            }

            updatePlayers();

            if (positions[turn] === 100) {
                alert("🎉 فاز " + (turn === 0 ? "اللاعب 1 (الأحمر)" : "اللاعب 2 (الأزرق)") + "!");
                positions = [1, 1];
                updatePlayers();
                return;
            }

            // تبديل الأدوار
            turn = turn === 0 ? 1 : 0;
            let playerText = document.getElementById("current-player");
            if (turn === 0) {
                playerText.innerText = "اللاعب 1 (الأحمر)";
                playerText.style.color = "#ff3366";
            } else {
                playerText.innerText = "اللاعب 2 (الأزرق)";
                playerText.style.color = "#33ccff";
            }
        }

        // بدء التشغيل الرسومي
        drawBoardElements();
        updatePlayers();
    </script>
</body>
</html>
