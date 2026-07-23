# Demo Guide - Branching & Merging
**Module 3, Topic 10 | Estimated duration: 40–45 minutes**

---

## What This Demo Teaches

- How to create a branch with `git branch`
- How to switch between branches with `git switch`
- How to make commits on a feature branch
- How branches stay isolated from main
- How to merge a branch into main
- How to delete a branch after merging
- How merge conflicts happen and how to resolve them

---

## Setup — Before the Demo Starts

1. Every student must have the `naija-portfolio/` repository from Topic 9 with at least 2 commits
2. Every student must be on the `main` branch — confirm with `git branch`
3. Every student must have VS Code open with the naija-portfolio project
4. Instructor runs the full demo once before class to confirm all commands work

> **Before starting ask:** "Everyone run `git log --oneline`. Can you see at least 2 commits? Good. Now run `git branch` — you should only see `* main`. If you see other branches from earlier practice, run `git branch -d branchname` to clean them up."

---

## Demo Steps

### Part 1 — Create a Feature Branch (5 min)

> "Right now we have a working portfolio on main. Our client wants us to add an about section. We are NOT going to touch main directly. We are going to create a feature branch."

**Instructor types:**
```bash
git branch
```

**Expected output:**
```
* main
```

**Instructor types:**
```bash
git branch feature/add-about-section
git branch
```

**Expected output:**
```
  feature/add-about-section
* main
```

> "We have two branches now. But notice the asterisk is still on main — we created the branch but we have not moved to it yet. Let's switch."

**Instructor types:**
```bash
git switch feature/add-about-section
git branch
```

**Expected output:**
```
* feature/add-about-section
  main
```

> "The asterisk moved. We are now on the feature branch. Any changes we make from this point will stay on this branch and will NOT affect main."

---

### Part 2 — Work on the Feature Branch (8 min)

> "Let's add an about section to our portfolio. Open index.html in VS Code."

**Instructor opens index.html and adds an about section after the nav:**
```html
<section id="about">
  <h2>About Me</h2>
  <p>I am a Nigerian developer learning to build AI-powered web applications.
  Based in Lagos, passionate about fintech and education technology.</p>
</section>
```

**Save the file, then in the terminal:**
```bash
git status
```

**Expected output:**
```
On branch feature/add-about-section
Changes not staged for commit:
        modified:   index.html
```

> "Git sees the change — and it confirms we are on the feature branch. Let's stage and commit."

**Instructor types:**
```bash
git add index.html
git commit -m "Add about section to portfolio homepage"
git log --oneline
```

**Expected output:**
```
d5e3a21 Add about section to portfolio homepage
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "Our feature branch now has 3 commits — 2 from when we were on main, plus the new one we just made."

---

### Part 3 — Prove the Isolation (5 min)

> "This is the magic moment. Let's switch back to main and see what happens."

**Instructor types:**
```bash
git switch main
```

> "Now open index.html in VS Code."

**Instructor opens index.html — the about section is gone.**

> "Where did our about section go? Is it lost?"

*Let students answer — they should say "It is on the feature branch".*

> "Exactly. It is not lost — it is on the feature branch. main does not know it exists yet. Let's check the log."

**Instructor types:**
```bash
git log --oneline
```

**Expected output:**
```
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "main only has 2 commits. The feature commit is invisible from here. Switch back to the feature branch."

**Instructor types:**
```bash
git switch feature/add-about-section
```

> "Open index.html again — the about section is back. That is branch isolation. That is why professional teams use branches — your work-in-progress never touches the production codebase."

---

### Part 4 — Merge the Feature Branch (5 min)

> "Our about section is complete and we are happy with it. Time to bring it into main."

**Instructor types:**
```bash
git switch main
```

> "Rule number one: always switch to main FIRST before merging. You are pulling the feature work into main — so you need to be standing in main."

**Instructor types:**
```bash
git merge feature/add-about-section
```

**Expected output:**
```
Updating b7e2c19..d5e3a21
Fast-forward
 index.html | 6 ++++++
 1 file changed, 6 insertions(+)
```

> "Merged. Let's verify."

**Instructor types:**
```bash
git log --oneline
```

**Expected output:**
```
d5e3a21 Add about section to portfolio homepage
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "main now has all 3 commits. Open index.html — the about section is there. Clean up the branch since it has done its job."

**Instructor types:**
```bash
git branch -d feature/add-about-section
git branch
```

**Expected output:**
```
* main
```

> "The branch is deleted. The commits are preserved in main. The branch label is just gone."

---

### Part 5 — Create a Merge Conflict (12 min)

> "Now the part everyone is nervous about. We are going to deliberately create a merge conflict — and then resolve it. After this, conflicts will never scare you again."

**Step 1 — Make a change on main:**

**Instructor opens style.css and changes the body background colour:**
```css
body {
    background-color: #ffffff;
    font-family: Arial, sans-serif;
}
```

**Instructor types:**
```bash
git add style.css
git commit -m "Set body background to white on main"
```

**Step 2 — Create a branch and make a conflicting change:**

**Instructor types:**
```bash
git switch -c feature/dark-theme
```

**Instructor opens style.css and changes the SAME background-color line:**
```css
body {
    background-color: #1a1a2e;
    font-family: Arial, sans-serif;
}
```

**Instructor types:**
```bash
git add style.css
git commit -m "Change background to dark theme colour"
```

**Step 3 — Attempt the merge:**

**Instructor types:**
```bash
git switch main
git merge feature/dark-theme
```

**Expected output:**
```
Auto-merging style.css
CONFLICT (content): Merge conflict in style.css
Automatic merge failed; fix conflicts and then commit the result.
```

> "There it is. A merge conflict. Git is telling us it cannot decide — two branches changed the same line of style.css. Let's open the file in VS Code and see what it looks like."

**Instructor opens style.css — VS Code shows the conflict:**
```css
body {
<<<<<<< HEAD
    background-color: #ffffff;
=======
    background-color: #1a1a2e;
>>>>>>> feature/dark-theme
    font-family: Arial, sans-serif;
}
```

> "Let's read this together. Between `<<<<<<< HEAD` and `=======` is what main currently has — white background. Between `=======` and `>>>>>>> feature/dark-theme` is what the feature branch had — dark background. Git put both versions in the file and is asking us: which one do you want?"

> "For this demo we will keep the dark theme. Here is how to resolve it manually — without clicking any buttons — so you understand what is happening."

**Instructor edits the file to remove all conflict markers and keep the dark colour:**
```css
body {
    background-color: #1a1a2e;
    font-family: Arial, sans-serif;
}
```

> "All conflict markers are gone. The file is clean. Now we stage and commit the resolution."

**Instructor types:**
```bash
git add style.css
git commit -m "Resolve merge conflict — keep dark theme background"
git log --oneline
```

**Expected output:**
```
f2a1b8c Resolve merge conflict — keep dark theme background
e4c9d3a Change background to dark theme colour
c7b1a2e Set body background to white on main
d5e3a21 Add about section to portfolio homepage
b7e2c19 Add navigation bar to homepage
a3f8d21 Initial commit — add portfolio homepage
```

> "Conflict resolved. That is it. That is the whole thing. It was just editing a text file and removing some markers. Never be afraid of a merge conflict again."

**Instructor types:**
```bash
git branch -d feature/dark-theme
```

---

## Common Student Issues to Watch For

| Issue | What to say |
|-------|-------------|
| Student commits to main instead of feature branch | "Run `git log --oneline` on both branches to find the commit. We will cover `git revert` later — for now, just re-create the feature from the correct branch." |
| Merge says "Already up to date" | "You are trying to merge a branch that has no new commits compared to main. Check you are on main and the feature branch actually has new commits." |
| Student cannot find the conflict markers in VS Code | "Make sure you opened the correct file — the one mentioned in the conflict error message. VS Code highlights conflicts in a different colour." |
| Student deleted a branch before merging | "Run `git branch -D branchname` (capital D) to force delete. But check `git log` first — if the commits were not merged, they may be lost." |
| Conflict markers still in the file after 'resolving' | "Did you delete ALL three lines — `<<<<<<<`, `=======`, AND `>>>>>>>`? All three must be gone before staging." |
| Student is stuck mid-merge and wants to abort | "Run `git merge --abort` — this cancels the merge and returns everything to the state before you started." |

---

## Wrap Up

Ask every student to run:
```bash
git log --oneline
git branch
```

They should see: at least 4–5 commits in the log, and only `* main` in the branch list.

> "You have now used Git the way professional developers use it every day. You created branches, isolated your work, merged it back, and resolved a conflict. Topic 11 takes this online — we push this project to GitHub and make it accessible to the world."

---

## Up Next

Topic 11 — GitHub: Remote Repositories. Students will create a GitHub account, push the naija-portfolio project to GitHub, and learn to pull and clone.
