---
layout: post
title: "What I Learned in CSP"
author: Debuggers Team
categories: [CSP, Teamwork, Learning]
---

# Blog that documents my issues and experiences in CSP

## Setting Up Tools and Equipment 🛠️
### Operating System Setup 💻
Install essential tools:  
- Homebrew (macOS package manager)  
- VSCode (code editor)  

Run setup scripts:  
```bash
./scripts/activate_macos.sh
./scripts/activate.sh   # prompts for Git UID and Personal Email
./scripts/venv.sh
Verify installation ✅:

bash
python --version      # Shows Python version
pip --version         # Shows pip version
ruby -v               # Shows Ruby version
bundle -v             # Shows Bundler version
gem --version         # Shows RubyGems version
git config --global --list  # Lists Git configuration
VSCode Configuration 🔧
Configure extensions, themes, and settings for development.

Project Creation and Access 🚀
Find Your Repository: Navigate to your repo on GitHub (e.g., https://github.com/YOUR-ORG/pages) and copy the URL.

Clone Repository:

bash
cd                    # Move to home directory
mkdir -p YOUR-ORG     # Create organization directory
cd YOUR-ORG           # Enter directory
git clone https://github.com/YOUR-ORG/student.git  # Clone repo
Set Up Environment:

bash
cd student
./scripts/venv.sh
source venv/bin/activate
bundle install
code .
Start Local Server 🌐:

bash
make  # Builds and serves Jekyll site locally
Version Control and Management 🔄
GitHub Features 🐙
Repositories 📁

Branches 🌿

Forks 🍴

Issues 🐛

Kanban Boards 📋

GitHub Pages 🌍

Actions ⚡

VSCode Version Control 🎛️
bash
git add .                     # Stage all changes
git commit -m "Your message"  # Commit changes
git push origin main           # Upload changes
git pull origin main           # Download remote changes
git checkout -b feature-name   # Create & switch to new branch
git checkout main              # Switch back to main branch
Merge Conflicts ⚔️: Resolve when multiple edits happen.

Staging 📝: Select changes to include.

History 📜: View commit history.

Essential Commands 💻
Basic Shell Commands 🐚
bash
ls        # List files and directories
pwd       # Print current directory
mkdir     # Make new directories
cd        # Change directory
git       # Version control commands
cat       # Display file contents
Helpful Tips & Tricks 💡
Multi-Repository Workspace 🗂️:

bash
code YOUR-ORG/student YOUR-ORG/pages
Check Git Configuration ⚙️:

bash
cat .git/config
Progress & Reflections 📈
Prior experience from CSSE and CSA helped initial setup.

Some outdated software required updates.

Key Learning Experiences 🎓
New Command-Line Skills:

code ~ → Open VSCode in home directory

code YOUR-ORG/student YOUR-ORG/pages → Open multiple repos in one workspace

cat .git/config → Display Git configuration

Virtual environment setup using ./scripts/venv.sh

Peer Teaching Benefits: Helping classmates troubleshoot strengthened my understanding.

Technical Challenges & Solutions ⚙️
Jupyter Notebook Integration 📝
Execute code cells via Help → Toggle Developer Tools → Console

Create DOM elements using %%html magic commands

Implement JavaScript for interactive content:

html
%%html
<div id="joke-out" style="font-family: Arial, sans-serif; margin: 8px 0;"></div>
<script>
  // JavaScript code for displaying random programming jokes
</script>
Repository Management Insights 🗂️
GitHub Organizations vs Public Repos: Public repos don’t automatically enable collaboration features.

Solution: Create a GitHub organization for full team access.

Theme Customization 🎨
Use make help to explore options

Update files:

Gemfile (Ruby dependencies)

_config.yml (Jekyll configuration)

post.html (template files)