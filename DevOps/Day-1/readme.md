

## 📌 1. What is Git?

Git is a **distributed version control system** that allows multiple people to work on the same project without overwriting each other’s work.

* Tracks changes in files
* Allows you to revert to previous versions
* Enables collaboration

**Example:** Think of Git as a time machine for your code.

---

## 📌 2. What is GitHub?

GitHub is a **cloud-based hosting platform** for Git repositories.

* Stores your code online
* Enables team collaboration
* Provides tools like pull requests, issues, and CI/CD integrations

**Example:** Git is the engine, GitHub is the car showroom.

---

## 📌 3. Why Use Git?

* Tracks history of changes
* Helps collaborate with teams
* Allows branching and merging
* Works offline
* Enables backup on platforms like GitHub, GitLab, Bitbucket

---

## 📌 4. Git Lifecycle

The Git workflow has **three main areas**:

1. **Working Directory** – Where you edit files.
2. **Staging Area** – Where you mark files to be committed.
3. **Repository** – Where commits are stored.

**Lifecycle Flow:**

```
Edit File → git add → git commit → git push
```
<img width="765" height="474" alt="image" src="https://github.com/user-attachments/assets/fe099374-f878-4df3-9f9b-274ab4fd9216" />

---

## 📌 5. Common Git Commands

### 🔹 Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### 🔹 Initialize a Repo

```bash
git init   # Start a new repo
git clone <repo_url>  # Clone an existing repo
```

### 🔹 Check Status

```bash
git status
```

### 🔹 Add & Commit Changes

```bash
git add file.txt           # Add specific file
git add .                  # Add all changes
git commit -m "Message"    # Commit changes
```

### 🔹 Push & Pull

```bash
git push origin main       # Push changes to remote
git pull origin main       # Get latest changes
```

### 🔹 Branching

```bash
git branch feature1        # Create new branch
git checkout feature1      # Switch to branch
git checkout -b feature1   # Create & switch
```

### 🔹 Merge Branch

```bash
git checkout main
git merge feature1
```

### 🔹 Delete Branch

```bash
git branch -d feature1           # Delete local branch
git push origin --delete feature1 # Delete remote branch
```

---

## 📌 6. Difference Between Git Merge and Git Rebase

| Feature           | Merge                             | Rebase                                |
| ----------------- | --------------------------------- | ------------------------------------- |
| History           | Keeps both branch histories       | Rewrites commit history               |
| Use Case          | When you want to preserve history | When you want a clean, linear history |
| Conflict Handling | Happens during merge              | Happens during rebase                 |

**Example:**

* Merge: Combines histories like a family tree
* Rebase: Moves commits as if they happened one after the other

---

## 📌 7. What is Git Cherry-Pick?

Cherry-pick lets you **apply a specific commit from one branch to another** without merging everything.

**Example:**

```bash
git checkout main
git cherry-pick <commit_hash>
```

Use when you want **only one commit** from another branch.

---

## 📌 8. Creating Branch & Pushing Changes (Step-by-Step)

```bash
# 1. Check current branch
git branch

# 2. Create new branch
git checkout -b feature-branch

# 3. Make changes to files
nano file.txt   # or any editor

# 4. Stage the changes
git add file.txt

# 5. Commit the changes
git commit -m "Added new feature"

# 6. Push the branch to remote
git push origin feature-branch
```

---

## 📌 9. Summary Diagram

```
Local Repo (Working Dir → Staging → Repo) ↔ Remote Repo (GitHub)
```

---

## 📌 10. Practice Exercise for Students

1. Create a GitHub account
2. Create a new repo
3. Clone it locally
4. Create a branch and add a new file
5. Commit and push changes
6. Merge branch into main
7. Try cherry-picking a commit

```

