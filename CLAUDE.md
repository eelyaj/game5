# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the Game

Open `index.html` directly in a browser — no build step, server, or dependencies required.

## Architecture

The entire application is a single self-contained file: `index.html`. It contains CSS, HTML, and JavaScript with no external dependencies.

### Game State

All game state lives in a single object `G`:
- `board`: 15×15 array (`0`=empty, `1`=black, `2`=white)
- `turn`: current player (`1` or `2`)
- `sp`: skill points array `[sp1, sp2]`, incremented by 1 for every stone placed by either player
- `phase`: `'place'` or `'skill'` — each turn has two phases
- `skip`: per-player flags for the "静如止水" skip-turn skill
- `usedSkill`: prevents using more than one skill per turn

### Turn Flow

1. **Place phase**: current player clicks the board to place a stone
2. **Skill phase**: player may activate one skill, then turn passes to opponent
3. Skip phase advances automatically if the player's skip flag is set

### Skills

Each player has 4 skills with skill-point costs:

| Name | Cost | Effect |
|------|------|--------|
| 飞沙走石 | 3 | Randomly removes one opponent stone |
| 力拔山兮 | 5 | Clears the entire board |
| 静如止水 | 4 | Forces opponent to skip their next turn |
| 时光倒流 | 4 | Reverts own last stone, returns to place phase |

### Rendering

The board is drawn on an HTML5 Canvas element. `draw()` redraws the full canvas on every state change. Ghost-stone preview tracks mouse position via `mousemove`.

### Key Functions

- `startGame()` — initializes/resets `G` and redraws
- `draw()` — renders board, stones, last-move indicator, and ghost stone
- `updateUI()` — syncs DOM (skill buttons, SP display, player panels, phase tag)
- `checkWin(r, c)` — checks four directions from the last placed stone
- Skill handlers: `useSkill1/2/3/4(player)` — each mutates `G` and calls `updateUI()`
