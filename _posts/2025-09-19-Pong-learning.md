---
layout: post
title: "Mastering Pong Development — Features, OOP, and Git Commits"
permalink: /pong-learning/
---

## 🚀 Overview
We started with a **basic Pong game**: the ball moved, paddles responded, and points could be scored. While functional, it was simple. The goal of this lesson was to **add new features, integrate localStorage, and refactor into Object-Oriented Programming (OOP)** — while practicing disciplined Git commits.

---

## 🔧 Adding a New Feature
Our task was to make Pong **more exciting and professional**.  
Some of the options included:

- **Speed Boost Mode** – the ball speeds up after every few points  
- **Power-Ups** – items appear and temporarily affect gameplay  
- **Score Unlocks** – paddle colors or backgrounds that unlock after milestones  
- **Custom Difficulty** – adjustable paddle/ball speed

We planned our feature first (Commit #2), built the HTML (Commit #3), wrote the JavaScript with conditionals (Commit #4), and added **localStorage persistence** (Commit #5). Finally, we tested it and marked it complete (Commit #6).

---

## 🏗️ Moving to OOP
We then reorganized the game into **classes**:

- `Paddle` – properties for position, size, speed; methods like `moveUp()` and `draw()`
- `Ball` – properties for position and velocity; methods like `move()` and `checkCollision()`
- `Game` – owned the loop, score, and instances of paddles and ball

This made the code easier to expand, debug, and maintain. We committed planning (Commit #7), the `Paddle` class (Commit #8), the `Ball` class (Commit #9), and the updated game loop (Commit #10). Finally, we added persistence to the OOP version with localStorage (Commit #11).

---

## 💾 Git Commit Discipline
One of the most valuable habits was committing **after every milestone**:

- Commit #1: Initial Pong setup  
- Commits #2–#6: Planning → HTML → JS → Persistence → Feature Complete  
- Commits #7–#11: OOP planning and refactor  
- Commit #12: Reflection  
- Commit #13: Final Submission  

This helped us **track progress, revert mistakes, and clearly show our development journey**.

---

## 📝 Reflection
The most challenging part was making sure the new feature worked smoothly without breaking existing logic. Using commits gave us confidence to experiment, because we could always roll back. OOP made the game code feel much cleaner — especially separating ball physics from paddle logic. Debugging with DevTools and console logs also became much easier with this structure.

---

🎉 Final Result:  
A more **fun, feature-rich Pong game**, saved progress with localStorage, and a professional Git commit history showing each step of growth.

