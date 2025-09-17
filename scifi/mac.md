---
layout: base
title: Mac 💻
permalink: /tools/mac
---

<style>
/* Change whole page background and text style */
body {
  font-family: "Helvetica Neue", Arial, sans-serif;
  color: #000000ff;
}

/* Style for the clickable list */
.toc {
  background-color: #16e8ffff;
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 30px;
}

.toc h2 {
  margin-top: 0;
  color: #1e2aceff;
}

.toc ul {
  list-style: none;
  padding: 0;
}

.toc li {
  margin: 8px 0;
}

.toc a {
  color: #0066cc;
  text-decoration: none;
  font-weight: 600;
}

.toc a:hover {
  text-decoration: underline;
}
</style>

# Mac Feature 💻✨

<div class="toc">
  <h2>Mac Feature 🚀</h2>
  <ul>
    <li><a href="#using-vs-code">Using VS Code 🖥️</a></li>
    <li><a href="#common-tools">Common Tools 🛠️</a></li>
    <li><a href="#terminal-tips">Terminal Tips 🖱️</a></li>
    <li><a href="#shortcuts">Keyboard Shortcuts ⌨️</a></li>
  </ul>
</div>

---

## Using VS Code 🖥️

### 1. Install VS Code 📥
- Download from [Visual Studio Code](https://code.visualstudio.com/).
- Move the app to your **Applications** folder 🗂️.
- Pin it to the Dock for easy access 📌.

### 2. Enable `code` Command 🛠️
Open VS Code → press `⇧⌘P` → search for **Shell Command: Install 'code' command in PATH**.  
Now you can open a project with:
```bash
code .
```

### 3. Recommended Extensions 🔌

* **Python 🐍** → run Python scripts.
* **Jupyter 📓** → support for notebooks.
* **Prettier 🎨** → auto format.
* **GitLens 🔍** → Git visualization.

---

## Common Tools 🛠️

### 1. Install Homebrew 🍺

Open Terminal and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After installation, verify:

```bash
brew --version
```

### 2. Install Git 🔧

```bash
brew install git
git --version
```

### 3. Install Python 🐍

Mac has a system Python, but for development:

```bash
brew install python
python3 --version
pip3 --version
```

### 4. Optional Tools ⚙️

* **iTerm2 🖤** → better terminal.
* **Oh My Zsh 🌟** → terminal customization.

---

## Terminal Tips 🖱️

### Basic Commands 💡

```bash
pwd     # print working directory 📂
ls      # list files 📄
cd dir  # change directory 🔀
mkdir x # create folder 📁
rm -rf  # remove file/folder ❌
```

### Git Commands 🌱

```bash
git clone <url>      # clone repository 📥
git pull             # fetch changes 🔄
git add .            # stage changes ➕
git commit -m "msg"  # commit 📝
git push             # upload changes ⬆️
```

### Brew Commands 🍺

```bash
brew list             # list installed packages 📦
brew search <pkg>     # search for package 🔍
brew update           # update brew ⬆️
brew upgrade          # upgrade all ⚡
brew uninstall <pkg>  # uninstall package ❌
```

### Setup Checks ✅

```bash
python3 --version 🐍
pip3 --version 📦
git --version 🔧
brew --version 🍺
```

```

---