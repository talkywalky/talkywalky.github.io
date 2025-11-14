---
layout: documentation
title: "Git"
slug: git
short_description: "How to use Git."
last_update: 2025-11-14
---

## What is Git?

Git is a distributed version control system that helps you track changes to files, collaborate with others, and safely experiment. You can:
- Save checkpoints of your work (commits)
- Create branches to try ideas without breaking the main line
- Sync with remote services like GitHub, GitLab, or Bitbucket

This guide assumes no prior Git knowledge and focuses on practical, copy‑pasteable commands.

## Install Git

- Windows: Install "Git for Windows" from the official site. During setup, the defaults are fine. You will get a "Git Bash" terminal.
- macOS: `brew install git` (with Homebrew) or install Xcode Command Line Tools: `xcode-select --install`.
- Linux (Debian/Ubuntu): `sudo apt-get update && sudo apt-get install -y git`
- Verify:
```bash
git --version
```

## First-time setup (once per computer)

Set your identity so commits are labeled correctly:
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Optional, recommended quality-of-life settings:
```bash
git config --global init.defaultBranch main
git config --global pull.rebase true
git config --global core.autocrlf input # Safe line endings on macOS/Linux; use 'true' on Windows
```

Check your configuration:
```bash
git config --list --show-origin
```

## SSH keys (recommended for GitHub)

1. Check for an existing key:
```bash
ls -al ~/.ssh
```

2. Create a new key (press Enter to accept defaults, optionally set a passphrase):
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

3. Start the SSH agent and add your key:
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

4. Copy your public key and add it to your Git hosting (GitHub → Settings → SSH and GPG keys → New SSH key):
```bash
cat ~/.ssh/id_ed25519.pub
```

## Create or get a repository

- Start a new repo in the current folder:
```bash
git init
```

- Clone an existing repo (HTTPS):
```bash
git clone https://github.com/<user>/<repo>.git
```

- Clone with SSH (after setting keys):
```bash
git clone git@github.com:<user>/<repo>.git
```

## Core ideas in 30 seconds

- Working directory: your actual files.
- Staging area (index): a holding area where you choose what to include in the next commit.
- Commit: a snapshot of staged changes with a message.
- Branch: a movable pointer to a line of commits (e.g., `main`, `feature/login`).
- Remote: a copy of your repo stored elsewhere (e.g., `origin` on GitHub).

## Your first workflow (most common commands)

1. See what changed:
```bash
git status
```

2. Stage files to include in a commit:
```bash
git add <file1> <file2>
# or everything:
git add .
```

3. Commit with a message:
```bash
git commit -m "feat: Describe what you changed"
```

4. Push your changes to GitHub:
```bash
git push origin main
```

5.  Get changes other people pushed:
```bash
git pull origin main
```

Tip: If your default branch is called `master`, replace `main` with `master`.

## Branching: work safely without breaking main

- Create and switch to a new branch:
```bash
git checkout -b feature/cool-thing
```

- Switch back to an existing branch:
```bash
git checkout main
```

- List branches:
```bash
git branch
```

- Delete a local branch (after merging):
```bash
git branch -d feature/cool-thing
```

## Basic collaboration with GitHub (Pull Requests)

1. Create a branch and push it:
```bash
git checkout -b feature/add-button
# ...edit files...
git add .
git commit -m "feat: Add primary button"
git push -u origin feature/add-button
```
2. On GitHub, open a Pull Request (PR) from your branch into `main`.
3. Get review, address comments by committing more changes to the same branch, push again.
4. Merge via the GitHub UI when approved. Pull the latest `main` locally.

## See history and differences

- Log (compact, oneline):
```bash
git log --oneline --graph --decorate --all
```

- Show what changed (not staged yet):
```bash
git diff
```

- Show what is staged for commit:
```bash
git diff --staged
```

- See who changed a line (blame/annotate):
```bash
git blame <file>
```

## Remotes and syncing

- Show remotes:
```bash
git remote -v
```

- Add a remote named `origin`:
```bash
git remote add origin git@github.com:<user>/<repo>.git
```

- Fetch (download) changes without merging:
```bash
git fetch origin
```

- Pull (fetch + merge):
```bash
git pull origin main
```

- Push a new local branch and set upstream:
```bash
git push -u origin my-branch
```

## Ignoring files with .gitignore

Create a `.gitignore` file to exclude build outputs, secrets, and temp files from commits.
Examples:
```
# Node
node_modules/
*.log

# macOS
.DS_Store

# Python
__pycache__/
*.pyc
```
If you change `.gitignore` after files are already tracked, untrack them:
```bash
git rm -r --cached node_modules
```

## Undo and recovery (safe first)

- Undo local unstaged changes to a file:
```bash
git restore <file>
```

- Unstage a file (keep changes in working directory):
```bash
git restore --staged <file>
```

- Amend the last commit message (no new changes):
```bash
git commit --amend -m "feat: New message"
```

- Create a new commit that reverts a previous one (good for public branches):
```bash
git revert <commit_sha>
```

- Move branch pointer backward (be careful; rewrites history):
```bash
git reset --hard <commit_sha>
```
Avoid using `--hard` on shared branches like `main`.

## Stashing: save work temporarily

If you need to switch branches but aren’t ready to commit:
```bash
git stash # save current modifications
# ...switch branches, pull, etc.
git stash list # see stashes
git stash apply # re-apply latest stash (keeps it in stash)
# or
git stash pop # apply and remove from stash
```

## Tags (mark releases)

- Create an annotated tag:
```bash
git tag -a v1.0.0 -m "Release v1.0.0"
```

  - Push tags:
```bash
git push --tags
```

- Delete tag:
```bash
git tag -d v1.0.0
```

## Common problems and fixes

- Permission denied (SSH): ensure your SSH key is added and you’re using the SSH URL.

- `fatal: not a git repository`: run `git init` or ensure you’re in the correct folder.

- `Updates were rejected because the remote contains work that you do not have`:
```bash
git pull --rebase origin main   # or: git pull origin main
# resolve conflicts if any, then push
git push origin main
```

- Accidentally committed secrets: rotate the secret. To purge from history, use tools like `git filter-repo` (advanced) and force-push after team agreement.


## Glossary (very short)

- Commit: A saved snapshot with a message.
- Branch: A pointer to a series of commits.
- Merge: Combine histories, preserving both lines of development.
- Rebase: Replay commits on top of another base (rewrites history). Beginners can skip.
- Remote: A repository hosted elsewhere (e.g., GitHub).
- PR (Pull Request): A proposal to merge changes from one branch to another.


## Quick cheat sheet

```bash
# Setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# New project or get existing
git init
git clone <url>

# Everyday
git status
git add <file> | git add .
git commit -m "message"
git pull origin main
git push origin main

# Branching
git checkout -b feature/x
git checkout main
git merge feature/x

# Inspect
git log --oneline --graph --decorate --all
git diff

# Undo (safe)
git restore <file>
git restore --staged <file>

# Stash
git stash && git stash pop
```
