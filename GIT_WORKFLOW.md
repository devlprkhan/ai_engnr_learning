# Git Workflow — ai_engnr_learning

This repository uses **Git submodules**.

The main repository is:

`ai_engnr_learning`

Individual projects are separate GitHub repositories and are included in this repository as submodules.

---

## Repository Structure

```text
ai_engnr_learning/
├── agentic_ai/
│   ├── langchain_learning/       # submodule
│   ├── langgraph_learning/       # submodule
│   └── rag_learning/             # submodule
├── dl_learning/                  # submodule
├── ml_learning/                  # submodule
├── projects/
│   ├── finance_tracker/          # submodule
│   ├── house-prices-ml/          # submodule
│   └── titanic-survival-pred/    # submodule
└── python_learning/
    ├── numpy_learning/           # normal folder
    └── pandas_learning/          # normal folder
```

---

# 1. Working on a Submodule

A submodule is its own Git repository.

For example:

```bash
cd dl_learning
```

Check its status:

```bash
git status
git branch --show-current
git remote -v
```

Make your changes normally.

Then:

```bash
git add .
git commit -m "Describe your changes"
git push origin main
```

The changes are now pushed to the **submodule's own GitHub repository**.

---

# 2. Update the Parent Repository

After pushing changes to a submodule, go back to the parent repository:

```bash
cd /path/to/ai_engnr_learning
```

Check:

```bash
git status
```

You may see:

```text
modified: dl_learning (new commits)
```

This means:

> The submodule has a newer commit, but the parent repository is still pointing to the old commit.

Update the parent repository's reference:

```bash
git add dl_learning
git commit -m "Update dl_learning"
git push origin main
```

That's it.

---

# 3. The Important Concept

There are **two commits** involved.

### Submodule

```text
dl_learning
      ↓
commit A → commit B
              ↓
        push to GitHub
```

### Parent repository

The parent repo stores a pointer:

```text
ai_engnr_learning
      ↓
dl_learning → commit B
```

Therefore:

```text
1. Commit + push inside submodule
2. Commit + push the submodule pointer in parent repo
```

Both steps are required.

---

# 4. Complete Example

Suppose you modify `ml_learning`.

### Step 1 — Enter submodule

```bash
cd ml_learning
```

### Step 2 — Make changes

Edit/create files normally.

### Step 3 — Commit and push the submodule

```bash
git add .
git commit -m "Add new ML notebook"
git push origin main
```

### Step 4 — Go back to parent

```bash
cd ..
```

or:

```bash
cd /path/to/ai_engnr_learning
```

### Step 5 — Check parent

```bash
git status
```

You should see something similar to:

```text
modified: ml_learning (new commits)
```

### Step 6 — Commit the new submodule pointer

```bash
git add ml_learning
git commit -m "Update ml_learning"
git push origin main
```

Now both repositories are updated.

---

# 5. Updating All Submodules on Another Machine

After cloning the parent repository:

```bash
git clone git@github.com:devlprkhan/ai_engnr_learning.git
cd ai_engnr_learning
```

Initialize and download all submodules:

```bash
git submodule update --init --recursive
```

For an already-cloned repository:

```bash
git pull origin main
git submodule update --init --recursive
```

---

# 6. Checking Submodule Versions

From the parent repository:

```bash
git submodule status
```

Example:

```text
268af23 dl_learning
7b94f92 ml_learning
8d30cf6 projects/finance_tracker
```

These hashes are the exact commits the parent repository is pointing to.

---

# 7. Pulling New Changes from Submodules

Normally, use:

```bash
git pull origin main
git submodule update --init --recursive
```

This checks out the exact submodule commits recorded by the parent repository.

If you specifically want the latest `main` commit from a submodule:

```bash
git submodule update --remote dl_learning
```

Then update the parent:

```bash
git add dl_learning
git commit -m "Update dl_learning"
git push origin main
```

---

# 8. Adding a New Project as a Submodule

First create/push the project as its own GitHub repository.

Then from the parent repository:

```bash
git submodule add -b main git@github.com:devlprkhan/REPOSITORY_NAME.git path/to/project
```

Example:

```bash
git submodule add -b main \
  git@github.com:devlprkhan/new_project.git \
  projects/new_project
```

Then:

```bash
git add .gitmodules projects/new_project
git commit -m "Add new project"
git push origin main
```

---

# 9. Normal Folders vs Submodules

Not everything needs to be a submodule.

### Normal learning material

```text
python_learning/
├── numpy_learning/
└── pandas_learning/
```

These are part of the parent repository.

Changes are committed directly:

```bash
git add .
git commit -m "Update Python learning material"
git push origin main
```

### Independent projects

Projects with their own GitHub repositories should be submodules:

```text
ml_learning/
dl_learning/
projects/finance_tracker/
```

Changes follow the **two-step workflow**:

```text
SUBMODULE
    ↓
commit
    ↓
push
    ↓
PARENT REPOSITORY
    ↓
commit updated submodule pointer
    ↓
push
```

---

# 10. Before Pushing — Quick Safety Check

### Inside a submodule

```bash
git status
git branch --show-current
git remote -v
```

Make sure:

```text
branch = main
remote = correct GitHub repository
```

Then:

```bash
git add .
git commit -m "Your message"
git push origin main
```

### Inside parent repository

```bash
git status
git submodule status
```

If a submodule shows:

```text
modified: some_project (new commits)
```

update its pointer:

```bash
git add some_project
git commit -m "Update some_project"
git push origin main
```

---

# 11. The Golden Rule

## If you changed files INSIDE a submodule:

```bash
cd submodule

git add .
git commit -m "Your changes"
git push origin main

cd ../..

git add submodule
git commit -m "Update submodule"
git push origin main
```

## If you changed files ONLY in the parent repo:

```bash
git add .
git commit -m "Your changes"
git push origin main
```

---

# 12. Most Common Mistake

Do **not** do this from the parent:

```bash
git add .
git commit
git push
```

and assume that changes inside a submodule have been pushed.

The parent repository does **not** contain the submodule's files.

It only stores:

```text
"Use commit XYZ from this repository."
```

Therefore, always push the submodule first, then update the parent pointer.

---

# Quick Cheat Sheet

```text
┌─────────────────────────────────────────────┐
│ CHANGED A SUBMODULE                         │
└─────────────────────────────────────────────┘

cd project

git status
git add .
git commit -m "Describe changes"
git push origin main

cd /path/to/ai_engnr_learning

git status
git add project
git commit -m "Update project"
git push origin main
```

```text
┌─────────────────────────────────────────────┐
│ CHANGED ONLY PARENT REPOSITORY              │
└─────────────────────────────────────────────┘

cd /path/to/ai_engnr_learning

git status
git add .
git commit -m "Describe changes"
git push origin main
```

```text
┌─────────────────────────────────────────────┐
│ CLONE / SET UP ON ANOTHER MACHINE           │
└─────────────────────────────────────────────┘

git clone git@github.com:devlprkhan/ai_engnr_learning.git
cd ai_engnr_learning
git submodule update --init --recursive
```

**Remember:**

> **Submodule changes → commit & push submodule → update & push parent.**
