tic tae toe

html----

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Tic Tac Toe</title>
    <link rel="stylesheet" href="Tstyles.css" />
  </head>
  <body>
    <div class="container">
      <h1>Tic Tac Toe</h1>
      <div class="scoreboard">
        <strong>Score</strong> – X: <span id="scoreX">0</span> | O:
        <span id="scoreO">0</span>
      </div>
      <div id="game-board" class="board"></div>
      <div class="info">
        <p id="status">Player X's turn</p>
        <button id="resetBtn">Reset Game</button>
        <button id="toggleTheme">Toggle Dark Mode</button>
        <button id="toggleMode">Switch to 2 Player Mode</button>
      </div>
    </div>

    <!-- JS Script -->
    <script src="Tscript.js"></script>
  </body>
</html>


css-----

body {
  font-family: "Segoe UI", sans-serif;
  background-color: #e6f0ff;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  margin: 0;
  transition: background-color 0.3s, color 0.3s;
}

body.dark {
  background-color: #121212;
  color: white;
}

.container {
  text-align: center;
  background: white;
  padding: 20px 30px;
  border-radius: 12px;
  box-shadow: 0 10px 20px rgba(0, 0, 50, 0.2);
  transition: background 0.3s;
}

body.dark .container {
  background: #1f1f1f;
  color: white;
}

h1 {
  color: #0066cc;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-gap: 10px;
  justify-content: center;
  margin: 20px auto;
}

.cell {
  width: 100px;
  height: 100px;
  background-color: #ffffff;
  border: 2px solid #0066cc;
  font-size: 2.5rem;
  color: #0066cc;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background-color 0.3s, transform 0.2s ease;
}

.cell:hover {
  background-color: #cce6ff;
  transform: scale(1.05);
}

.cell.win {
  background-color: #99ccff;
}

.info,
.scoreboard {
  margin-top: 10px;
}

button {
  margin: 5px;
  padding: 10px 20px;
  background-color: #0066cc;
  border: none;
  color: white;
  font-size: 1rem;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background-color: #0055aa;
}

body.dark .cell {
  background-color: #333;
  color: #99ccff;
  border-color: #99ccff;
}

body.dark .cell:hover {
  background-color: #444;
}

body.dark .cell.win {
  background-color: #0066cc;
}


js----

const board = document.getElementById('game-board');
const statusText = document.getElementById('status');
const resetBtn = document.getElementById('resetBtn');
const toggleThemeBtn = document.getElementById('toggleTheme');
const toggleModeBtn = document.getElementById('toggleMode');
const scoreXText = document.getElementById('scoreX');
const scoreOText = document.getElementById('scoreO');

let cells = [];
let currentPlayer = 'X';
let isGameActive = true;
let scoreX = 0;
let scoreO = 0;
let isVsAI = true;

const winningCombinations = [
  [0,1,2],[3,4,5],[6,7,8],
  [0,3,6],[1,4,7],[2,5,8],
  [0,4,8],[2,4,6]
];

function createBoard() {
  board.innerHTML = '';
  cells = [];

  for (let i = 0; i < 9; i++) {
    const cell = document.createElement('div');
    cell.classList.add('cell');
    cell.dataset.index = i;
    cell.addEventListener('click', handleCellClick);
    board.appendChild(cell);
    cells.push(cell);
  }
}

function handleCellClick(e) {
  const index = e.target.dataset.index;
  if (!isGameActive || cells[index].textContent !== '') return;

  makeMove(index, currentPlayer);

  if (checkWin(currentPlayer)) {
    endGame(currentPlayer);
    return;
  }

  if (checkTie()) {
    endGame(null);
    return;
  }

  if (isVsAI && currentPlayer === 'X') {
    currentPlayer = 'O';
    setTimeout(() => {
      aiMove();
    }, 400);
  } else {
    currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
    updateStatus();
  }
}

function makeMove(index, player) {
  cells[index].textContent = player;
}

function aiMove() {
  if (!isGameActive) return;

  const emptyCells = cells
    .map((cell, i) => cell.textContent === '' ? i : null)
    .filter(i => i !== null);

  const move = emptyCells[Math.floor(Math.random() * emptyCells.length)];
  makeMove(move, 'O');

  if (checkWin('O')) {
    endGame('O');
  } else if (checkTie()) {
    endGame(null);
  } else {
    currentPlayer = 'X';
    updateStatus();
  }
}

function checkWin(player) {
  return winningCombinations.some(([a, b, c]) => {
    if (
      cells[a].textContent === player &&
      cells[b].textContent === player &&
      cells[c].textContent === player
    ) {
      cells[a].classList.add('win');
      cells[b].classList.add('win');
      cells[c].classList.add('win');
      return true;
    }
    return false;
  });
}

function checkTie() {
  return cells.every(cell => cell.textContent !== '');
}

function endGame(winner) {
  isGameActive = false;
  if (winner) {
    statusText.textContent = Player ${winner} wins!;
    winner === 'X' ? scoreX++ : scoreO++;
  } else {
    statusText.textContent = "It's a draw!";
  }
  updateScores();
}

function updateScores() {
  scoreXText.textContent = scoreX;
  scoreOText.textContent = scoreO;
}

function updateStatus() {
  statusText.textContent = Player ${currentPlayer}'s turn;
}

function resetGame() {
  isGameActive = true;
  currentPlayer = 'X';
  updateStatus();
  createBoard();
}

function toggleTheme() {
  document.body.classList.toggle('dark');
}

function toggleGameMode() {
  isVsAI = !isVsAI;
  toggleModeBtn.textContent = isVsAI ? 'Switch to 2 Player Mode' : 'Switch to Vs AI Mode';
  resetGame();
}

resetBtn.addEventListener('click', resetGame);
toggleThemeBtn.addEventListener('click', toggleTheme);
toggleModeBtn.addEventListener('click', toggleGameMode);

createBoard();
