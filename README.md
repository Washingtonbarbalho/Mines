<!DOCTYPE html>
<html>
<head>
    <title>GERADOR MINES</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            height: 100vh;
            margin: 0;
            font-family: Arial, sans-serif;
            padding: 20px;
        }

        h1 {
            text-align: center;
            margin-bottom: 20px;
        }

        table {
            border-collapse: separate;
            border-spacing: 10px;
            margin-bottom: 20px;
        }

        td {
            width: 40px;
            height: 40px;
            border: 1px solid #999;
            text-align: center;
            font-weight: bold;
            font-size: 20px;
            background-color: darkblue;
            color: #ddd;
            border-radius: 8px;
            box-shadow: 0 2px 2px rgba(0, 0, 0, 0.3), 0 4px 4px rgba(0, 0, 0, 0.2);
            transition: background-color 0.3s;
            position: relative;
            pointer-events: none;
        }

        td.revealed {
            background-color: #DA760E;
            color: #000000;
        }

        td.revealed .star {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 60%;
            height: 60%;
            background-color: #ffffff;
            clip-path: polygon(50% 0%, 35% 35%, 0% 35%, 25% 60%, 10% 95%, 50% 75%, 90% 95%, 75% 60%, 100% 35%, 65% 35%);
        }

        button {
            display: inline-block;
            padding: 10px 20px;
            font-size: 16px;
            font-weight: bold;
            text-transform: uppercase;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 4px;
            transition: background-color 0.3s;
        }

        button:hover {
            background-color: #45a049;
            cursor: pointer;
        }

        #recommendations {
            text-align: center;
            margin-top: 10px;
            font-weight: bold;
            color: #666;
        }

        /* Nova animação */
        @keyframes spin {
            0% {
                transform: rotate(0deg);
            }
            100% {
                transform: rotate(360deg);
            }
        }

        .loading {
            display: inline-block;
            width: 16px;
            height: 16px;
            border: 4px solid #fff;
            border-top-color: #000;
            border-radius: 50%;
            animation: spin 1s infinite linear;
        }
    </style>
</head>
<body>
    <h1>GERADOR MINES</h1>
    <div style="overflow-x: auto;">
        <table id="board"></table>
    </div>
    <button onclick="analyzeSignal()">ANALISAR SINAL</button>
    <div id="recommendations"></div>

    <script>
        var boardSize = 5;
        var numBombs = 4;
        var bombs = [];
        var revealedCount = 0;
        var board = [];

        function initializeBoard() {
            board = [];
            for (var i = 0; i < boardSize; i++) {
                board[i] = [];
                for (var j = 0; j < boardSize; j++) {
                    board[i][j] = { bomb: false, revealed: false };
                }
            }
        }

        function placeBombs() {
            var bombCount = 0;

            while (bombCount < numBombs) {
                var row = Math.floor(Math.random() * boardSize);
                var col = Math.floor(Math.random() * boardSize);

                if (!board[row][col].bomb) {
                    board[row][col].bomb = true;
                    bombs.push({ row: row, col: col });
                    bombCount++;
                }
            }
        }

        function createBoard() {
            var boardElement = document.getElementById('board');
            boardElement.innerHTML = '';

            for (var i = 0; i < boardSize; i++) {
                var row = document.createElement('tr');
                for (var j = 0; j < boardSize; j++) {
                    var cell = document.createElement('td');
                    cell.setAttribute('data-row', i);
                    cell.setAttribute('data-col', j);
                    row.appendChild(cell);
                }
                boardElement.appendChild(row);
            }
        }

        function revealCell(row, col) {
            var cell = document.querySelector('[data-row="' + row + '"][data-col="' + col + '"]');
            cell.classList.add('revealed');
            cell.innerHTML = '<div class="star"></div>';
            board[row][col].revealed = true;
            revealedCount++;

            if (board[row][col].bomb) {
                endGame(false);
            } else if (revealedCount === (boardSize * boardSize) - numBombs) {
                endGame(true);
            }
        }

        function analyzeSignal() {
            var analyzeButton = document.querySelector('button');
            analyzeButton.disabled = true;
            analyzeButton.innerHTML = '<span class="loading"></span> Analisando melhor jogada...';

            setTimeout(function() {
                resetBoard();
                createBoard();
                var emptyCells = getEmptyCells();

                for (var i = 0; i < 4; i++) {
                    var randomIndex = Math.floor(Math.random() * emptyCells.length);
                    var cell = emptyCells[randomIndex];
                    revealCell(cell.row, cell.col);
                    emptyCells.splice(randomIndex, 1);
                }

                analyzeButton.disabled = false;
                analyzeButton.innerText = 'ANALISAR SINAL';

                showRecommendations();
            }, 5000);
        }

        function resetBoard() {
            bombs = [];
            revealedCount = 0;
            initializeBoard();
            placeBombs();
        }

        function getEmptyCells() {
            var emptyCells = [];

            for (var i = 0; i < boardSize; i++) {
                for (var j = 0; j < boardSize; j++) {
                    if (!board[i][j].bomb && !board[i][j].revealed) {
                        emptyCells.push({ row: i, col: j });
                    }
                }
            }

            return emptyCells;
        }

        function endGame(isWin) {
            var message = isWin ? 'Você ganhou!' : 'Você perdeu!';
            alert(message);
            analyzeSignal();
        }

        function showRecommendations() {
            var recommendationsElement = document.getElementById('recommendations');
            recommendationsElement.innerHTML = '';

            var bombCount = numBombs;
            var attemptCount = 3;

            var recommendationsText = 'Minas: ' + bombCount + ' 💣<br>';
            recommendationsText += 'Tentativas: ' + attemptCount + ' ⭐<br>';
            recommendationsText += 'Obs.: Se perder em ' + attemptCount + ' sinais seguidos é hora de dar uma pausa e fazer gestão de banca!';

            recommendationsElement.innerHTML = recommendationsText;
        }

        initializeBoard();
        placeBombs();
        createBoard();
    </script>
</body>
</html>
