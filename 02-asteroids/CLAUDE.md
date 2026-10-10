# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

No build, bundler, package manager, or test suite — this is plain HTML5/Canvas/JS with zero dependencies.

- Run: open `index.html` directly in a browser, or serve locally with `npx serve .` and visit `http://localhost:3000`.
- There is no lint or test command to run.

## Architecture

Entire game lives in `game.js` (single file, no modules). `index.html` just sets up an 800×600 `<canvas>` and loads it.

- **Game loop**: `requestAnimationFrame(loop)` at the bottom drives `update(dt)` then `draw()` every frame, with `dt` clamped to 0.05s.
- **Entities**: `Bullet`, `Asteroid`, `Ship`, `Particle` are independent classes, each with its own `update(dt)` and `draw()`. There is no shared base class or entity manager — the global arrays `bullets`, `asteroids`, `particles`, and the single `ship` are iterated directly in `update`/`draw`.
- **Toroidal space**: all entity positions wrap via the `wrap(v, max)` helper — the play field has no walls, entities re-enter on the opposite edge.
- **Collision**: brute-force O(n·m) distance checks (`dist`) each frame in `update` — bullets vs. asteroids, ship vs. asteroids. No spatial partitioning.
- **State machine**: global `state` is one of `'playing' | 'dead' | 'gameover'`, checked at the top of `update(dt)` to branch behavior (respawn timer, game-over restart on Space).
- **Asteroid splitting**: asteroids have `size` 3→2→1 (`RADII`/`SPEEDS`/`POINTS` arrays indexed by size); `Asteroid.split()` returns two smaller asteroids at the same position, collected into `newAsteroids` and merged into the `asteroids` array after the collision pass.
- **Input**: raw `keydown`/`keyup` listeners fill a `keys` map (held state) and a `justPressed` map (edge-triggered, consumed via `pressed(code)`) — used so shooting fires once per keypress while movement keys are checked continuously.

Note: the README describes power-ups and special asteroid types (e.g. shooting star) that are not present in the current `game.js` — treat the README's feature list as aspirational, not a source of truth for the implemented game.


## Testing:
Vitest = usar la skill y la información de: @