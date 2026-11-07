# Git Workflow — Push This Repo to GitHub

Read this once. You won't need to repeat these steps for later batches — just `git add`,
`commit`, `push` each time you get new files.

## Step 0: Delete the old repo (since you're starting fresh)

1. Go to your old repo on GitHub.
2. Click **Settings** (top right of the repo page).
3. Scroll all the way to the bottom → **Danger Zone** → **Delete this repository**.
4. GitHub will ask you to type the repo name to confirm. Type it exactly, then confirm.

## Step 1: Create the new empty repo

1. github.com → **+** (top right) → **New repository**
2. Name: `cybersecurity-portfolio`
3. Visibility: **Public**
4. Leave every checkbox unticked — don't add a README, don't add a .gitignore
5. Click **Create repository**

## Step 2: Install Git (skip if you already did this)

- Windows: https://git-scm.com/download/win — run the installer, defaults are fine
- Confirm it worked: open a terminal and run `git --version`

## Step 3: Configure git (skip if you already did this)

```bash
git config --global user.name "Saleigh"
git config --global user.email "your-email@example.com"
```

## Step 4: Unzip and push

1. Unzip the folder I gave you.
2. Open a terminal, type `cd ` (with a space), then drag the `cybersecurity-portfolio`
   folder into the terminal window to auto-fill the path, then press Enter.
3. Run:

```bash
git init
git add .
git commit -m "Initial portfolio scaffold: 31 projects, Project 00 home lab built"
git branch -M main
git remote add origin https://github.com/saleigh/cybersecurity-portfolio.git
git push -u origin main
```

4. When it asks for a password, use a **Personal Access Token** instead (not your real
   password): github.com → profile picture → Settings → Developer settings → Personal
   access tokens → Tokens (classic) → Generate new token (classic) → tick **repo** →
   Generate → copy the code it shows you → paste that as the password.

## Step 5: Check it worked

Go to `github.com/saleigh/cybersecurity-portfolio` and refresh. You should see all 31
project folders.

## Every batch after this

```bash
git add .
git commit -m "Add Project NN: <project name>"
git push
```

That's the whole loop — no need to recreate the repo or reconfigure anything again.
