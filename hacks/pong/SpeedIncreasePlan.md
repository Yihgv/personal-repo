# SpeedBoostMode – Plan

## Goal
Make Pong more exciting by increasing the ball speed every few successful paddle hits.

## UX
- Add a checkbox under the canvas: **Speed Boost Mode**.
- Show a small multiplier label like `x1.00`.
- Optional feedback: brief “+10%” popup near the ball when a boost happens.

## Rules
- Every **3 paddle hits** (`HITS_PER_BOOST = 3`), multiply ball velocity by **1.10** (`BOOST_FACTOR`).
- Cap total ball speed at **MAX_SPEED = 18** to keep gameplay controllable.
- Reset the hit counter on serve so each rally starts fresh.

## Data / State
- `hitCount` (int): counts paddle contacts since last serve.
- `speedMultiplier` (float): UI-only multiplier display.
- `boostEnabled` (bool): checkbox value, persisted in localStorage.

## Persistence
- Save `{ boostEnabled: true|false }` in `localStorage` under key `pong.settings`.
- Load on page start to restore the toggle.

## Implementation Steps
1. (Commit #3) Add HTML controls (checkbox + label).
2. (Commit #4) Add JS: counters, `applySpeedBoostIfNeeded()` with `if` conditions.
3. (Commit #5) Wire localStorage load/save around the toggle.
4. (Commit #6) Polish: anti-yo-yo (min X speed), tiny nudge, optional “+10%” flash.
