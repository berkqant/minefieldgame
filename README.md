# minefieldgame
A simple browser-based logic game inspired by Minesweeper, built with HTML and JavaScript during my internship
let timer;
let elapsedTime = 0;
let totalCells;
let revealedCells;
let gameEnded = false;
let cells;

function startGame() {
    createGrid();
    startTimer();
    revealedCells = 0;
    gameEnded = false;
    document.getElementById('game-over').style.display = 'none';
    document.getElementById('congratulations').style.display = 'none';
}

function createGrid() {
    const xAxis = parseInt(document.getElementById('x_axis').value);
    const yAxis = parseInt(document.getElementById('y_axis').value);
    const numberOfMines = parseInt(document.getElementById('mine_field').value);
    const grid = document.getElementById('grid');

    if (isNaN(xAxis) || isNaN(yAxis) || isNaN(numberOfMines) || xAxis <= 0 || yAxis <= 0 || numberOfMines <= 0) {
        alert("Enter a valid number.");
        return;
    }

    grid.innerHTML = '';
    grid.style.gridTemplateColumns = ⁠ repeat(${xAxis}, 20px) ⁠;
    grid.style.gridTemplateRows = ⁠ repeat(${yAxis}, 20px) ⁠;

    cells = [];
    totalCells = xAxis * yAxis;

    for (let i = 0; i < yAxis; i++) {
        cells[i] = [];
        for (let j = 0; j < xAxis; j++) {
            const cell = document.createElement('div');
            cell.className = 'cell';
            const button = document.createElement('button');
            cell.appendChild(button);
            grid.appendChild(cell);
            cells[i][j] = { element: cell, hasMine: false, neighborMineCount: 0 };
        }
    }

    let placedMines = 0;
    while (placedMines < numberOfMines) {
        const randomRow = Math.floor(Math.random() * yAxis);
        const randomCol = Math.floor(Math.random() * xAxis);
        if (!cells[randomRow][randomCol].hasMine) {
            cells[randomRow][randomCol].hasMine = true;
            placedMines++;
        }
    }

    for (let row = 0; row < yAxis; row++) {
        for (let col = 0; col < xAxis; col++) {
            if (cells[row][col].hasMine) continue;
            let mineCount = 0;
            for (let i = -1; i <= 1; i++) {
                for (let j = -1; j <= 1; j++) {
                    const newRow = row + i;
                    const newCol = col + j;
                    if (newRow >= 0 && newRow < yAxis && newCol >= 0 && newCol < xAxis && cells[newRow][newCol].hasMine) {
                        mineCount++;
                    }
                }
            }
            cells[row][col].neighborMineCount = mineCount;
        }
    }

    cells.flat().forEach(cell => {
        const button = cell.element.querySelector('button');
        button.addEventListener('click', () => {
            if (gameEnded) return;
            if (cell.hasMine) {
                revealAllMines();
                gameOver();
            } else {
                cell.element.innerHTML = ⁠ <span class="number-${cell.neighborMineCount}">${cell.neighborMineCount}</span> ⁠;
                button.remove();
                revealedCells++;
                if (revealedCells === totalCells - numberOfMines) {
                    winGame();
                }
            }
        });
    });
}

function triggerFireworks() {
    const fireworksContainer = document.getElementById('fireworks-container');
    fireworksContainer.innerHTML = '';

    for (let i = 0; i < 20; i++) {
        const firework = document.createElement('div');
        firework.className = 'firework';
        firework.style.left = ⁠ ${Math.random() * 100}% ⁠;
        firework.style.top = ⁠ ${Math.random() * 100}% ⁠;
        fireworksContainer.appendChild(firework);

        setTimeout(() => {
            firework.remove();
        }, 1000);
    }
}

function startTimer() {
    elapsedTime = 0;
    document.getElementById('elapsed-time').textContent = elapsedTime;
    timer = setInterval(() => {
        elapsedTime++;
        document.getElementById('elapsed-time').textContent = elapsedTime;
    }, 1000);
}




function winGame() {
    gameEnded = true;
    clearInterval(timer);
    document.getElementById('congratulations').style.display = 'block';
    triggerFireworks();
}

function revealAllCells() {
    const cells = document.querySelectorAll('.cell');
    cells.forEach(cell => {
        const button = cell.querySelector('button');
        if (button) {
            button.click();
        }
    });
}

function revealAllMines() {
    cells.flat().forEach(cell => {
        if (cell.hasMine) {
            const img = document.createElement('img');
            img.src = 'war.png';
            cell.element.innerHTML = '';
            cell.element.appendChild(img);
        }
    });
}
