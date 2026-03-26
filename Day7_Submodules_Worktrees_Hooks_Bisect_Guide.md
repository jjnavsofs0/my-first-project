# Day 7: Submodules, Worktrees, Hooks & Bisect — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**
Module 7 of 8 · Composing Repos, Parallel Checkouts & Local Automation

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Submodules](#1-submodules)
  - [2. Worktrees](#2-worktrees)
  - [3. Hooks (Client-Side)](#3-hooks-client-side)
  - [4. Git Bisect](#4-git-bisect)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Create a Library Repo & Add as Submodule](#task-1-create-a-library-repo--add-as-submodule)
  - [Task 2: Clone a Repo With Submodules](#task-2-clone-a-repo-with-submodules)
  - [Task 3: Add a Git Worktree](#task-3-add-a-git-worktree)
  - [Task 4: Pre-Commit Hook (Block Empty Commits)](#task-4-pre-commit-hook-block-empty-commits)
  - [Task 5: Bisect to Find a Bad Commit](#task-5-bisect-to-find-a-bad-commit)
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
| Training root (macOS/Linux) | `~/Projects/trainings/git-18094` |
| Training root (Windows) | `%USERPROFILE%\Projects\trainings\git-18094` |
| Day 1 repo (macOS/Linux) | `~/Projects/trainings/git-18094/day1-lab` |
| Day 1 repo (Windows) | `%USERPROFILE%\Projects\trainings\git-18094\day1-lab` |

> **Prerequisite:** Days 1–6 completed — branching, merging, and remotes understood.

---

## Command Reference

### 1. Submodules

```bash
# Add a submodule (URL or path to another repo)
# Local path / file URLs: recent Git blocks file:// clones unless allowed:
git -c protocol.file.allow=always submodule add ../my-lib vendor/my-lib

# Initialise submodules after clone (use same -c when submodules use local paths)
git -c protocol.file.allow=always submodule update --init --recursive

# Shorthand without local paths:
git submodule update --init --recursive

# Pull submodule commits
cd vendor/my-lib && git pull && cd ../..
git add vendor/my-lib
git commit -m "chore: bump submodule"
```

### 2. Worktrees

```bash
# Second working directory — create NEW branch at current HEAD (-b required if branch does not exist)
git worktree add -b hotfix/urgent-fix ../day1-lab-hotfix

# List worktrees
git worktree list

# Remove when done
git worktree remove ../day1-lab-hotfix
```

### 3. Hooks (Client-Side)

Hooks live in `.git/hooks/` (or managed tools like **pre-commit**, **Husky**). Examples: `pre-commit`, `commit-msg`, `pre-push`.

```bash
chmod +x .git/hooks/pre-commit
```

### 4. Git Bisect

```bash
git bisect start
git bisect bad          # current commit is bad
git bisect good abc1234 # known good commit
# Git checks out middle; test, then:
git bisect good   # or bad
git bisect reset  # when finished
```

---

## Lab Guide

### Prerequisites

Use the training root so a **sibling** folder can hold a tiny library repo for submodules.

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094
```

**Windows (PowerShell):**
```powershell
cd $env:USERPROFILE\Projects\trainings\git-18094
```

---

### Task 1: Create a Library Repo & Add as Submodule

```bash
cd ~/Projects/trainings/git-18094
mkdir -p lib-helper && cd lib-helper
git init
echo "VERSION=1" > lib.conf
git add lib.conf
git commit -m "init: lib-helper"

cd ../day1-lab
git checkout -b chore/add-submodule
git -c protocol.file.allow=always submodule add ../lib-helper vendor/lib-helper
git submodule status
git commit -m "chore: add lib-helper submodule"
```

---

### Task 2: Clone a Repo With Submodules

Simulate a fresh clone (use a **new folder** so you do not delete your main lab):

```bash
cd ~/Projects/trainings/git-18094
git clone day1-lab day1-lab-submodule-test
cd day1-lab-submodule-test
git -c protocol.file.allow=always submodule update --init --recursive
ls vendor/lib-helper
cat vendor/lib-helper/lib.conf
```

When done you may remove the test folder: `rm -rf ~/Projects/trainings/git-18094/day1-lab-submodule-test`

---

### Task 3: Add a Git Worktree

```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout main
git worktree add -b hotfix/doc-typo ../day1-lab-wt
cd ../day1-lab-wt
echo "# hotfix worktree" >> README.md
git add README.md
git commit -m "docs: typo from worktree"
cd ../day1-lab
git worktree list
git merge hotfix/doc-typo -m "merge: doc fix from worktree"
git worktree remove ../day1-lab-wt
git branch -d hotfix/doc-typo
```

**Windows:** Use paths like `%USERPROFILE%\Projects\trainings\git-18094\day1-lab-wt`.

---

### Task 4: Pre-Commit Hook (Block Empty Commits)

```bash
cd ~/Projects/trainings/git-18094/day1-lab
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/sh
if git diff --cached --quiet; then
  echo "pre-commit: no staged changes"
  exit 1
fi
exit 0
EOF
chmod +x .git/hooks/pre-commit
```

Test: try `git commit` with nothing staged — hook should fail. Then stage a file and commit — should succeed.

---

### Task 5: Bisect to Find a Bad Commit

```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout main
# Create known good baseline tag
BASE=$(git rev-parse HEAD)
git checkout -b lab/bisect-demo
for i in 1 2 3 4 5; do
  echo "step $i" >> bisect_log.txt
  git add bisect_log.txt
  git commit -m "chore: bisect step $i"
done
# Introduce "bug" at tip
echo "BUG=1" >> bisect_log.txt
git add bisect_log.txt
git commit -m "feat: introduce bug for bisect"

git bisect start
git bisect bad
git bisect good "$BASE"

# When prompted on each checkout, inspect bisect_log.txt and run:
#   git bisect bad   # if BUG=1 is present
#   git bisect good  # if not

# Automated example (exit 0 = good, exit 1 = bad for git bisect run):
git bisect run sh -c 'grep -q BUG=1 bisect_log.txt && exit 1 || exit 0'

git bisect reset
git checkout main
git branch -D lab/bisect-demo
```

If `git bisect run` is unsupported or fails, use **manual** `git bisect good` / `git bisect bad` after each checkout.

**Deliverables:** Submodule committed · `git submodule update --init` verified · Worktree merge · Hook tested · Bisect identifies bug commit

---

## Hands-On Lab Setup

**macOS / Linux / Git Bash:**
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout main
```

**Windows (PowerShell):**
```powershell
cd $env:USERPROFILE\Projects\trainings\git-18094\day1-lab
git checkout main
```

---

## Lab Task Checklist

Condensed commands. Paths use `~/Projects/trainings/git-18094`; adjust for Windows.

### Task 1: Submodule
```bash
cd ~/Projects/trainings/git-18094
mkdir -p lib-helper && cd lib-helper && git init
echo "VERSION=1" > lib.conf && git add lib.conf && git commit -m "init: lib-helper"
cd ../day1-lab && git checkout -b chore/add-submodule
git -c protocol.file.allow=always submodule add ../lib-helper vendor/lib-helper
git commit -m "chore: add lib-helper submodule"
```

### Task 2: Clone with submodules
```bash
cd ~/Projects/trainings/git-18094
git clone day1-lab day1-lab-submodule-test
cd day1-lab-submodule-test && git -c protocol.file.allow=always submodule update --init --recursive
```

### Task 3: Worktree
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git worktree add -b hotfix/doc-typo ../day1-lab-wt
cd ../day1-lab-wt
echo "# hotfix" >> README.md && git add README.md && git commit -m "docs: from worktree"
cd ../day1-lab && git merge hotfix/doc-typo && git worktree remove ../day1-lab-wt && git branch -d hotfix/doc-typo
```

### Task 4: Hook
Create `.git/hooks/pre-commit` as in Lab Guide; `chmod +x` on Unix.

### Task 5: Bisect
Follow Task 5 in Lab Guide (manual bisect is fine).

**Deliverables:** Same as Lab Guide Task 5

---

## Quick Reference

| Topic | Command |
|-------|---------|
| Add submodule | `git submodule add <url-or-path> <path>` |
| After clone | `git submodule update --init --recursive` |
| Worktree | `git worktree add <path> <branch>` |
| List worktrees | `git worktree list` |
| Bisect | `git bisect start` · `good` · `bad` · `reset` |

---

## Bonus Commands

```bash
# Submodule: update remote URL
git config submodule.vendor/lib-helper.url NEW_URL

# Worktree: prune stale registration
git worktree prune

# bisect: visualize
git bisect visualize
```

---

*Day 8 Preview → CI/CD Integration, Git LFS & Signing: pipelines, large files, verified commits, course wrap-up*