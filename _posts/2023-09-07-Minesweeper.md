---
toc: true
comments: false
layout: post
title: Minesweeper
description: Will you get a mine? Hopefully not, or else goodbye!
type: Not Used
courses: { compsci: {wek: 3} }
---

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minesweeper</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        #board {
            display: grid;
            gap: 1px;
            border: 1px solid #000;
            background-color: #333;
        }
        .cell {
            width: 20px;
            height: 20px;
            background-color: #ddd;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
        }
        .cell.clicked {
            background-color: #ccc;
        }
        .cell.mine {
            background-color: #f00;
        }
    </style>
</head>
<body>
    <div id="board"></div>
    <script>
        const rows = 10;
        const cols = 10;
        const numMines = 20;

        let board = [];
        let gameOver = false;

        function initBoard() {
            for (let i = 0; i < rows; i++) {
                board[i] = [];
                for (let j = 0; j < cols; j++) {
                    board[i][j] = { isMine: false, revealed: false, count: 0 };
                }
            }

            // Place mines randomly
            for (let i = 0; i < numMines; i++) {
                let row, col;
                do {
                    row = Math.floor(Math.random() * rows);
                    col = Math.floor(Math.random() * cols);
                } while (board[row][col].isMine);
                board[row][col].isMine = true;
            }

            // Calculate mine counts for each cell
            for (let i = 0; i < rows; i++) {
                for (let j = 0; j < cols; j++) {
                    if (!board[i][j].isMine) {
                        board[i][j].count = countMines(i, j);
                    }
                }
            }
        }

        function countMines(row, col) {
            let count = 0;
            for (let i = -1; i <= 1; i++) {
                for (let j = -1; j <= 1; j++) {
                    const r = row + i;
                    const c = col + j;
                    if (r >= 0 && r < rows && c >= 0 && c < cols && board[r][c].isMine) {
                        count++;
                    }
                }
            }
            return count;
        }

        function revealCell(row, col) {
            const cell = board[row][col];
            if (cell.revealed || gameOver) return;

            cell.revealed = true;
            const cellElem = document.getElementById(`cell-${row}-${col}`);
            cellElem.classList.add('clicked');

            if (cell.isMine) {
                gameOver = true;
                cellElem.classList.add('mine');
                alert('Game over! You hit a mine.');
            } else {
                if (cell.count === 0) {
                    for (let i = -1; i <= 1; i++) {
                        for (let j = -1; j <= 1; j++) {
                            const r = row + i;
                            const c = col + j;
                            if (r >= 0 && r < rows && c >= 0 && c < cols) {
                                revealCell(r, c);
                            }
                        }
                    }
                }
            }
        }

        function createBoard() {
            const boardElem = document.getElementById('board');
            boardElem.innerHTML = '';

            for (let i = 0; i < rows; i++) {
                for (let j = 0; j < cols; j++) {
                    const cellElem = document.createElement('div');
                    cellElem.classList.add('cell');
                    cellElem.id = `cell-${i}-${j}`;
                    cellElem.addEventListener('click', () => revealCell(i, j));
                    boardElem.appendChild(cellElem);
                }
            }
        }

        initBoard();
        createBoard();
    </script>
</body>
</html>