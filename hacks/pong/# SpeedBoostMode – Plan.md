# SpeedBoostMode – Plan

## Goal
Make Pong more exciting by increasing the ball speed every few successful paddle hits.

## UX
- Add a checkbox under the canvas: **Speed Boost Mode**.
- Show a small multiplier label like `x1.00`.
- Optional feedback: brief “+10%” popup near the ball when a boost happens.

## Rules
- Every **3 paddle hits** (`HITS_PER_BOOST = 3`), multiply ball velocity by **1.10** (`BOOST_FACTOR`).
- Cap total ball sp
