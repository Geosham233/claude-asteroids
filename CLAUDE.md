# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a clone of the classic **Asteroids** arcade game, implemented in pure HTML5 Canvas with no dependencies or bundlers. The game is a single-page application (index.html) with all game logic in a single JavaScript file (game.js).

## Running the Game

- **Direct**: Double-click `index.html` to open in your browser
- **Local server**: `npx serve .`, then visit `http://localhost:3000`

The game uses `requestAnimationFrame` for the main loop and renders to a 800×600 canvas element.

## Architecture

### Code Organization

All game code is in `game.js` (~424 lines), organized into logical sections with comment headers:

1. **Input System** — Keyboard tracking (continuous vs. just-pressed keys)
2. **Utilities** — Helper functions: `wrap()` (toroidal wrapping), `dist()` (distance), `rand()`, `randInt()`
3. **Game Objects** — Classes for Bullet, Asteroid, Ship, and Particle
4. **Game State** — Global state variables and initialization
5. **Update** — Delta-time based physics and collision detection
6. **Rendering** — Canvas drawing and HUD
7. **Main Loop** — `requestAnimationFrame` with delta-time management

### Game Object Classes

- **Bullet** — Fired by the ship. Travels in a straight line, despawns after 1.1 seconds, 2px radius.
- **Asteroid** — Three sizes (1=small, 2=medium, 3=large). Each has irregular polygon geometry (8–13 verts). Splits into two smaller asteroids when destroyed. Movement is toroidal-wrapped.
- **Ship** — Player-controlled. Triangle silhouette with engine flame. Invincible for 3 seconds after respawning (visual blinking). Shoot cooldown of 0.2 seconds.
- **Particle** — Explosion debris. 8–14 spawned per asteroid destruction based on size.

### Game Flow

1. **initGame()** — Resets state, spawns 4 large asteroids
2. **update(dt)** — Handles input, physics, and collision detection
3. **nextLevel()** — Clears bullets/particles, respawns ship, spawns 3 + level asteroids
4. **draw()** — Renders everything and the HUD

### Game States

- **'playing'** — Normal gameplay
- **'dead'** — Ship destroyed, 2-second timer before respawn
- **'gameover'** — All lives lost, waiting for Space to restart

## Key Constants

| Constant | Purpose | Location |
|----------|---------|----------|
| `RADII[size]` | Asteroid collision radius by size | line 61 |
| `SPEEDS[size]` | Asteroid base velocity by size | line 62 |
| `POINTS[size]` | Score awarded per size | line 63 |
| Ship rotation speed | 3.5 rad/s | line 143 |
| Ship thrust | 260 px/s² | line 144 |
| Bullet speed | 520 px/s | line 37 |
| Bullet lifetime | 1.1 seconds | line 40 |

## Physics & Collisions

- **Wrapping**: Toroidal space using `wrap(v, max)` — objects exiting one edge re-enter the opposite
- **Drag**: Ship applies 0.987 damping per frame for arcade feel
- **Bullet–Asteroid**: Simple radius collision `dist(bullet, asteroid) < asteroid.radius`
- **Ship–Asteroid**: Uses scaled radius: `dist(ship, asteroid) < ship.radius + asteroid.radius * 0.82`

## Common Modifications

- **Game speed**: Adjust `SPEEDS[size]` (line 62) or `THRUST` (line 144)
- **Difficulty**: Modify `SAFE_DIST` (line 245) for spawn safety, or asteroid count in `spawnAsteroids()` call (line 265)
- **Asteroid splits**: Change the size-decrement logic in `split()` (line 98) or spawn count
- **Scoring**: Modify `POINTS[size]` (line 63)
- **Visual**: Adjust Ship shape (lines 184–188), explosion colors (line 198), or particle counts (line 331)
