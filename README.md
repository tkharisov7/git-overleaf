# git-overleaf
**tldr: u hate overleaf, but have to collab with colleagues using it**

generated by gemini 2.5 pro

tested and edited by myself

# Git + Overleaf Synchronization Guide

This repository uses a special Git workflow to keep a specific subfolder (`latex/`) synchronized with a shared Overleaf project. This allows developers to work in a local environment with their preferred tools (VS Code, etc.) while collaborating with a team that uses the Overleaf web interface.

The master copy of the entire project (code, data, manuscript) lives on GitHub. The `latex/` subfolder is treated as a "subtree" that syncs with the Overleaf remote.

## Table of Contents
1.  [Prerequisites](#prerequisites)
2.  [One-Time Setup](#one-time-setup)
3.  [Daily Workflow](#daily-workflow)
4.  [Workflow Cheat Sheet](#workflow-cheat-sheet)

---

## Prerequisites

1.  **Git:** You must have Git installed on your local machine.
2.  **Overleaf Premium Account:** The Git integration is a premium feature on Overleaf. Your institution may provide a license.
3.  **SSH Key (Recommended):** To avoid typing your Overleaf password for every push/pull, it is highly recommended to [add your SSH key to your Overleaf account](https://www.overleaf.com/learn/how-to/Using_Git_and_ssh_keys_with_Overleaf).

---

## One-Time Setup

You only need to perform these steps once when you clone the repository to a new machine.

### 1. Clone the GitHub Repository
Clone this repository to your local machine.

```bash
# Replace with the actual repository URL
git clone git@github.com:your-username/your-repo.git
```

Then you will be prompted to enter the password. You can generate it in the Ovelreaf/Account Settings and paste here. You have to do it only once when cloning the repo.

```bash
# Go to the main git repo
cd git-repo-name
```

### 2. Add the Overleaf Remote
You need to get the unique Git URL for the Overleaf project.

-   In Overleaf, open the project and click the **Menu** button (top-left).
-   In the "Sync" section, click **Git**.
-   Copy the Git URL. Use the SSH version (`git@...`) if you have set up an SSH key, otherwise use the HTTPS version (`https://...`).

Now, add this URL as a new remote named `overleaf`:

```bash
# Replace the URL with the one you copied from Overleaf
git remote add overleaf git@git.overleaf.com:YOUR_PROJECT_ID.git
```

Verify that both `origin` (GitHub) and `overleaf` remotes are configured:

```bash
git remote -v
# You should see something like this:
# origin     git@github.com:your-username/your-repo.git (fetch)
# origin     git@github.com:your-username/your-repo.git (push)
# overleaf   git@git.overleaf.com:YOUR_PROJECT_ID.git (fetch)
# overleaf   git@git.overleaf.com:YOUR_PROJECT_ID.git (push)
```

### 3. Link the `latex/` Subfolder using `git subtree`
This command establishes the link between the local `latex/` folder and the Overleaf project's `main` branch. It will pull the entire Overleaf history and place it into the `latex/` directory.

```bash
# This command adds Overleaf's 'master' branch content into our 'latex/' folder.
# --squash is crucial for keeping our main repository history clean.
git subtree add --prefix=latex overleaf master --squash
```

The initial setup is now complete! Your `latex/` folder is populated with the project content from Overleaf.

---

## Daily Workflow

This is the standard cycle for working on the project.

### Step A: Pull Latest Changes
Before you start working, always pull the latest changes from both GitHub and Overleaf to ensure you are up to date.

```bash
# 1. Get updates from the main GitHub repository (code, etc.)
git pull origin main

# 2. Get updates from the Overleaf project into the latex/ folder
git subtree pull --prefix=latex overleaf master --squash
```
*Git will automatically handle merging the changes. If there are conflicts, it will prompt you to resolve them locally, which is a key safety feature of this workflow.*

### Step B: Do Your Work
Work as you normally would. Edit files in any folder (`code/`, `latex/`, etc.) and commit your changes locally using standard Git commands.

```bash
# Stage your changes
git add .

# Commit your changes with a descriptive message
git commit -m "Wrote introduction and updated figure generation script"
```

### Step C: Push Your Work
When you are ready to share your progress, push your changes to both remotes.

```bash
# 1. Push all changes (code, latex, etc.) to the main GitHub repository
git push origin main

# 2. Push ONLY the changes from the latex/ folder to Overleaf
git subtree push --prefix=latex overleaf master
```
Your colleagues on Overleaf will now see your manuscript updates.

---

## Workflow Cheat Sheet

Here are the essential commands for your day-to-day work after the one-time setup is complete.

### **Start of Day (or before editing):**
```bash
# Sync from Overleaf
git subtree pull --prefix=latex overleaf master --squash
```

### **End of Day (or after committing changes):**
```bash
# Push to GitHub
git push origin main

# Push to Overleaf
git subtree push --prefix=latex overleaf master
```
