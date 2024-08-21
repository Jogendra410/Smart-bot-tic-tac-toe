<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dynamic Tic Tac Toe</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            font-family: 'Arial', sans-serif;
            overflow: hidden;
            text-align: center;
        }

        .title {
            font-size: 3rem;
            color: #ffffff;
            margin-bottom: 20px;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.8);
            animation: glow 1.5s ease-in-out infinite alternate;
        }

        .container {
            display: grid;
            grid-template-columns: repeat(3, 80px);
            grid-template-rows: repeat(3, 80px);
            gap: 10px;
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.5), 0 0 25px rgba(255, 255, 255, 0.3);
            background: rgba(255, 255, 255, 0.1);
            border: 2px solid #ffffff;
            transition: box-shadow 0.4s ease-in-out;
        }

        .container:hover {
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.7), 0 0 35px rgba(255, 255, 255, 0.5);
        }

        .cell {
            width: 80px;
            height: 80px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2.5rem;
            font-weight: bold;
            color: #ffffff;
            cursor: pointer;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.3);
            position: relative;
            transition: background 0.3s, transform 0.4s;
        }

        .cell span {
            display: inline-block;
            transform: scale(0);
            opacity: 0;
            transition: transform 0.4s ease-in-out, opacity 0.4s ease-in-out;
            filter: drop-shadow(0 0 5px rgba(255, 255, 255, 0.6));
        }

        .cell.active span {
            transform: scale(1);
            opacity: 1;
            animation: glow 1s ease-in-out infinite alternate;
        }

        .cell:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05) rotate(3deg);
        }

        @keyframes glow {
            from { text-shadow: 0 0 10px rgba(255, 255, 255, 0.8), 0 0 20px rgba(255, 255, 255, 0.5); }
            to { text-shadow: 0 0 15px rgba(255, 255, 255, 1), 0 0 25px rgba(255, 255, 255, 0.7); }
        }

        .winner-panel {
            display: none;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            padding: 20px;
            background: rgba(0, 0, 0, 0.8);
            border-radius: 15px;
            text-align: center;
            color: #ffffff;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.7), 0 0 35px rgba(255, 255, 255, 0.5);
            animation: fadeIn 0.5s ease-in-out;
        }

        .winner-panel.active {
            display: flex;
        }

        .winner-panel h2 {
            margin: 0 0 10px;
            font-size: 2rem;
            animation: glow 1s ease-in-out infinite alternate;
        }

        .winner-panel button {
            margin-top: 20px;
            padding: 10px 20px;
            font-size: 1rem;
            color: #ff416c;
            background: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s, transform 0.3s, box-shadow 0.3s;
        }

        .winner-panel button:hover {
            background: #ff416c;
            color: #ffffff;
            transform: translateY(-3px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
    <h1 class="title">Tic Tac Toe</h1>

    <div class="container" id="board">
        <div class="cell" data-index="0"><span></span></div>
        <div class="cell" data-index="1"><span></span></div>
        <div class="cell" data-index="2"><span></span></div>
        <div class="cell" data-index="3"><span></span></div>
        <div class="cell" data-index="4"><span></span></div>
        <div class="cell" data-index="5"><span></span></div>
        <div class="cell" data-index="6"><span></span></div>
        <div class="cell" data-index="7"><span></span></div>
        <div class="cell" data-index="8"><span></span></div>
    </div>

    <div class="winner-panel" id="winnerPanel">
        <h2 id="winnerMessage">Winner: X</h2>
        <button onclick="restartGame()">Restart Game</button>
    </div>

    <script>
        const cells = document.querySelectorAll('.cell');
        const winnerPanel = document.getElementById('winnerPanel');
        const winnerMessage = document.getElementById('winnerMessage');
        let currentPlayer = 'X';
        let board = ['', '', '', '', '', '', '', '', ''];
        let isGameOver = false;

        cells.forEach(cell => {
            cell.addEventListener('click', handleCellClick);
        });

        function handleCellClick(event) {
            const index = event.currentTarget.getAttribute('data-index');

            if (board[index] !== '' || isGameOver || currentPlayer === 'O') {
                return;
            }

            board[index] = currentPlayer;
            event.currentTarget.querySelector('span').textContent = currentPlayer;
            event.currentTarget.classList.add('active');

            if (checkWinner()) {
                winnerMessage.textContent = `Winner: ${currentPlayer}`;
                winnerPanel.classList.add('active');
                isGameOver = true;
            } else if (board.every(cell => cell !== '')) {
                winnerMessage.textContent = `It's a draw!`;
                winnerPanel.classList.add('active');
                isGameOver = true;
            } else {
                currentPlayer = 'O';
                setTimeout(botMove, 500); // Simulate bot thinking time
            }
        }

        function botMove() {
            if (isGameOver) return;

            let bestMove = getBestMove();
            board[bestMove] = 'O';
            cells[bestMove].querySelector('span').textContent = 'O';
            cells[bestMove].classList.add('active');

            if (checkWinner()) {
                winnerMessage.textContent = `Winner: O`;
                winnerPanel.classList.add('active');
                isGameOver = true;
            } else if (board.every(cell => cell !== '')) {
                winnerMessage.textContent = `It's a draw!`;
                winnerPanel.classList.add('active');
                isGameOver = true;
            } else {
                currentPlayer = 'X';
            }
        }

        function getBestMove() {
            // Check for afunction getBestMove() {
            const winningCombinations = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8],
                [0, 3, 6], [1, 4, 7], [2, 5, 8],
                [0, 4, 8], [2, 4, 6]
            ];

            // Check for a winning move
            for (const combination of winningCombinations) {
                const [a, b, c] = combination;
                if (board[a] === board[b] && board[a] === 'O' && board[c] === '') return c;
                if (board[a] === board[c] && board[a] === 'O' && board[b] === '') return b;
                if (board[b] === board[c] && board[b] === 'O' && board[a] === '') return a;
            }

            // Block the opponent's winning move
            for (const combination of winningCombinations) {
                const [a, b, c] = combination;
                if (board[a] === board[b] && board[a] === 'X' && board[c] === '') return c;
                if (board[a] === board[c] && board[a] === 'X' && board[b] === '') return b;
                if (board[b] === board[c] && board[b] === 'X' && board[a] === '') return a;
            }

            // Take the center if available
            if (board[4] === '') return 4;

            // Choose a corner if available
            const corners = [0, 2, 6, 8];
            const availableCorners = corners.filter(index => board[index] === '');
            if (availableCorners.length > 0) return availableCorners[Math.floor(Math.random() * availableCorners.length)];

            // Choose a random available spot
            const availableSpots = board.map((value, index) => value === '' ? index : null).filter(value => value !== null);
            return availableSpots[Math.floor(Math.random() * availableSpots.length)];
        }

        function checkWinner() {
            const winningCombinations = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8],
                [0, 3, 6], [1, 4, 7], [2, 5, 8],
                [0, 4, 8], [2, 4, 6]
            ];

            return winningCombinations.some(combination => {
                return combination.every(index => {
                    return board[index] === currentPlayer;
                });
            });
        }

        function restartGame() {
            board = ['', '', '', '', '', '', '', '', ''];
            currentPlayer = 'X';
            isGameOver = false;
            winnerPanel.classList.remove('active');
            cells.forEach(cell => {
                cell.querySelector('span').textContent = '';
                cell.classList.remove('active');
            });
        }
    </script>
</body>
</html>
