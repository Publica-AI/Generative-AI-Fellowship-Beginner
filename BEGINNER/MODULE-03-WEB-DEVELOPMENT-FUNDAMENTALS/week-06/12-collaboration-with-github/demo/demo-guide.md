# Demo Guide - Collaboration with GitHub — Pull Requests & Issues
**Module 3, Topic 12 | Estimated duration: 45–50 minutes**

---

## What This Demo Teaches

- How to create a GitHub Issue
- How to fork a repository
- How to clone a fork locally
- How to create a branch, make changes, and push to a fork
- How to open a Pull Request
- How to review and merge a Pull Request
- How `Closes #N` automatically closes an issue on merge

---

## Setup — Before the Demo Starts

**Instructor preparation (do this before class):**
1. Create a shared GitHub repository called `genai-fellowship-demo` under your GitHub account
2. Add a simple `index.html`, `style.css`, and `README.md` to it — same structure as naija-portfolio but simpler
3. Push at least 2 commits so it has a history
4. Do NOT add students as collaborators — they will fork instead
5. Share the repository URL with students

**Student requirements:**
- GitHub account set up and logged in
- naija-portfolio repository from Topics 9–11 with full history
- Terminal open

> **Tell students at the start:** "Today you are going to contribute to my repository — not your own. This is exactly how open source contribution works. You cannot push directly to my repo, so you will fork it, make changes, and ask me to review and merge them via a Pull Request."

---

## Demo Steps

### Part 1 — Create a GitHub Issue (5 min)

> "Before we write a single line of code, we create an Issue. In professional teams, every piece of work starts with an Issue."

**Instructor demonstrates on the shared repository:**
1. Go to the `genai-fellowship-demo` repository
2. Click the **Issues** tab
3. Click **New issue**
4. Title: `Add Nigerian developer profiles to the contributors section`
5. Description:
```
## Feature request

The homepage needs a contributors section that lists Nigerian developers
who have contributed to this project.

## What to add
- A <section id="contributors"> below the main content
- At least one contributor card with name, city, and a short bio
- Basic styling for the card

## Acceptance criteria
- [ ] Section exists in index.html
- [ ] At least one contributor card is visible
- [ ] Section is styled in style.css
```
6. Click **Submit new issue**

> "The issue is now #1. Everyone can see it, it is tracked, and when we eventually merge the fix, GitHub will close it automatically."

**Ask students to read the issue.** "Does this description give a developer enough information to start working? Yes — they know what to build, where to put it, and what done looks like."

---

### Part 2 — Fork the Repository (5 min)

> "You do not have write access to my repository. You cannot push directly to it. So you are going to fork it — create your own copy on GitHub."

**Students go to the shared repository URL in their browser.**

**Instructor demonstrates:**
1. Click the **Fork** button in the top right
2. Leave all settings as default
3. Click **Create fork**

> "GitHub created a copy of my repository under your account. It looks identical but it is yours. The URL changed from `github.com/instructor/genai-fellowship-demo` to `github.com/your-username/genai-fellowship-demo`."

**Students fork the repository.**

---

### Part 3 — Clone the Fork Locally (5 min)

> "Now clone YOUR fork — not the original. You will work on your local copy, push to your fork, and then open a PR from your fork to my original."

**Students copy the clone URL from their fork (not the original).**

**Instructor demonstrates:**
```bash
cd ~/Desktop
git clone https://github.com/your-username/genai-fellowship-demo.git
cd genai-fellowship-demo
git log --oneline
```

**Expected output:**
```
b7e2c19 Add initial homepage structure
a3f8d21 Initial commit
```

> "You have the full history. Now create a feature branch."

```bash
git switch -c feature/add-contributors-section
git branch
```

**Expected output:**
```
* feature/add-contributors-section
  main
```

---

### Part 4 — Make Changes and Commit (8 min)

> "Now we build the feature. Open index.html in VS Code."

**Instructor adds a contributors section to index.html:**
```html
<section id="contributors">
  <h2>Nigerian Contributors</h2>
  <div class="contributor-card">
    <h3>Chidi Okafor</h3>
    <p>Lagos, Nigeria</p>
    <p>Full-stack developer building AI-powered fintech applications.</p>
  </div>
</section>
```

**Instructor adds styling to style.css:**
```css
#contributors {
    padding: 2rem;
    background-color: #f8f9fa;
}

.contributor-card {
    border: 1px solid #dee2e6;
    border-radius: 8px;
    padding: 1rem;
    max-width: 400px;
}

.contributor-card h3 {
    color: #387F7F;
    margin-bottom: 0.25rem;
}
```

**Instructor types:**
```bash
git add index.html style.css
git commit -m "Add contributors section with Nigerian developer card — Closes #1"
git log --oneline
```

**Expected output:**
```
d5e3a21 Add contributors section with Nigerian developer card — Closes #1
b7e2c19 Add initial homepage structure
a3f8d21 Initial commit
```

> "Notice the commit message ends with `Closes #1`. When this commit is merged via a PR, Issue #1 will automatically close. GitHub links everything together."

---

### Part 5 — Push to the Fork (3 min)

**Instructor types:**
```bash
git push origin feature/add-contributors-section
```

**Expected output:**
```
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Writing objects: 100% (4/4), 678 bytes | 678.00 KiB/s, done.
To https://github.com/your-username/genai-fellowship-demo.git
 * [new branch]      feature/add-contributors-section -> feature/add-contributors-section
```

> "We pushed the feature branch to our fork. The branch is now on GitHub but only in our fork — not in the original repository yet. Now we open a Pull Request to propose the merge."

---

### Part 6 — Open a Pull Request (8 min)

**Instructor goes to their fork on GitHub.**

> "GitHub usually shows a yellow banner: 'Your branch has recent pushes — Compare & pull request'. Click that button."

**If the banner does not appear:**
1. Click the **Pull requests** tab
2. Click **New pull request**
3. Base repository: `instructor/genai-fellowship-demo` | base: `main`
4. Head repository: `your-username/genai-fellowship-demo` | compare: `feature/add-contributors-section`

**Fill in the PR form:**

Title:
```
Add contributors section with Nigerian developer profiles
```

Description:
```
## What this PR does
Adds a contributors section to the homepage featuring Nigerian developer profiles.
Each contributor card shows name, city, and a brief bio.

## Changes
- index.html: Added <section id="contributors"> with a contributor card
- style.css: Added card styling with brand colour (#387F7F)

## How to test
1. Open index.html in a browser
2. Scroll to the bottom — you should see the Contributors section
3. Confirm the card shows name, city, and bio

## Related issue
Closes #1
```

5. Click **Create pull request**

> "The PR is now open. It shows the diff — exactly what changed. It is linked to Issue #1. A reviewer can now see everything: what changed, why, and how to test it."

**Show students the PR page — point out:**
- The commits tab showing the commit(s) included
- The Files changed tab showing the diff
- The link to Issue #1 in the description

---

### Part 7 — Review and Merge the PR (8 min)

**Instructor switches to their own GitHub account (the repository owner).**

> "Now I am the reviewer. I am going to read the PR, look at the code, and merge it."

**Instructor clicks Files changed tab — shows the diff.**

> "This is the diff — green lines are additions, red lines are deletions. I can see exactly what changed without reading the whole codebase."

**Instructor leaves an inline comment on a line of index.html:**
1. Hover over a line number → click the `+` button
2. Type: "Good use of semantic HTML. Consider adding an `aria-label` to the section for accessibility."
3. Click "Start a review"
4. Click "Finish your review" → select "Approve" → "Submit review"

> "I approved the PR with a comment. In a real team, the developer would address the feedback, push another commit, and the reviewer would re-approve. For our demo, we will merge now."

**Instructor clicks "Merge pull request" → "Confirm merge".**

**Expected result:**
- PR shows as "Merged" (purple badge)
- Issue #1 shows as "Closed"

> "Look at Issue #1 — it is now automatically closed. GitHub linked the commit message to the issue and closed it when the PR merged. This is the complete professional workflow."

---

### Part 8 — Pull the Merged Changes Locally (3 min)

**Instructor switches back to the terminal:**

```bash
git switch main
git pull
git log --oneline
```

**Expected output:**
```
f2a1b8c Merge pull request #1 from your-username/feature/add-contributors-section
d5e3a21 Add contributors section with Nigerian developer card — Closes #1
b7e2c19 Add initial homepage structure
a3f8d21 Initial commit
```

> "The merge commit is now in main locally. The feature branch's commits are here too. Clean up the local branch."

```bash
git branch -d feature/add-contributors-section
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| Student opens PR against wrong base | "In the PR form, make sure Base repository is the instructor's repo and base branch is main. Head repository is your fork." |
| PR shows 'Can't automatically merge' | "There are conflicts between your branch and main. Pull the latest main into your branch locally, resolve conflicts, and push again." |
| Issue does not close after merge | "Check the commit message — `Closes #1` must match the issue number exactly. Also check the PR was merged to main, not another branch." |
| Student forked the wrong repo | "Delete your fork on GitHub and start again from the correct repository URL." |
| Push to fork rejected | "Make sure you cloned YOUR fork, not the original. Run `git remote -v` — the URL should show your username, not the instructor's." |

---

## Wrap Up

> "You have just completed the full professional GitHub workflow — from Issue to merged PR. This is how Nigerian developers contribute to international open source projects. This is how Andela-placed developers work with their teams. This is how you will work in your career."

Ask every student to confirm:
- Their fork has the contributors section
- Issue #1 is closed on the instructor's repository
- They can see the merge commit in `git log --oneline`

> "Module 3 is complete. You know HTML, CSS, JavaScript, and Git. Your naija-portfolio is on GitHub. You have opened a Pull Request. Module 4 — Backend Development — we connect everything to servers, databases, and APIs. See you there."

---

## Up Next

Module 4 — Backend Development: Consuming External APIs + FastAPI.
