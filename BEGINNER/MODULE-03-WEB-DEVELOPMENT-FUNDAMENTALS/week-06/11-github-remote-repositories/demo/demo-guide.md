# Demo Guide - GitHub — Remote Repositories
**Module 3, Topic 11 | Estimated duration: 40–45 minutes**

---

## What This Demo Teaches

- How to create a GitHub account and repository
- How to link a local repository to GitHub with `git remote add origin`
- How to push commits to GitHub with `git push`
- How to pull changes from GitHub with `git pull`
- How to clone a repository with `git clone`
- How to create and use a Personal Access Token

---

## Setup — Before the Demo Starts

1. Every student must have the `naija-portfolio/` repository from Topics 9 and 10 with at least 4 commits
2. Every student must have a GitHub account — if not, create one now at github.com (2 minutes)
3. Every student must be on the `main` branch with a clean working tree — confirm with `git status`
4. Instructor must have their own GitHub account ready for the demo
5. Have the GitHub token creation page bookmarked: github.com → Settings → Developer Settings → Personal Access Tokens

> **Check before starting:** "Everyone run `git log --oneline` — you should see at least 4 commits. Everyone run `git status` — you should see 'nothing to commit, working tree clean'. Everyone go to github.com and confirm you are logged in."

---

## Demo Steps

### Part 1 — Create a GitHub Repository (5 min)

> "First we create an empty repository on GitHub. Empty — no files, no README, nothing. We are going to fill it from our local project."

**Instructor demonstrates on browser:**
1. Click the `+` icon in the top right → "New repository"
2. Repository name: `naija-portfolio`
3. Description: "My developer portfolio — built during the GenAI Fellowship"
4. Visibility: **Public**
5. **Leave all three checkboxes unticked** — no README, no .gitignore, no licence
6. Click "Create repository"

> "Critical: do NOT tick any of those boxes. We already have a README and .gitignore in our local project. If GitHub creates them too, we will get a conflict."

**GitHub now shows the repository setup page — point out the section "…or push an existing repository from the command line".**

> "This is the section we need. GitHub is telling us exactly what to run. Let's do it."

**Students create their own repository following the same steps.**

---

### Part 2 — Set Up Authentication (8 min)

> "Before we can push, we need to set up authentication. GitHub stopped accepting regular passwords in 2021. We need a Personal Access Token."

**Instructor demonstrates:**
1. GitHub → click profile picture top right → Settings
2. Scroll to the very bottom → Developer settings
3. Personal access tokens → Tokens (classic)
4. Generate new token (classic)
5. Note: "naija-portfolio push access"
6. Expiration: 90 days
7. Scopes: check `repo` (the first checkbox — gives full repository access)
8. Click "Generate token"

> "Now copy this token immediately. This is the only time GitHub will ever show it to you. If you close this page without copying, you will have to delete it and create a new one."

**Students create their own token and copy it somewhere safe.**

**Set up credential storage so Git remembers the token:**

**Instructor types:**
```bash
git config --global credential.helper store
```

> "This tells Git to save your credentials after you enter them once. You will not be asked again on this machine."

---

### Part 3 — Connect Local to GitHub (5 min)

**Copy the remote add command from the GitHub setup page.**

**Instructor types (replacing with their own username):**
```bash
git remote add origin https://github.com/your-username/naija-portfolio.git
```

**Instructor types:**
```bash
git remote -v
```

**Expected output:**
```
origin  https://github.com/your-username/naija-portfolio.git (fetch)
origin  https://github.com/your-username/naija-portfolio.git (push)
```

> "Our local repository now knows about GitHub. `origin` is just the nickname we gave to our GitHub URL — it is the standard convention. Every developer uses `origin` for their primary remote."

> "Notice it shows both fetch and push — fetch is for pulling, push is for pushing. They use the same URL but the operations are different."

---

### Part 4 — First Push to GitHub (5 min)

**Instructor types:**
```bash
git push -u origin main
```

**Git asks for username — enter your GitHub username.**
**Git asks for password — paste the Personal Access Token (NOT your GitHub password).**

**Expected output:**
```
Enumerating objects: 15, done.
Counting objects: 100% (15/15), done.
Delta compression using up to 8 threads
Compressing objects: 100% (12/12), done.
Writing objects: 100% (15/15), 2.14 KiB | 2.14 MiB/s, done.
Total 15 (delta 2), reused 0 (delta 0), pack-reused 0
To https://github.com/your-username/naija-portfolio.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

> "It worked. Now go to your GitHub repository in the browser and refresh."

**Instructor opens the repository in the browser — files are visible.**

> "Your code is on the internet. Every file, every commit, every message you wrote — all visible here. Click the commit count — you can see your entire history."

> "The `-u` flag we used means 'set upstream' — it links our local main to the GitHub main. From now on, you just type `git push` — no need for `-u origin main` every time."

**Ask students to open their own repository in the browser to confirm their push worked.**

---

### Part 5 — Make a Change and Push Again (5 min)

> "Let's simulate normal development. We make a change locally and push it to GitHub."

**Instructor opens README.md in VS Code and improves it:**
```markdown
# Naija Portfolio

My personal developer portfolio built during the Generative AI Fellowship.

## What's inside
- Responsive HTML/CSS layout
- JavaScript interactivity
- Navigation, About, and Skills sections

## How to run
1. Clone: git clone https://github.com/your-username/naija-portfolio.git
2. Open index.html in your browser

## Technologies
HTML5 · CSS3 · JavaScript · Git · GitHub

## About me
Developer based in Lagos, Nigeria. Building AI-powered web applications.
```

**Instructor types:**
```bash
git add README.md
git commit -m "Improve README with project details and setup instructions"
git push
```

**Expected output:**
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (5/5), 567 bytes | 567.00 KiB/s, done.
To https://github.com/your-username/naija-portfolio.git
   d5e3a21..f7c2b14  main -> main
```

> "Notice this time we just typed `git push` — no flags needed. The `-u` we used on the first push set up the tracking connection. GitHub shows the new commit immediately. Refresh the browser — the README is updated."

**Students update their own README and push.**

---

### Part 6 — Pull Changes From GitHub (5 min)

> "Now let's simulate pulling updates. We are going to make a change directly on GitHub — as if a teammate had pushed something — and then pull it down locally."

**Instructor goes to GitHub, opens README.md, clicks the pencil/edit icon:**
- Add one line: `## Status: In Progress`
- Scroll down and click "Commit changes directly to the main branch"
- Click "Commit changes"

> "I just made a commit directly on GitHub. Now my local machine is behind — it does not have this change yet. Let's pull it."

**Instructor types:**
```bash
git pull
```

**Expected output:**
```
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
Unpacking objects: 100% (3/3), done.
From https://github.com/your-username/naija-portfolio
   f7c2b14..a9d3e21  main -> origin/main
Updating f7c2b14..a9d3e21
Fast-forward
 README.md | 2 ++
 1 file changed, 2 insertions(+)
```

> "Git pulled the change from GitHub and applied it to our local file. Open README.md — the new line is there. This is exactly what happens when a teammate pushes code — you pull to receive their work."

---

### Part 7 — Clone a Repository (5 min)

> "The last command: `git clone`. This is how you get a project onto a new machine or contribute to someone else's project."

**Instructor opens a new terminal, navigates to a different location (e.g. Desktop):**

```bash
cd ~/Desktop
git clone https://github.com/your-username/naija-portfolio.git naija-portfolio-clone
cd naija-portfolio-clone
git log --oneline
```

**Expected output:**
```
a9d3e21 Improve README — add status section
f7c2b14 Improve README with project details
d5e3a21 Add about section to portfolio homepage
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "A complete copy of the project — with the full commit history — downloaded to a new folder in seconds. This is how a new team member sets up the project on their machine. This is how you start contributing to an open source project. Clone once, then pull regularly."

> "Notice we named the folder `naija-portfolio-clone` to avoid confusion with the original. In a real scenario, the default folder name (the same as the repo name) is fine."

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| `git push` asks for password repeatedly | "Run `git config --global credential.helper store` — this saves your token so Git remembers it." |
| Student uses their GitHub password instead of the token | "GitHub no longer accepts passwords. Go back to Settings → Developer Settings and generate a Personal Access Token." |
| `git push` gives "remote origin already exists" error | "You already added a remote. Run `git remote -v` to check the URL. If it is wrong, run `git remote set-url origin <correct-url>`." |
| Push rejected — "Updates were rejected because the remote contains work you do not have" | "Someone (or you on GitHub) has commits you do not have locally. Run `git pull` first, then `git push`." |
| Student accidentally initialised GitHub repo with README | "Delete the GitHub repository and create a new empty one. Then run `git remote add origin` again with the new URL." |
| Token expired or lost | "Go to GitHub → Settings → Developer Settings → Personal Access Tokens. Delete the old one and generate a new one." |

---

## Wrap Up

Ask every student to:
1. Open their GitHub repository in the browser and confirm all files are visible
2. Click the commit count and confirm the full history is showing
3. Share their GitHub repository URL in the class chat

> "Look at what you have built. A version-controlled portfolio project, live on GitHub, with a complete commit history showing your professional workflow. This is what Nigerian developers use to get remote jobs. This is what international companies look at. From today, every project you build should go on GitHub. Topic 12 — we cover Pull Requests and Issues — the final piece of professional team collaboration on GitHub."

---

## Up Next

Topic 12 — Collaboration with GitHub: Pull Requests & Issues. Students will fork a repository, make changes, and open a Pull Request.
