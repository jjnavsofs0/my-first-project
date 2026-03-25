# Day 6: Git Workflow Patterns — Lab Guide & Command Reference

**Version Control with Git, GitHub & GitLab**  
Module 6 of 8 · Git Flow, GitHub Flow, GitLab Flow & Release Discipline

---

## Table of Contents

- [Current Setup](#current-setup)
- [Command Reference](#command-reference)
  - [1. Tags (Lightweight & Annotated)](#1-tags-lightweight--annotated)
  - [2. Long-Lived Branches (Git Flow–Style)](#2-long-lived-branches-git-flowstyle)
  - [3. GitHub Flow (Simplified)](#3-github-flow-simplified)
  - [4. GitLab Flow (Branches + Environments)](#4-gitlab-flow-branches--environments)
  - [5. Merge Strategies (Concept)](#5-merge-strategies-concept)
- [Lab Guide](#lab-guide)
  - [Prerequisites](#prerequisites)
  - [Task 1: Annotated Tags & Release History](#task-1-annotated-tags--release-history)
  - [Task 2: GitHub Flow — Short-Lived Feature Branch](#task-2-github-flow--short-lived-feature-branch)
  - [Task 3: Introduce `develop` (Git Flow–Style)](#task-3-introduce-develop-git-flowstyle)
  - [Task 4: Release Branch & Tag on `main`](#task-4-release-branch--tag-on-main)
  - [Task 5: Compare Flows & Clean Up](#task-5-compare-flows--clean-up)
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

> **Prerequisite:** Days 1–5 completed — comfortable with branches, merge, rebase, remotes, and tags (introduced here).

---

## Command Reference

### 1. Tags (Lightweight & Annotated)

```bash
# Lightweight tag (name only — points to current commit)
git tag v1.0.0

# Annotated tag (recommended for releases — message + signer metadata)
git tag -a v1.0.0 -m "Release 1.0.0"

# Tag a specific commit
git tag -a v0.9.0 abc1234 -m "Beta"

# List tags
git tag -l "v1.*"

# Show tag details
git show v1.0.0

# Push tags to remote
git push origin v1.0.0
git push origin --tags
```

### 2. Long-Lived Branches (Git Flow–Style)

| Branch | Typical role |
|--------|----------------|
| `main` | Production releases only |
| `develop` | Integration branch for the next release |
| `feature/*` | Branched from `develop` |
| `release/*` | Stabilisation before tagging on `main` |
| `hotfix/*` | Emergency fix from `main` |

```bash
git checkout -b develop main
git checkout -b feature/login develop
# ... work ...
git checkout develop
git merge --no-ff feature/login
```

### 3. GitHub Flow (Simplified)

- **`main` is always deployable.**
- Branch from `main` → open PR → review → merge → deploy.
- Prefer **short-lived branches** and small PRs.

```bash
git checkout main
git pull origin main
git checkout -b fix/typo-readme
# ... commit ...
git push -u origin fix/typo-readme
```

### 4. GitLab Flow (Branches + Environments)

- Branches map to stages: e.g. `main` → staging → production (via merge or promotion).
- Often combined with **merge requests** and **environment-specific** deployment jobs (CI/CD).

```bash
git checkout -b feature/api-pagination main
git push -u origin feature/api-pagination
```

### 5. Merge Strategies (Concept)

| Strategy | When |
|----------|------|
| **Fast-forward** | Linear history, no divergence |
| **Merge commit** (`--no-ff`) | Preserve branch context on `develop` / `main` |
| **Squash merge** | One commit per feature (GitHub/GitLab UI) |
| **Rebase** | Linear history on feature branch before merge |

---

## Lab Guide

### Prerequisites

```bash
cd ~/Projects/trainings/git-18094/day1-lab
git status
git checkout main
git pull origin main 2>/dev/null || true
```

**Windows:** `cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab`

---

### Task 1: Annotated Tags & Release History

```bash
git checkout main
git tag -a v0.1.0 -m "practice: annotated tag"
git tag
git show v0.1.0
git log --oneline --decorate -5
```

---

### Task 2: GitHub Flow — Short-Lived Feature Branch

```bash
git checkout main
git pull origin main 2>/dev/null || true
git checkout -b chore/workflow-readme-note
echo "" >> README.md
echo "<!-- GitHub Flow: small branch from main -->" >> README.md
git add README.md
git commit -m "docs: note GitHub Flow practice"
git checkout main
git merge --no-ff chore/workflow-readme-note -m "merge: GitHub Flow README note"
git branch -d chore/workflow-readme-note
git log --oneline --graph --decorate -8
```

---

### Task 3: Introduce `develop` (Git Flow–Style)

```bash
git checkout main
git checkout -b develop
echo "# develop integration branch" > WORKFLOW.md
git add WORKFLOW.md
git commit -m "chore: add develop branch marker"
git checkout -b feature/greet-user develop
echo "GREETING=hello" >> WORKFLOW.md
git add WORKFLOW.md
git commit -m "feat: add greeting config"
git checkout develop
git merge --no-ff feature/greet-user -m "merge: feature greet-user"
git branch -d feature/greet-user
git log --oneline --graph --all --decorate -10
```

---

### Task 4: Release Branch & Tag on `main`

```bash
git checkout develop
git checkout -b release/1.0.0
echo "VERSION=1.0.0" >> WORKFLOW.md
git add WORKFLOW.md
git commit -m "chore: bump version for release"

git checkout main
git merge --no-ff release/1.0.0 -m "release: v1.0.0"
git tag -a v1.0.0 -m "Release 1.0.0"

git checkout develop
git merge main -m "merge: sync main release into develop"
git branch -d release/1.0.0

git log --oneline --graph --all --decorate -12
```

---

### Task 5: Compare Flows & Clean Up

1. Sketch on paper (or notes): **GitHub Flow** (main + short branches) vs **Git Flow** (`main` + `develop` + releases).
2. List when you would use **squash merge** vs **merge commit** vs **rebase** (team rules).
3. Optional cleanup (local only):

```bash
git tag -d v0.1.0   # only if you want to remove practice tag locally
# Remote: git push origin :refs/tags/v0.1.0  # deletes remote tag — use with care
```

**Deliverables:** Annotated tag · `develop` + feature merge · Release merge to `main` with `v1.0.0` · Short written comparison of two flows

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

**Windows (CMD):**
```cmd
cd %USERPROFILE%\Projects\trainings\git-18094\day1-lab
git checkout main
```

---

## Lab Task Checklist

Condensed commands. For rationale and diagrams, see the [Lab Guide](#lab-guide).

### Task 1: Tags
```bash
cd ~/Projects/trainings/git-18094/day1-lab
git checkout main
git tag -a v0.1.0 -m "practice: annotated tag"
git show v0.1.0
```

### Task 2: GitHub Flow Branch
```bash
git checkout -b chore/workflow-readme-note
echo "" >> README.md && echo "<!-- GitHub Flow -->" >> README.md
git add README.md && git commit -m "docs: note GitHub Flow practice"
git checkout main && git merge --no-ff chore/workflow-readme-note -m "merge: GitHub Flow README note"
git branch -d chore/workflow-readme-note
```

### Task 3: develop + feature
```bash
git checkout -b develop
echo "# develop integration branch" > WORKFLOW.md
git add WORKFLOW.md && git commit -m "chore: add develop branch marker"
git checkout -b feature/greet-user develop
echo "GREETING=hello" >> WORKFLOW.md
git add WORKFLOW.md && git commit -m "feat: add greeting config"
git checkout develop && git merge --no-ff feature/greet-user -m "merge: feature greet-user"
git branch -d feature/greet-user
```

### Task 4: Release + tag on main
```bash
git checkout -b release/1.0.0 develop
echo "VERSION=1.0.0" >> WORKFLOW.md
git add WORKFLOW.md && git commit -m "chore: bump version for release"
git checkout main && git merge --no-ff release/1.0.0 -m "release: v1.0.0"
git tag -a v1.0.0 -m "Release 1.0.0"
git checkout develop && git merge main -m "merge: sync main into develop"
git branch -d release/1.0.0
```

### Task 5: Notes + optional `git tag -d v0.1.0`

**Deliverables:** Same as Lab Guide Task 5

---

## Quick Reference

| Topic | Command / idea |
|-------|----------------|
| Annotated tag | `git tag -a vX.Y.Z -m "msg"` |
| Push all tags | `git push origin --tags` |
| Git Flow | `main` + `develop` + `release/*` + `feature/*` |
| GitHub Flow | Branch from `main`, PR, merge, deploy |
| GitLab Flow | Branch per environment + MR + CI/CD |
| Merge commit | `git merge --no-ff` |

---

## Bonus Commands

```bash
# Optional: git-flow extension (install separately)
# git flow init

# List branches merged into main
git branch --merged main

# Pretty log
git log --oneline --graph --all --decorate --boundary
```

---

*Day 7 Preview → Submodules, Worktrees, Hooks & Bisect: isolate dependencies, parallel worktrees, automation, binary search for bugs*
