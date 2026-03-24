# Day 5: Advanced Git Commands — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**  
Module 5 of 8 · Advanced Git Commands & History Management

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Rebase](#1-rebase)
  - [2. Cherry-Pick](#2-cherry-pick)
  - [3. Stash](#3-stash)
  - [4. Reset vs Revert](#4-reset-vs-revert)
  - [5. Reflog](#5-reflog)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Rebase Feature Branch](#task-1-rebase-feature-branch)
  - [Task 2: Interactive Rebase (Squash)](#task-2-interactive-rebase-squash)
  - [Task 3: Cherry-Pick a Commit](#task-3-cherry-pick-a-commit)
  - [Task 4: Stash & Restore](#task-4-stash--restore)
  - [Task 5: Reflog Recovery](#task-5-reflog-recovery)
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

> **Prerequisite:** Days 1–4 completed. Run `git log --oneline` — verify 5+ commits.

---

## Command Reference

### 1. Rebase

```bash
# Standard rebase: replay commits onto main
git checkout feature/branch
git rebase main

# Interactive rebase: squash last N commits
git rebase -i HEAD~3

# After resolving conflict during rebase
git add <file>
git rebase --continue

# Cancel rebase
git rebase --abort
```

**Interactive rebase commands:** `pick` | `reword` | `edit` | `squash` | `fixup` | `drop`

⚠️ **Golden Rule:** NEVER rebase commits already pushed to a shared branch.

### 2. Cherry-Pick

```bash
# Apply one commit to current branch
git cherry-pick <SHA>

# Cherry-pick range
git cherry-pick abc123..def456

# Stage only (no commit)
git cherry-pick --no-commit <SHA>

# Abort cherry-pick
git cherry-pick --abort
```

### 3. Stash

```bash
# Stash changes
git stash
git stash push -m "WIP: welcome message"

# Stash including untracked
git stash -u

# List stashes
git stash list

# Apply and remove from stack
git stash pop

# Apply but keep in stack
git stash apply

# Drop stash
git stash drop
```

### 4. Reset vs Revert

```bash
# RESET (local only — rewrites history)

# --soft: undo commit, keep changes STAGED
git reset --soft HEAD~1

# --mixed: undo commit, keep changes UNSTAGED (default)
git reset --mixed HEAD~1

# --hard: undo commit, DISCARD changes
git reset --hard HEAD~1

# REVERT (safe for shared branches — creates undo commit)
git revert <SHA>
git revert <SHA> --no-edit
```

### 5. Reflog

```bash
# Show HEAD movement history
git reflog

# Reflog for specific branch
git reflog show main

# With timestamps
git reflog --date=iso

# Recover: checkout or reset to reflog entry
git checkout -b recovered HEAD@{2}
git reset --hard HEAD@{1}
```

---

## Lab Guide

### Prerequisites

```bash
cd ~/Projects/trainings/git-18094/day1-lab
git log --oneline   # verify 5+ commits
```

**Windows:** `cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab`

---

### Task 1: Rebase Feature Branch onto Main

```bash
# 1. Create fresh branch
git checkout -b feature/rebase-practice

# 2. Add 2 commits (e.g. power() and modulo())
printf '\ndef power(a, b):\n    return a ** b\n' >> calculator.py
git add calculator.py && git commit -m "feat: add power function"

printf '\ndef modulo(a, b):\n    return a %% b\n' >> calculator.py
git add calculator.py && git commit -m "feat: add modulo function"

# 3. Add commit on main (simulate teammate)
git checkout main
echo '# Updated' >> README.md
git add README.md && git commit -m "docs: update README"

# 4. Rebase feature onto main
git checkout feature/rebase-practice
git log --oneline --graph --all   # observe diverged history
git rebase main
git log --oneline --graph --all   # observe linear history
```

---

### Task 2: Interactive Rebase — Squash 3 Commits

```bash
# 1. On feature/rebase-practice, add 3 small commits
git checkout feature/rebase-practice
echo 'print("a")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 1"
echo 'print("b")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 2"
echo 'print("c")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 3"

# 2. Squash last 3 into 1
git rebase -i HEAD~3
# In editor: change "pick" to "squash" (or "s") for 2nd and 3rd commits
# Save → edit combined message → save

# 3. Verify
git log --oneline
```

> **Editor:** `git rebase -i` opens your `GIT_EDITOR`. To squash non-interactively (macOS):  
> `GIT_SEQUENCE_EDITOR="sed -i '' '2,3s/^pick/squash/'" GIT_EDITOR=true git rebase -i HEAD~3`  
> On Linux, use `sed -i '2,3s/^pick/squash/'` (no `''` after `-i`).

---

### Task 3: Cherry-Pick a Commit

```bash
# 1. List commits on the feature branch
git log feature/rebase-practice --oneline

# 2. Switch to main
git checkout main

# 3. Cherry-pick one commit by SHA — pick a change that applies cleanly to main
#    (e.g. "feat: add power function"). Avoid cherry-picking the tip if it is a
#    squashed "wip" commit that overlaps files and causes conflicts.
git cherry-pick <SHA-from-feature>
# Example (after Task 2), if your log shows f187c41 feat: add power function:
#   git cherry-pick f187c41

# 4. Verify
git log --oneline -3
```

---

### Task 4: Stash & Restore Mid-Task Work

```bash
# 1. Create branch with WIP
git checkout main
git checkout -b feature/new-ui

# 2. Edit file (DON'T commit) — must have unstaged changes or stash is empty
printf '\nprint("Welcome")\n' >> calculator.py

# 3. Stash
git status
git stash push -m "welcome message WIP"

# 4. Verify clean
git status

# 5. Switch context
git checkout main
echo '# Comment' >> README.md
git add README.md && git commit -m "docs: add comment"

# 6. Restore WIP
git checkout feature/new-ui
git stash pop
git status   # your changes are back
```

---

### Task 5: Simulate Reset --hard & Recover with Reflog

```bash
# 1. Note current state
git checkout main
git log --oneline
LAST_SHA=$(git rev-parse HEAD)

# 2. INTENTIONALLY reset (simulate accident)
git reset --hard HEAD~3

# 3. Recover using reflog
git reflog
# HEAD@{0} = the reset itself
# HEAD@{1} = usually your state BEFORE the reset — use that to recover
# If you ran other commands after the reset, pick the reflog entry *before*
# "reset: moving to" from `git reflog` and use: git reset --hard <that-SHA>

git reset --hard HEAD@{1}

# 4. Verify recovery
git log --oneline
```

---

## Hands-On Lab Setup

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git log --oneline   # verify 5+ commits
```

**Windows (PowerShell):**
```powershell
cd $env:USERPROFILE\Projects\trainings\git-18094\day1-lab
git log --oneline
```

**Windows (CMD):**
```cmd
cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab
git log --oneline
```

---

## Lab Task Checklist

Condensed commands for the full lab. For context and edge cases, see the [Lab Guide](#lab-guide).

### Task 1: Rebase Feature Branch onto Main
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout -b feature/rebase-practice
printf '\ndef power(a, b):\n    return a ** b\n' >> calculator.py
git add calculator.py && git commit -m "feat: add power function"
printf '\ndef modulo(a, b):\n    return a %% b\n' >> calculator.py
git add calculator.py && git commit -m "feat: add modulo function"
git checkout main
echo '# Updated' >> README.md
git add README.md && git commit -m "docs: update README"
git checkout feature/rebase-practice
git log --oneline --graph --all
git rebase main
git log --oneline --graph --all
```

### Task 2: Interactive Rebase (Squash)
```bash
git checkout feature/rebase-practice
echo 'print("a")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 1"
echo 'print("b")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 2"
echo 'print("c")' >> calculator.py
git add calculator.py && git commit -m "wip: debug 3"
git rebase -i HEAD~3
# In editor: squash 2nd and 3rd commits — see Lab Guide
git log --oneline
```

### Task 3: Cherry-Pick
```bash
git log feature/rebase-practice --oneline
git checkout main
# Use a SHA that applies cleanly (e.g. feat: add power function), not a conflicting tip
git cherry-pick <SHA-from-feature>
git log --oneline -3
```

### Task 4: Stash & Restore
```bash
git checkout main
git checkout -b feature/new-ui
printf '\nprint("Welcome")\n' >> calculator.py
git stash push -m "welcome message WIP"
git checkout main
echo '# Comment' >> README.md
git add README.md && git commit -m "docs: add comment"
git checkout feature/new-ui
git stash pop
```

### Task 5: Reflog Recovery
```bash
git checkout main
git log --oneline
git reset --hard HEAD~3
git reflog
git reset --hard HEAD@{1}
git log --oneline
```

**Deliverables:** Rebase · Interactive squash · Cherry-pick · Stash workflow · Reflog recovery

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `git rebase main` | Rebase onto main |
| `git rebase -i HEAD~N` | Interactive rebase |
| `git cherry-pick <SHA>` | Apply one commit |
| `git stash` | Stash changes |
| `git stash pop` | Restore stash |
| `git reset --soft HEAD~1` | Undo commit, keep staged |
| `git reset --hard HEAD~1` | Undo commit, discard |
| `git revert <SHA>` | Safe undo (new commit) |
| `git reflog` | View HEAD history |

---

## Bonus Commands

```bash
# Amend last commit
git commit --amend -m "New message"

# Rebase onto specific base
git rebase --onto main feature/old feature/new

# Find bug-introducing commit
git bisect start
git bisect bad
git bisect good <known-good-SHA>
```

---

## Reset Modes Summary

| Mode | Commits | Staging | Working Dir |
|------|---------|---------|-------------|
| `--soft` | Undone | Kept | Kept |
| `--mixed` | Undone | Cleared | Kept |
| `--hard` | Undone | Cleared | Cleared |

---

*Day 6 → Git Workflow Patterns (`lab6/Day6_Git_Workflow_Patterns_Guide.md`) · Day 7 → Submodules, Worktrees, Hooks & Bisect (`lab7/…`) · Day 8 → CI/CD, LFS & Signing (`lab8/…`)*
