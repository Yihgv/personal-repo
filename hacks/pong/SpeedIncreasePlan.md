# SpeedBoostMode — Plan

Goal: Make the ball go faster every few paddle hits for rising difficulty.

Rules:
- Every 3 paddle hits: increase ball speed by +10%, up to a safety cap.
- Option to toggle the feature on/off via checkbox in UI.
- Display current speed multiplier (x1.00, x1.10, etc.).
- Optional persistence with localStorage (on/off remembered).

Minimum requirements mapping:
- New function(s): applySpeedBoostIfNeeded(), clampSpeed(), updateBoostStatus(), resetSpeedTracking()
- If condition: apply boost only when hitCount % HITS_PER_BOOST === 0 and toggle is ON
- HTML/CSS: checkbox + small status text
- localStorage: remembers toggle state