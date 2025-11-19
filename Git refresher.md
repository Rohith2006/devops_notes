# 📘 Notes: Introduction to Git & GitHub (Day 1)

## 🧠 What Is Git?

Git is a **distributed source-code management (SCM)** tool that tracks and saves every change in your project. It enables safe collaboration, version history, branching, and backups.

### 💡 Why Git?

* Avoid file confusion like `final_v3_really_final.py`
* Track every change forever
* Work offline
* Collaborate safely without overwriting each other’s code

---

## 🏰 Types of SCM

| Type                | Where Code Lives          | Example | Notes                   |
| ------------------- | ------------------------- | ------- | ----------------------- |
| **Local SCM**       | Only on one machine       | RCS     | No collaboration        |
| **Remote SCM**      | Central server            | SVN     | Needs network           |
| **Distributed SCM** | Each person has full copy | **Git** | Offline + collaboration |

---

## ⚙️ Installing Git

```bash
sudo su
yum install git
# or
apt update -y
apt install git
```

Verify:

```bash
git --version
```

---

## 🪪 Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global core.editor "vim"
```

---

## 🔐 SSH Setup (For GitHub)

```bash
ssh-keygen -t rsa -b 4096 -C "your@email.com"
```

Add public key to → GitHub Settings → SSH Keys.

---

## 🏙️ GitHub Overview

GitHub is a **platform for hosting repositories**, managing issues, collaborating through pull requests, and versioning projects.

Steps:

1. Create GitHub account
2. Create a new repository
3. Connect your local repo with GitHub

---

# 🌱 Starting a Git Project

```bash
mkdir demo
cd demo
git init
ls -a    # shows .git hidden folder
```

`.git` = local repository storing history.

---

# 🔄 Four Key Areas of Git

| Area                  | Also Called   | Description               | Example Command    |
| --------------------- | ------------- | ------------------------- | ------------------ |
| **Working Tree**      | Workspace     | Real files you edit       | `git status`       |
| **Staging Area**      | Index / Cache | Files prepared for commit | `git add file.txt` |
| **Local Repository**  | .git          | Full history & commits    | `git commit`       |
| **Remote Repository** | origin        | GitHub copy               | `git push`         |

---

# 📥 Tracking Files

```bash
touch Abc.txt Xyz.txt
git status   # untracked
git add Abc.txt
git status   # Abc staged, Xyz untracked
```

---

# 📝 Commit Changes

```bash
git commit -m "Learn git commit"
```

Each commit = snapshot of project at a point in time.

---

# 🌐 Connecting to GitHub

```bash
git remote add origin git@github.com:user/repo.git
git push origin main
```

---

# 🔍 Viewing Differences

| Command             | Shows                                     |
| ------------------- | ----------------------------------------- |
| `git diff`          | unstaged changes (working tree vs index)  |
| `git diff --cached` | staged changes (index vs last commit)     |
| `git status`        | file states (untracked, modified, staged) |

---

# 📚 Useful Git Commands (Quick List)

### Working with Files

```bash
git add file.txt
git add .
git restore --staged file.txt
git commit -m "msg"
git commit --amend
```

### Branching

```bash
git branch
git switch -c feature/x
git merge feature/x
git rebase main
```

### Remote Operations

```bash
git clone <url>
git fetch origin
git pull
git push
git push -u origin feature/x
```

### Tags

```bash
git tag v1.0
git tag -a v1.0 -m "release"
git push --tags
```

### Stashing

```bash
git stash
git stash list
git stash pop
```

### Undo & Recovery

```bash
git revert <commit>
git reset --soft HEAD~1
git reset --hard HEAD~1
git reflog
```

---

# 🧭 Typical Git Workflow

```
Working Tree 
   ↓ git add
Staging Area 
   ↓ git commit
Local Repository 
   ↓ git push
Remote Repository (GitHub)
```

To download others' work:

```
git pull
```

---

# 🏁 Summary

* Git is a **distributed version control system**.
* `.git` directory holds **entire history**.
* Workflow is **Working Tree → Staging → Commit → Push**.
* GitHub allows **collaboration**, **pull requests**, and **backups**.
* Use `git status`, `git diff`, `git log` to inspect history anytime.


