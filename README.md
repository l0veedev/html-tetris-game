# HTML Tetris Game

A lightweight, high-performance open-source Tetris game built with pure HTML5 Canvas and JavaScript. Written in a single file with zero external dependencies or assets, featuring smooth swipe controls for mobile devices.

### How to Play
- Use the **Arrow Keys** or **W, A, S, D** to move and rotate the pieces on PC.
- On mobile devices: **Swipe Left/Right** to move, **Tap** to rotate, and **Swipe Down** to drop the piece instantly.
- Complete full horizontal lines to clear them and score points.

### Play Online
You can play the game instantly in your browser via [GitHub Pages](https://l0veedev.github.io/html-tetris-game/).

---

### Source Code (`index.html`)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML Tetris Game — Play Online | l0veedev</title>
    <meta name="description" content="A lightweight, high-performance open-source Tetris game built with pure HTML5 Canvas and JavaScript. Mobile-friendly with swipe controls!">
    <meta name="keywords" content="html tetris game, javascript tetris, retro arcade, indie pet project, l0veedev">
    <meta name="author" content="l0veedev">
    <style>
        body {
            margin: 0;
            padding: 20px;
            background: #0d0d0d;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            box-sizing: border-box;
            outline: none;
            color: #fff;
            font-family: sans-serif;
            user-select: none;
        }
        h1 {
            margin: 0 0 15px 0;
            font-size: 22px;
            letter-spacing: 2px;
            color: #e0e0e0;
        }
        canvas {
            background: #1a1a1a;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.8);
            border-radius: 4px;
            cursor: pointer;
            margin-bottom: 20px;
            touch-action: none;
        }
        .comments-container {
            width: 100%;
            max-width: 320px;
        }
    </style>
</head>
<body>
    <h1>TETRIS</h1>
    <canvas width="320" height="640" id="game"></canvas>

    <div class="comments-container">
        <script src="[https://giscus.app/client.js](https://giscus.app/client.js)"
                data-repo="l0veedev/html-snake-game"
                data-repo-id="R_kgDOVnXJAA"
                data-category="Announcements"
                data-category-id="DIC_kwDOVnXJAM4DKrfR"
                data-mapping="pathname"
                data-strict="0"
                data-reactions-enabled="1"
                data-emit-metadata="0"
                data-input-position="bottom"
                data-theme="dark"
                data-lang="ru"
                crossorigin="anonymous"
                async>
        </script>
    </div>

    <script>
        const canvas = document.getElementById('game');
        const context = canvas.getContext('2d');
        const grid = 32;
        const tetrominoSequence = [];
        const playfield = [];

        for (let row = -2; row < 20; row++) {
            playfield[row] = [];
            for (let col = 0; col < 10; col++) {
                playfield[row][col] = 0;
            }
        }

        const tetrominos = {
            'I': [[0,0,0,0],[1,1,1,1],[0,0,0,0],[0,0,0,0]],
            'J': [[1,0,0],[1,1,1],[0,0,0]],
            'L': [[0,0,1],[1,1,1],[0,0,0]],
            'O': [[1,1],[1,1]],
            'S': [[0,1,1],[1,1,0],[0,0,0]],
            'Z': [[1,1,0],[0,1,1],[0,0,0]],
            'T': [[0,1,0],[1,1,1],[0,0,0]]
        };

        const colors = {
            'I': '#00f0f0',
            'O': '#f0f000',
            'T': '#a000f0',
            'S': '#00f000',
            'Z': '#f00000',
            'J': '#0000f0',
            'L': '#f0a000'
        };

        function getRandomInt(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function generateSequence() {
            const sequence = ['I', 'J', 'L', 'O', 'S', 'T', 'Z'];
            while (sequence.length) {
                const rand = getRandomInt(0, sequence.length - 1);
                tetrominoSequence.push(sequence.splice(rand, 1)[0]);
            }
        }

        function getNextTetromino() {
            if (tetrominoSequence.length === 0) generateSequence();
            const name = tetrominoSequence.pop();
            const matrix = tetrominos[name];
            const col = playfield[0].length / 2 - Math.ceil(matrix[0].length / 2);
            const row = name === 'I' ? -1 : -2;
            return { name, matrix, row, col };
        }

        function rotate(matrix) {
            const N = matrix.length - 1;
            return matrix.map((row, i) => row.map((val, j) => matrix[N - j][i]));
        }

        function isValidMove(matrix, cellRow, cellCol) {
            for (let row = 0; row < matrix.length; row++) {
                for (let col = 0; col < matrix[row].length; col++) {
                    if (matrix[row][col] && (
                        cellCol + col < 0 ||
                        cellCol + col >= playfield[0].length ||
                        cellRow + row >= playfield.length ||
                        playfield[cellRow + row][cellCol + col]
                    )) {
                        return false;
                    }
                }
            }
            return true;
        }

        function placeTetromino() {
            for (let row = 0; row < tetromino.matrix.length; row++) {
                for (let col = 0; col < tetromino.matrix[row].length; col++) {
                    if (tetromino.matrix[row][col]) {
                        if (tetromino.row + row < 0) return showGameOver();
                        playfield[tetromino.row + row][tetromino.col + col] = tetromino.name;
                    }
                }
            }

            for (let row = playfield.length - 1; row >= 0; ) {
                if (playfield[row].every(cell => !!cell)) {
                    for (let r = row; r >= 0; r--) {
                        for (let c = 0; c < playfield[r].length; c++) {
                            playfield[r][c] = playfield[r-1] ? playfield[r-1][c] : 0;
                        }
                    }
                } else {
                    row--;
                }
            }
            tetromino = getNextTetromino();
        }

        function showGameOver() {
            cancelAnimationFrame(rAF);
            gameOver = true;
            context.fillStyle = 'black';
            context.globalAlpha = 0.75;
            context.fillRect(0, canvas.height / 2 - 30, canvas.width, 60);
            context.globalAlpha = 1;
            context.fillStyle = 'white';
            context.font = '36px monospace';
            context.textAlign = 'center';
            context.textBaseline = 'middle';
            context.fillText('GAME OVER!', canvas.width / 2, canvas.height / 2);
        }

        let count = 0;
        let tetromino = getNextTetromino();
        let rAF = null;
        let gameOver = false;

        function loop() {
            rAF = requestAnimationFrame(loop);
            context.clearRect(0, 0, canvas.width, canvas.height);

            for (let row = 0; row < 20; row++) {
                for (let col = 0; col < 10; col++) {
                    if (playfield[row][col]) {
                        context.fillStyle = colors[playfield[row][col]];
                        context.fillRect(col * grid, row * grid, grid - 1, grid - 1);
                    }
                }
            }

            if (tetromino) {
                if (++count > 35) {
                    tetromino.row++;
                    count = 0;
                    if (!isValidMove(tetromino.matrix, tetromino.row, tetromino.col)) {
                        tetromino.row--;
                        placeTetromino();
                    }
                }

                context.fillStyle = colors[tetromino.name];
                for (let row = 0; row < tetromino.matrix.length; row++) {
                    for (let col = 0; col < tetromino.matrix[row].length; col++) {
                        if (tetromino.matrix[row][col]) {
                            context.fillRect((tetromino.col + col) * grid, (tetromino.row + row) * grid, grid - 1, grid - 1);
                        }
                    }
                }
            }
        }

        window.addEventListener('keydown', function(e) {
            if (document.activeElement && document.activeElement.tagName === 'IFRAME') return;
            if (gameOver) return;

            if (e.code === 'ArrowLeft' || e.code === 'KeyA') {
                const col = tetromino.col - 1;
                if (isValidMove(tetromino.matrix, tetromino.row, col)) tetromino.col = col;
            }
            if (e.code === 'ArrowRight' || e.code === 'KeyD') {
                const col = tetromino.col + 1;
                if (isValidMove(tetromino.matrix, tetromino.row, col)) tetromino.col = col;
            }
            if (e.code === 'ArrowUp' || e.code === 'KeyW' || e.code === 'Space') {
                const matrix = rotate(tetromino.matrix);
                if (isValidMove(matrix, tetromino.row, tetromino.col)) tetromino.matrix = matrix;
            }
            if (e.code === 'ArrowDown' || e.code === 'KeyS') {
                const row = tetromino.row + 1;
                if (!isValidMove(tetromino.matrix, row, tetromino.col)) {
                    tetromino.row = row - 1;
                    placeTetromino();
                    return;
                }
                tetromino.row = row;
            }
            e.preventDefault();
        });

        let touchStartX = 0;
        let touchStartY = 0;
        let touchStartTime = 0;

        canvas.addEventListener('touchstart', e => {
            if (gameOver) return;
            touchStartX = e.touches[0].clientX;
            touchStartY = e.touches[0].clientY;
            touchStartTime = Date.now();
        }, { passive: true });

        canvas.addEventListener('touchend', e => {
            if (gameOver) return;
            let diffX = e.changedTouches[0].clientX - touchStartX;
            let diffY = e.changedTouches[0].clientY - touchStartY;
            let timeDiff = Date.now() - touchStartTime;

            if (Math.abs(diffX) < 20 && Math.abs(diffY) < 20 && timeDiff < 250) {
                const matrix = rotate(tetromino.matrix);
                if (isValidMove(matrix, tetromino.row, tetromino.col)) {
                    tetromino.matrix = matrix;
                }
                return;
            }

            if (Math.abs(diffX) > Math.abs(diffY)) {
                if (diffX > 30) {
                    const col = tetromino.col + 1;
                    if (isValidMove(tetromino.matrix, tetromino.row, col)) tetromino.col = col;
                } else if (diffX < -30) {
                    const col = tetromino.col - 1;
                    if (isValidMove(tetromino.matrix, tetromino.row, col)) tetromino.col = col;
                }
            } else {
                if (diffY > 40) {
                    while (isValidMove(tetromino.matrix, tetromino.row + 1, tetromino.col)) {
                        tetromino.row++;
                    }
                    placeTetromino();
                }
            }
        }, { passive: true });

        window.focus();
        window.addEventListener('click', () => window.focus());
        rAF = requestAnimationFrame(loop);
    </script>
</body>
</html>
