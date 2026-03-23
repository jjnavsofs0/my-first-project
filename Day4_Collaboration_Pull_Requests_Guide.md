# Day 4: Collaboration & Pull Requests — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**  
Module 4 of 8 · Collaboration Workflows & Pull Requests

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Fork & PR Workflow](#1-fork--pr-workflow)
  - [2. Branch & PR Workflow](#2-branch--pr-workflow)
  - [3. PR Lifecycle Commands](#3-pr-lifecycle-commands)
  - [4. GitHub CLI (Optional)](#4-github-cli-optional)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Fork the Trainer's Repository](#task-1-fork-the-trainers-repository)
  - [Task 2: Create Feature Branch & Commit](#task-2-create-feature-branch--commit)
  - [Task 3: Push & Open PR](#task-3-push--open-pr)
  - [Task 4: Review a Peer's PR](#task-4-review-a-peers-pr)
  - [Task 5: Address Feedback & Merge](#task-5-address-feedback--merge)
- [Quick Reference](#quick-reference)
- [Code Review Best Practices](#code-review-best-practices)

---

## Current Setup

| Setting | Value |
|---------|-------|
| `user.name` | jjnavsofs0 |
| `user.email` | jaisingh1006@gmail.com |
| Day 1 repo (macOS/Linux) | `~/Projects/trainings/git-18094/day1-lab` |
| Day 1 repo (Windows) | `%USERPROFILE%\Projects\trainings\git-18094\day1-lab` |

> **Prerequisite:** Days 1–3 completed — remote repos and SSH configured. **Need:** GitHub/GitLab account, partner for review.

---

## Command Reference

### 1. Fork & PR Workflow

```bash
# 1. Fork on GitHub/GitLab UI → clone YOUR fork
git clone git@github.com:jjnavsofs0/sample-repo.git
cd sample-repo

# 2. Add upstream (original repo)
git remote add upstream git@github.com:TRAINER-USER/sample-repo.git

# 3. Sync with original
git fetch upstream
git pull upstream main

# 4. Create feature branch
git checkout -b feature/add-power-function

# 5. Make changes, commit, push to YOUR fork
git add -A
git commit -m "feat: add power() function"
git push -u origin feature/add-power-function

# 6. Open PR on GitHub: your fork → upstream repo
```

### 2. Branch & PR Workflow

```bash
# 1. Clone shared repo (you have write access)
git clone git@github.com:org/repo.git
cd repo

# 2. Create branch from main
git checkout main
git pull origin main
git checkout -b feature/add-search-endpoint

# 3. Commit and push to SAME repo
git add -A
git commit -m "feat: add search endpoint"
git push -u origin feature/add-search-endpoint

# 4. Open PR within same repo
```

### 3. PR Lifecycle Commands

```bash
# Push branch & set tracking
git push -u origin feature/branch-name

# Sync fork with upstream
git pull upstream main

# Fetch upstream (no merge)
git fetch upstream

# After PR merged: get latest
git checkout main
git pull origin main

# Delete local branch
git branch -d feature/branch-name

# Rebase branch on main (before PR)
git rebase main
git push --force-with-lease
```

### 4. GitHub CLI (Optional)

```bash
# Create PR
gh pr create

# List PRs
gh pr list

# Checkout PR locally
gh pr checkout 42

# Approve PR
gh pr review --approve

# Merge PR
gh pr merge
```

---

## Lab Guide

### Prerequisites

- GitHub account with SSH configured
- Trainer's sample repo URL (shared in class)
- Partner for Task 4 review

---

### Task 1: Fork the Trainer's Repository

1. Navigate to trainer's sample repo link
2. Click **Fork** → select your account
3. Clone **YOUR fork** (not the original):

```bash
git clone git@github.com:jjnavsofs0/sample-repo.git
cd sample-repo
```

4. Verify and add upstream:

```bash
git remote -v
# origin should point to YOUR fork

git remote add upstream <TRAINER-REPO-SSH-URL>
git fetch upstream
```

---

### Task 2: Create Feature Branch & Commit

```bash
# Create branch (use your name)
git checkout -b feature/add-jjnavsofs0-function

# Add new function to calculator.py (e.g. power(base, exp))
# Add test in tests/test_calc.py

git add -A
git commit -m "feat: add power() function"
git commit -m "test: add power() test"  # or combine in one commit
```

**Example `power()` function:**
```python
def power(base, exp):
    return base ** exp
```

---

### Task 3: Push & Open PR

```bash
# Push and set tracking
git push -u origin feature/add-jjnavsofs0-function

# Open PR on GitHub:
# Base: upstream main (or trainer's main)
# Compare: your feature branch
# Title: feat: add power() function
# Fill PR template: Summary, Changes, How to Test
```

---

### Task 4: Review Your Partner's Pull Request

1. Open partner's PR from repo's **Pull Requests** tab
2. Read description before looking at code
3. Click **Files Changed** to review diff
4. Add **2+ inline comments** (click line number)
5. Use **Suggestion** feature for at least one fix
6. **Submit Review** → Request Changes or Approve
7. Be specific and constructive

---

### Task 5: Address Feedback & Complete Merge

1. Read ALL review comments on your PR
2. Reply to each comment
3. Make changes locally:

```bash
# Make fixes
git add -A
git commit -m "fix: address review feedback"
git push
```

4. Click **Re-request review** on GitHub
5. After approval → **Merge** (squash or merge commit)
6. Delete branch (local + remote)

```bash
git checkout main
git pull origin main
git branch -d feature/add-jjnavsofs0-function
```

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `git push -u origin branch` | Push & set tracking |
| `git pull upstream main` | Sync fork with original |
| `git remote add upstream <url>` | Add upstream |
| `git fetch upstream` | Fetch without merge |
| `git rebase main` | Update branch before PR |
| `git push --force-with-lease` | Safe force push after rebase |

---

## Code Review Best Practices

**For Reviewer:**
- ✅ Read PR description first
- ✅ Look for logic errors
- ✅ Ask questions, not accusations
- ✅ Use Suggestion feature for fixes
- ❌ Don't nitpick formatting

**For Author:**
- ✅ Keep PRs small (< 400 lines)
- ✅ Clear description: What? Why? How to test?
- ✅ Self-review before requesting review
- ✅ Link to issue if applicable

---

## Fork vs Branch Flow

| Flow | When to Use |
|------|-------------|
| **Fork & PR** | Contributing to repos you don't own |
| **Branch & PR** | Team repos with write access |

---

*Day 5 Preview → Advanced Git: rebase, cherry-pick, stash, reset vs revert, reflog*
