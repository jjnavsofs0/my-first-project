# Day 3: Remote Repositories & GitHub/GitLab — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**  
Module 3 of 8 · Remote Repositories & GitHub/GitLab Basics

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Remote Management](#1-remote-management)
  - [2. Push & Pull](#2-push--pull)
  - [3. Clone & Fetch](#3-clone--fetch)
  - [4. SSH Authentication](#4-ssh-authentication)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Set Up SSH Authentication](#task-1-set-up-ssh-authentication)
  - [Task 2: Create Remote Repository](#task-2-create-remote-repository)
  - [Task 3: Push Local Repo to Remote](#task-3-push-local-repo-to-remote)
  - [Task 4: Clone an Existing Repo](#task-4-clone-an-existing-repo)
  - [Task 5: Write README & Push Documentation](#task-5-write-readme--push-documentation)
- [Hands-On Lab Setup](#hands-on-lab-setup)
- [Lab Task Checklist](#lab-task-checklist)
- [Quick Reference](#quick-reference)
- [Bonus Commands](#bonus-commands)

---

## Current Setup

| Setting | Value |
|---------|-------|
| `user.name` | jjnavsofs0 |
| `user.email` | jaisingh1006@gmail.com |
| Day 1 repo (macOS/Linux) | `~/Projects/trainings/git-18094/day1-lab` |
| Day 1 repo (Windows) | `%USERPROFILE%\Projects\trainings\git-18094\day1-lab` |

> **Prerequisite:** Days 1 & 2 completed — local repos and branching mastered.
>
> **Demo repo:** [github.com/jjnavsofs0/git-training-demo](https://github.com/jjnavsofs0/git-training-demo) — use for Task 4 (clone) if you don't have your own repo yet.
>
> **Path note:** Presentation uses `~/training/day1-lab`; this guide uses `~/Projects/trainings/git-18094/day1-lab`.

---

## Command Reference

### 1. Remote Management

```bash
# Add a remote named origin
git remote add origin git@github.com:jjnavsofs0/my-first-project.git

# List all remotes
git remote -v

# Remove a remote
git remote remove origin

# Rename remote
git remote rename origin upstream

# Add upstream (for forks)
git remote add upstream git@github.com:ORG/repo.git
```

### 2. Push & Pull

```bash
# Push and set tracking branch (first time)
git push -u origin main

# Push to tracked remote (after -u set)
git push

# Push a specific branch
git push origin feature/login

# Pull (fetch + merge)
git pull origin main

# Pull specific branch
git pull origin feature/login
```

### 3. Clone & Fetch

```bash
# Clone via SSH
git clone git@github.com:jjnavsofs0/repo.git

# Clone into a specific folder
git clone git@github.com:jjnavsofs0/repo.git my-folder

# Fetch only (download, no merge)
git fetch origin

# View remote branch history
git log origin/main
```

### 4. SSH Authentication

```bash
# Generate Ed25519 key
ssh-keygen -t ed25519 -C "jaisingh1006@gmail.com"

# Start SSH agent
eval "$(ssh-agent -s)"

# Add key to agent
ssh-add ~/.ssh/id_ed25519

# Show public key (copy to GitHub/GitLab)
cat ~/.ssh/id_ed25519.pub

# macOS: copy to clipboard
pbcopy < ~/.ssh/id_ed25519.pub

# Test connection
ssh -T git@github.com
ssh -T git@gitlab.com
```

---

## Lab Guide

### Prerequisites

Navigate to your Day 1 repository:

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094/day1-lab
```

**Windows (PowerShell):**
```powershell
cd $env:USERPROFILE\Projects\trainings\git-18094\day1-lab
```

**Windows (CMD):**
```cmd
cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab
```

---

### Task 1: Set Up SSH Authentication

```bash
# 1. Generate key (press Enter for defaults)
ssh-keygen -t ed25519 -C "jaisingh1006@gmail.com"

# 2. Start agent and add key
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# 3. Copy public key
cat ~/.ssh/id_ed25519.pub

# 4. Add to GitHub: Settings → SSH keys → New SSH key → paste → Save
#    Or GitLab: Preferences → SSH Keys → Add key

# 5. Test connection
ssh -T git@github.com
# Expect: "Hi jjnavsofs0! You've successfully authenticated..."
```

---

### Task 2: Create Remote Repository

1. Go to [github.com](https://github.com) or [gitlab.com](https://gitlab.com)
2. Click **+** → **New Repository** (GitHub) or **New Project** (GitLab)
3. Name: `my-first-project` (match your local folder)
4. Visibility: **Private**
5. **Do NOT** check "Initialize repository" (you already have one)
6. Click **Create Repository** — copy the **SSH clone URL**

---

### Task 3: Push Local Repo to Remote

```bash
cd ~/Projects/trainings/git-18094/day1-lab

# Add remote (replace with your repo URL)
git remote add origin git@github.com:jjnavsofs0/my-first-project.git

# Ensure branch is named main
git branch -M main

# Push (first time — sets tracking)
git push -u origin main

# Verify
git remote -v

# Refresh GitHub/GitLab page — your commits should appear!
```

**HTTPS alternative:** Use `https://github.com/jjnavsofs0/my-first-project.git` if SSH not configured.

**Windows:** Same commands — use Git Bash or ensure path uses correct format.

---

### Task 4: Clone an Existing Repo

```bash
# Clone your repo into a new folder
git clone git@github.com:jjnavsofs0/my-first-project.git cloned-repo

cd cloned-repo

# Verify (full history was downloaded)
git log --oneline
git remote -v
ls -la

# Make a change
echo 'Cloned edit' >> README.md  # macOS/Linux
# Windows PowerShell: Add-Content README.md "Cloned edit"
# Windows CMD: echo Cloned edit >> README.md

git add .
git commit -m "docs: update from cloned repo"
git push
```

---

### Task 5: Write README & Push Documentation

```bash
cd ~/Projects/trainings/git-18094/day1-lab

# Create/update README.md with:
# - Project name & description
# - Quick start (how to run)
# - Prerequisites
# - How to run tests

git add README.md
git commit -m "docs: add comprehensive README"

# If .gitignore not yet committed: git add .gitignore && git commit -m "chore: add .gitignore"

git push

# Open GitHub/GitLab in browser — README renders on repo homepage
```

**Deliverables:** SSH configured · Remote repo with pushed commits · README rendered on web

---

## Hands-On Lab Setup

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094/day1-lab
```

**Windows (PowerShell):**
```powershell
cd $env:USERPROFILE\Projects\trainings\git-18094\day1-lab
```

**Windows (CMD):**
```cmd
cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab
```

---

## Lab Task Checklist

Condensed commands for the full lab. For UI steps and README template, see the [Lab Guide](#lab-guide).

### Task 1: Set Up SSH Authentication
```bash
ssh-keygen -t ed25519 -C "jaisingh1006@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
# Add key in GitHub/GitLab UI, then:
ssh -T git@github.com
```

### Task 2: Create Remote Repository
Create an empty repo on GitHub/GitLab (no README init) — copy the **SSH clone URL**.

### Task 3: Push Local Repo to Remote
Replace the remote URL with yours:
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git remote add origin git@github.com:jjnavsofs0/my-first-project.git
git branch -M main
git push -u origin main
git remote -v
```

### Task 4: Clone an Existing Repo
```bash
git clone git@github.com:jjnavsofs0/my-first-project.git cloned-repo
cd cloned-repo
git log --oneline
git remote -v
echo 'Cloned edit' >> README.md
git add .
git commit -m "docs: update from cloned repo"
git push
```

### Task 5: Write README & Push Documentation
```bash
cd ~/Projects/trainings/git-18094/day1-lab
# Edit README.md — see Lab Guide for structure
git add README.md
git commit -m "docs: add comprehensive README"
git push
```

**Deliverables:** SSH configured · Remote repo with pushed commits · README rendered on web

**Example README structure:**
```markdown
# Calculator Project

A simple Python calculator with add, subtract, multiply, divide.

## Quick Start

git clone git@github.com:jjnavsofs0/my-first-project.git
cd my-first-project
python calculator.py

## Prerequisites

- Python 3.8+

## Running Tests

python -m pytest tests/
```

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `git remote add origin <url>` | Add remote |
| `git remote -v` | List remotes |
| `git push -u origin main` | Push & set tracking |
| `git push` | Push to tracked remote |
| `git pull origin main` | Fetch + merge |
| `git fetch origin` | Download only |
| `git clone <url>` | Clone repo |
| `ssh-keygen -t ed25519` | Generate SSH key |
| `ssh -T git@github.com` | Test SSH |

---

## Bonus Commands

```bash
# Rename origin to upstream
git remote rename origin upstream

# Fetch without merging (download only, inspect before merge)
git fetch origin
git log origin/main

# View remote branch history
git log origin/main
```

## fetch vs pull

| Command | Action |
|---------|--------|
| `git fetch` | Downloads changes to `origin/main` — working files untouched |
| `git pull` | Fetch + merge — updates your branch immediately |

**Best practice:** `git fetch` first, inspect with `git log origin/main`, then merge.

---

## Clone vs Fork

| Operation | When | Result |
|-----------|------|--------|
| **Clone** | You have write access | Local copy + origin remote |
| **Fork** | Contributing to others' repo | Your copy on platform |

---

*Day 4 Preview → Collaboration Workflows & Pull Requests: PR/MR process, code review, Fork vs Branch*
