# Day 2: Branching & Merging — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**  
Module 2 of 8 · Branching & Merging Strategies

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Branch Commands](#1-branch-commands)
  - [2. Merge Commands](#2-merge-commands)
  - [3. Conflict Resolution](#3-conflict-resolution)
  - [4. Visualisation](#4-visualisation)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Create Feature Branches](#task-1-create-feature-branches)
  - [Task 2: Commit on Feature Branch](#task-2-commit-on-feature-branch)
  - [Task 3: Merge Back to Main](#task-3-merge-back-to-main)
  - [Task 4: Simulate & Resolve Merge Conflict](#task-4-simulate--resolve-merge-conflict)
  - [Task 5: Visualise Branch History](#task-5-visualise-branch-history)
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

> **Prerequisite:** Complete Day 1 — your calculator.py repo with 5+ commits must exist.
>
> **Path note:** Presentation uses `~/training/day1-lab`; this guide uses `~/Projects/trainings/git-18094/day1-lab`. Adjust for your setup.

---

## Command Reference

### 1. Branch Commands

```bash
# List all branches (* = current)
git branch

# Create a branch (stay on current)
git branch feature/welcome-page

# Switch to existing branch (classic)
git checkout feature/welcome-page

# Create AND switch (shorthand)
git checkout -b feature/login

# Modern syntax (Git 2.23+)
git switch -c feature/signup
git switch feature/signup          # switch only

# List branches with details
git branch --list
git branch -v                       # show last commit on each branch

# Delete merged branch (safe — warns if unmerged)
git branch -d feature/name

# Force-delete branch (no warning!)
git branch -D feature/name
```

### 2. Merge Commands

```bash
# Fast-forward merge (when no divergence on main)
git checkout main
git merge feature/welcome-page

# Force merge commit even when FF possible
git merge --no-ff feature/welcome-page

# Squash all feature commits into one
git merge --squash feature/welcome-page
git commit -m "feat: add welcome page"

# Cancel in-progress merge
git merge --abort
```

### 3. Conflict Resolution

```bash
# 1. Check conflicted files
git status

# 2. Edit file — remove markers, keep chosen code
# <<<<<<< HEAD
# your version
# =======
# their version
# >>>>>>> feature/branch

# 3. Stage resolved file
git add <file>

# 4. Complete merge (Git auto-fills message)
git commit
```

### 4. Visualisation

```bash
# Full branch history (most useful)
git log --oneline --graph --all

# With branch/tag names
git log --oneline --graph --all --decorate

# Files changed per commit
git log --stat

# Last 10 commits only
git log -n 10 --oneline

# Compare changes between branches
git diff main..feature
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

### Task 1: Create Feature Branches

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout -b feature/add-division
git branch                                    # confirm * on feature/add-division
git checkout -b feature/add-modulo
git checkout feature/add-division              # switch back
git branch                                    # should show 3 branches
```

**Windows (PowerShell/CMD):** Same commands — Git Bash or Git CMD work identically.

---

### Task 2: Commit on Feature Branch

1. **Add `divide(a, b)` to calculator.py** (on `feature/add-division`):

```python
def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

2. **Commit the changes:**
```bash
git add calculator.py
git commit -m "feat: add divide function"
```

3. **Create test file:**

**macOS / Linux / Git Bash:**
```bash
mkdir -p tests
```

**Windows (PowerShell):**
```powershell
New-Item -ItemType Directory -Force tests
```

**Windows (CMD):**
```cmd
mkdir tests
```

Create `tests/test_calc.py`:
```python
def test_divide():
    from calculator import divide
    assert divide(10, 2) == 5
    assert divide(0, 5) == 0
```

4. **Commit tests:**
```bash
git add tests/
git commit -m "test: add divide test"
git log --oneline
```

---

### Task 3: Merge Back to Main

```bash
git checkout main
git log --oneline                    # divide() not here yet
git merge feature/add-division       # observe: fast-forward or 3-way?
git log --oneline --graph --all      # see merged history
git branch -d feature/add-division   # clean up merged branch
```

---

### Task 4: Simulate & Resolve a Merge Conflict

```bash
# 1. On main — add '# Calculator v2.0' as first line
git checkout main
# macOS/Linux: prepend header
{ echo '# Calculator v2.0'; cat calculator.py; } > calculator.py.tmp && mv calculator.py.tmp calculator.py
git add calculator.py
git commit -m "docs: version header on main"

# 2. On feature — add '# Calculator utilities' as first line (SAME position = conflict)
git checkout feature/add-modulo
# macOS/Linux: prepend header
{ echo '# Calculator utilities'; cat calculator.py; } > calculator.py.tmp && mv calculator.py.tmp calculator.py
git add calculator.py
git commit -m "docs: version header on feature"

# 3. Merge — expect CONFLICT
git checkout main
git merge feature/add-modulo
# CONFLICT (content): Merge conflict in calculator.py

# 4. Check conflicted files
git status

# 5. Open calculator.py — find conflict markers, resolve:
# <<<<<<< HEAD
# # Calculator v2.0
# =======
# # Calculator utilities
# >>>>>>> feature/add-modulo
# Choose final version (e.g. # Calculator v2.0 (utilities)), delete ALL markers

# 6. Stage and complete merge
git add calculator.py
git commit
```

**Windows (PowerShell):** Use `{ "# Calculator v2.0"; Get-Content calculator.py } | Set-Content calculator.py` instead of the bash prepend.

**Escape hatch:** `git merge --abort` — cancels merge and restores pre-merge state.

---

### Task 5: Visualise Branch History

```bash
git log --oneline --graph --all --decorate
# Identify merge commit (asterisk with two parent lines diverging)

git log --oneline
git log --stat
git show HEAD
```

**Deliverables:** Multi-branch repo · Merge conflict resolved · `git log --graph` screenshot · Branch strategy notes

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `git branch` | List all local branches |
| `git branch <name>` | Create new branch |
| `git checkout <branch>` | Switch to branch |
| `git checkout -b <name>` | Create and switch |
| `git switch -c <name>` | Modern: create & switch |
| `git merge <branch>` | Merge into current branch |
| `git merge --no-ff` | Force merge commit |
| `git merge --squash` | Squash all into one commit |
| `git merge --abort` | Cancel in-progress merge |
| `git branch -d <name>` | Delete merged branch |
| `git branch -D <name>` | Force-delete branch |
| `git log --graph --all` | Visualise branch history |
| `git diff main..feature` | Changes between branches |

---

## Bonus Commands

```bash
# Force merge commit (preserve branch history)
git merge --no-ff feature/add-division

# See files changed per commit
git log --stat

# Delete branch after merge
git branch -d feature/add-division
```

---

## Branch Naming Conventions

| Prefix | Purpose |
|--------|---------|
| `main` / `master` | Production-ready code |
| `develop` | Integration branch |
| `feature/` | One branch per feature |
| `hotfix/` | Emergency production fix |
| `release/` | Pre-release stabilisation |

---

## Merge Conflict Markers

```
<<<<<<< HEAD
your current branch's version
=======
incoming branch's version
>>>>>>> feature/branch-name
```

**Resolution:** Delete all three marker lines, keep your chosen code, save, then `git add` and `git commit`.

---

*Day 3 Preview → Remote Repositories: origin, push, pull, fetch, SSH keys, clone vs fork*
