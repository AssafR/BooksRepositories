# 📚 Book Tracker – Git Submodules Setup

This repository serves as a **private meta-repository** for tracking multiple public Git repositories (for example, open-source books or PDFs).  
Each linked repository is included as a **Git submodule**, allowing you to update all of them in one place without duplicating large files.

---

## 🚀 Why Submodules?

- Keeps this repo small — submodules are **links**, not full copies.  
- You can **update all** tracked repositories with a single command.  
- You can **add or remove** books easily.  
- Each submodule can stay on a specific branch or tag.

---

## 🧩 Setup Instructions

### 1. Create the parent repository

Create a **private** empty repo on GitHub and clone it locally:
```bash
git clone https://github.com/<your-user>/<books-tracker>.git
cd books-tracker
```

### 2. Add public repositories as submodules

You can organize them under a `repos/` subfolder (recommended):

```bash
git submodule add --branch main --depth 1 https://github.com/author/book1.git repos/book1
git submodule add --branch master --depth 1 https://github.com/author2/book2.git repos/book2
git commit -m "Add book submodules"
git push
```

> **Tip:** You can mark submodules as shallow (only the latest commit):
> ```bash
> git config -f .gitmodules submodule.repos/book1.shallow true
> git config -f .gitmodules submodule.repos/book2.shallow true
> git add .gitmodules
> git commit -m "Make submodules shallow"
> ```

---

## 🔁 Updating All Books

From the root of this repository, run:

```bash
git pull
git submodule update --init --recursive --remote --depth 1
git add .
git commit -m "Update books"
git push
```

- `--remote` updates each submodule to the latest commit on its tracked branch.  
- `--depth 1` keeps the clones lightweight.  
- Repos with local edits stay pinned until manually updated.

---

## 🧠 Cloning to a New Machine

If you’re setting up this tracker on a new system:

```bash
git clone --recurse-submodules https://github.com/<your-user>/<books-tracker>.git
```

If you forgot `--recurse-submodules`:
```bash
git submodule update --init --recursive
```

---

## ➕ Bulk Add Helper (Optional)

Keep a file named `repos.txt` in the root, formatted like this:
```
https://github.com/author/book1.git repos/book1 main
https://github.com/author2/book2.git repos/book2 master
```

Then add them all at once (PowerShell):

```powershell
Get-Content .\repos.txt | ForEach-Object {
  $parts = $_ -split '\s+'
  $url=$parts[0]; $path=$parts[1]; $branch=$parts[2]
  git submodule add --branch $branch --depth 1 $url $path
}
git commit -m "Add submodules from list"
git push
```

---

## ⚙️ One-Click Updater (Windows PowerShell)

Save the following as  
`C:\Scripts\Update-BookSubmodules.ps1`:

```powershell
param([string]$RepoPath="C:\PATH\TO\books-tracker")

Set-Location $RepoPath
git pull
git submodule update --init --recursive --remote --depth 1
git add .
if ((git status --porcelain) -ne $null) {
  git commit -m "Auto-update books $(Get-Date -Format s)"
  git push
}
```

### Schedule Automatic Updates (Daily at 09:00)

```cmd
schtasks /Create /SC DAILY /ST 09:00 /TN "UpdateBookSubmodules" ^
 /TR "powershell -NoProfile -ExecutionPolicy Bypass -File C:\Scripts\Update-BookSubmodules.ps1" ^
 /F
```

---

## 🛠 Common Tasks

### Pin a specific book to a release
```bash
cd repos/book1
git fetch --tags
git checkout v1.2.3
cd ../..
git add repos/book1
git commit -m "Pin book1 to v1.2.3"
```

### Handle large files (Git LFS)
If any repo uses [Git LFS](https://git-lfs.com/):
```bash
git lfs install
git submodule foreach --recursive "git lfs pull"
```

### Remove a submodule
```bash
git submodule deinit -f repos/book1
git rm -f repos/book1
rm -rf .git/modules/repos/book1
git commit -m "Remove book1"
```

### Speed up updates
```bash
git submodule update --init --remote --jobs 8 --depth 1
```

---

## 🧾 Summary

| Action | Command |
|--------|----------|
| Add new book repo | `git submodule add --branch main <url> repos/<name>` |
| Update all books | `git submodule update --init --recursive --remote --depth 1` |
| Clone tracker + submodules | `git clone --recurse-submodules <tracker-url>` |
| Auto-update (PowerShell) | `Update-BookSubmodules.ps1` |
| Remove a book | `git submodule deinit -f ... && git rm -f ...` |

---

**Enjoy your organized book tracker!**  
Easily follow and update multiple GitHub repositories — all from one private place.
