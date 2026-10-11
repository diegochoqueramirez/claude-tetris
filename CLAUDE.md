# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Vanilla JS Tetris on HTML5 Canvas. No dependencies, no `package.json`, no build, no tests, no linter. Three files: `index.html` (DOM + canvases), `style.css` (dark theme), `game.js` (all logic). README and in-game UI text are in Spanish — keep new user-facing strings in Spanish.

## Running

Open `index.html` directly, or serve statically (preferred):

```bash
python -m http.server 8000   # then http://localhost:8000
```

## Architecture (`game.js`)

- **Global mutable state**: `board, current, next, score, lines, level, paused, gameOver, lastTime, dropAccum, dropInterval, animId` are module-level `let`s, (re)initialized in `init()`. `init()` is also the restart handler.
- **Cell encoding**: the piece type index (1–8; 8 = "tuerca", a 3×3 ring challenge piece) doubles as the color index. `PIECES[type]` matrices store `type` in filled cells, `merge()` copies those values into `board`, and `drawBlock` looks up `COLORS[value]`. Adding/reordering pieces requires keeping `PIECES` and `COLORS` aligned; `randomPiece()` picks uniformly over `PIECES.length - 1`.
- **Pieces** are `{ type, shape, x, y }`; `shape` is a square matrix copied from `PIECES`. Rotation = `rotateCW` (transpose + reverse); `tryRotate` applies simple horizontal kicks `[0,-1,1,-2,2]` (not SRS).
- **Collision** (`collide(shape, ox, oy)`) is the single source of truth for movement, rotation, ghost (`ghostY`), and game-over detection in `spawn()`. Cells with `ny < 0` are allowed (above board).
- **Loop**: `requestAnimationFrame` accumulates `dt` into `dropAccum`; gravity step when it exceeds `dropInterval`. Pause/game over cancel the RAF via `animId`; resume restarts `loop` with a fresh `lastTime`.
- **Lock flow**: `lockPiece()` → `merge()` → `clearLines()` (updates score/lines/level/`dropInterval`) → `spawn()` (promotes `next`, may call `endGame()`, redraws next preview).
- **Scoring/speed**: `LINE_SCORES[cleared] * level`; soft drop +1/row, hard drop +2/row; level = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)`.

## Coupling to watch

- Board canvas size in `index.html` (300×600) must equal `COLS*BLOCK × ROWS*BLOCK`.
- `drawNext()` assumes a 4×4 grid at 30px (`NB`) matching the 120×120 `#next-canvas`.
- DOM element IDs (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`) are looked up at load in `game.js`.
