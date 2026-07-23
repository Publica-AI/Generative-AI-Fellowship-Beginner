# Demo Guide - Core Git Workflow
**Module 3, Topic 9 | Estimated duration: 35–40 minutes**

---

## What This Demo Teaches

- How to initialise a Git repository with `git init`
- How to check file states with `git status`
- How to stage files with `git add`
- How to save snapshots with `git commit`
- How to view history with `git log --oneline`
- How to see changes with `git diff`
- How to create a `.gitignore` file

---

## Setup — Before the Demo Starts

1. Every student must have Git installed — run `git --version` to confirm
2. Every student must have their `naija-portfolio/` project folder ready with `index.html`, `style.css`, and `README.md` inside
3. Every student must have their terminal open and navigated to their `naija-portfolio/` folder
4. Instructor runs the full demo once before class to confirm all commands work on their machine

> **Tip:** Tell students at the start — "Every time I type a command, you type it too. We move together. If you fall behind, raise your hand and we will wait."

---

## Demo Steps

### Part 1 — Initialise the Repository (5 min)

**Instructor types:**
```bash
git --version
```

**Expected output:**
```
git version 2.43.0
```

> "Good. Git is installed. Now let's navigate into our project."

**Instructor types:**
```bash
cd naija-portfolio
pwd
```

**Expected output:**
```
/Users/chidi/naija-portfolio
```

> "We are inside the naija-portfolio folder. Right now Git has no idea this project exists. Let's change that."

**Instructor types:**
```bash
git init
```

**Expected output:**
```
Initialized empty Git repository in /Users/chidi/naija-portfolio/.git/
```

> "Git has initialised. It created a hidden `.git/` folder inside our project. That folder is Git's brain — it will store every version of every file from this point forward. You never need to touch it, but it is good to know it is there."

**Ask students:** "Open your file explorer and look inside the naija-portfolio folder. Can anyone see the `.git/` folder?" (They may need to enable hidden files.) "Good. Now close it. We will never need to open it manually."

---

### Part 2 — Check Status (5 min)

**Instructor types:**
```bash
git status
```

**Expected output:**
```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
        index.html
        style.css

nothing added to commit but untracked files present (use "git add" to track)
```

> "Look at what Git is telling us. It sees three files — but it says they are Untracked. That means Git knows they exist but is not doing anything with them yet. They are in our Working Directory but not in the Staging Area."

> "Notice Git is helpful — it even suggests the next command: `git add`. This is why we always run `git status` first. It tells us the current state and usually points to what to do next."

**Ask students:** "Based on what we learned in the slides — what is the next command we should run to move these files into the Staging Area?"

*Students should say `git add`.*

---

### Part 3 — Create .gitignore First (5 min)

> "Before we stage anything, let's create our `.gitignore` file. We always do this before the first commit."

**Instructor opens VS Code and creates `.gitignore` in the project root, types:**
```
# Secrets — never commit these
.env
.env.local

# Dependencies
node_modules/

# Operating system files
.DS_Store
Thumbs.db

# Editor files
.vscode/
```

**Save the file, then in the terminal:**
```bash
git status
```

**Expected output:**
```
On branch main

No commits yet

Untracked files:
        .gitignore
        README.md
        index.html
        style.css
```

> "The `.gitignore` file itself appears as untracked — we do want to commit that. Now let's test it."

**Instructor types:**
```bash
echo "API_KEY=fakesecret123" > .env
git status
```

**Expected output:**
```
Untracked files:
        .gitignore
        README.md
        index.html
        style.css
```

> "See that? The `.env` file exists in our folder but it does NOT appear in `git status`. Git is ignoring it, exactly as we told it to. This is how your API keys stay safe. Even if you run `git add .`, the `.env` file will never be staged."

**Delete the fake .env:**
```bash
rm .env
```

---

### Part 4 — Stage Files (5 min)

> "Now let's stage our files. We will start by staging just one file to see how it works."

**Instructor types:**
```bash
git add index.html
git status
```

**Expected output:**
```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   index.html

Untracked files:
        .gitignore
        README.md
        style.css
```

> "Look at the colour change — index.html is now green under 'Changes to be committed'. It is in the Staging Area. The other files are still red — still untracked. Now let's stage everything."

**Instructor types:**
```bash
git add .
git status
```

**Expected output:**
```
On branch main

No commits yet

Changes to be committed:
        new file:   .gitignore
        new file:   README.md
        new file:   index.html
        new file:   style.css
```

> "Everything is green. All four files are staged and ready to commit. Notice `.env` is not here — our `.gitignore` is working."

---

### Part 5 — Make the First Commit (5 min)

> "Now we save our first snapshot. The commit message tells the story of what this snapshot contains."

**Instructor types:**
```bash
git commit -m "Initial commit — add portfolio homepage"
```

**Expected output:**
```
[main (root-commit) a3f8d21] Initial commit — add portfolio homepage
 4 files changed, 52 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 README.md
 create mode 100644 index.html
 create mode 100644 style.css
```

> "Our first commit is done. Git tells us: 4 files changed, 52 lines added. See that code `a3f8d21`? That is our commit's unique ID — its fingerprint. Every commit ever made in every Git repository that has ever existed has a unique hash like this."

**Instructor types:**
```bash
git status
```

**Expected output:**
```
On branch main
nothing to commit, working tree clean
```

> "This is the cleanest message in Git — 'nothing to commit, working tree clean'. It means every change in your project is saved. Your Working Directory and your Repository are in sync."

---

### Part 6 — Make a Change and Commit Again (7 min)

> "Let's simulate real development. We are going to add a navigation bar to our portfolio."

**Instructor opens `index.html` in VS Code and adds a `<nav>` element:**
```html
<nav>
  <a href="#about">About</a>
  <a href="#skills">Skills</a>
  <a href="#contact">Contact</a>
</nav>
```

**Save the file, then in the terminal:**
```bash
git status
```

**Expected output:**
```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   index.html
```

> "Git noticed the change immediately. index.html is now shown as 'modified' — it has changed since the last commit. It is in our Working Directory but not yet staged."

**Instructor types:**
```bash
git diff
```

**Expected output:**
```
diff --git a/index.html b/index.html
index 4b3e2a1..9f8d7c6 100644
--- a/index.html
+++ b/index.html
@@ -8,6 +8,12 @@
 <body>
+  <nav>
+    <a href="#about">About</a>
+    <a href="#skills">Skills</a>
+    <a href="#contact">Contact</a>
+  </nav>
   <header>
```

> "The plus signs in green show exactly what we added. This is our review before staging — always check `git diff` before committing so you know exactly what you are saving."

**Instructor types:**
```bash
git add index.html
git commit -m "Add navigation bar to homepage"
```

**Expected output:**
```
[main b7e2c19] Add navigation bar to homepage
 1 file changed, 6 insertions(+)
```

---

### Part 7 — View the History (3 min)

**Instructor types:**
```bash
git log --oneline
```

**Expected output:**
```
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "There is our history. Two commits, two snapshots of our project. The most recent is at the top. Each one has its unique hash and its message."

> "This history will grow as you build your portfolio. By the time you finish this module, you will have 10, 20, maybe 50 commits — each one a meaningful step in your project. Employers who look at your GitHub will see this history and know you work like a professional."

**Ask students:** "What would you type if you only wanted to see the last 3 commits?"

*Answer:* `git log --oneline -3`

---

### Part 8 — Fix a Mistake (5 min)

> "Let's say you staged a file by accident. Here is how to unstage it — without losing your changes."

**Instructor stages a file:**
```bash
git add style.css
git status
```

> "Now style.css is staged — but let's say we are not ready to commit it yet."

**Instructor types:**
```bash
git restore --staged style.css
git status
```

**Expected output:**
```
Changes not staged for commit:
        modified:   style.css
```

> "style.css is back in the Working Directory. Our changes are still there — we just removed it from the Staging Area. `git restore --staged` is your undo button for staging."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `git init` run in the wrong folder | "Run `pwd` to see where you are. Navigate to your project folder first with `cd naija-portfolio`." |
| `git status` shows `.env` in untracked files | "Check your `.gitignore` — is `.env` listed? Check for typos. The filename must match exactly." |
| Student committed with a bad message | "Run `git commit --amend -m 'Better message here'` — but only before we push to GitHub." |
| Student deleted the `.git/` folder | "We need to run `git init` again, but your history is gone. This is why we never delete that folder." |
| `git diff` shows nothing | "Your changes may already be staged. Run `git diff --staged` instead." |
| Student on Windows gets line ending warnings | "Safe to ignore for now — this is about how Windows and Mac handle line endings differently." |

---

## Wrap Up

Before ending the demo, ask every student to run:

```bash
git log --oneline
```

They should see at least 2 commits. If anyone has fewer than 2, help them catch up before Topic 10 — branching requires a working repository with at least one commit.

> "You have just completed your first real Git workflow. You initialised a repository, protected your secrets with `.gitignore`, staged and committed files, made changes and committed them, and viewed your history. This is the workflow you will use every single day as a developer. Topic 10 — we add branching. That is where Git gets really powerful."

---

## Up Next

Topic 10 — Branching & Merging. Students will create a feature branch, make changes on it, and merge it back to main — including resolving a merge conflict.
