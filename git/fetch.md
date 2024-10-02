## How to use `fetch` in git

### 1. Fetch updates from the remote repository:
First, run a git fetch to retrieve the latest updates from the remote without merging them into your current branch:

```bash
git fetch origin
```

### 2. View the differences between your current branch and the fetched remote branch:
After fetching, you can use git diff to compare your current branch with the fetched remote branch (e.g., `origin/main` or `origin/your-branch`).

```bash
git diff origin/main
```
This will show the changes between your local branch and the remote branch. You can replace `origin/main` with any other remote branch if you're working on a different branch.

### 3. Compare fetched changes with your current branch's commit history:
To see if there are new commits on the remote branch that you don’t have locally or if you have local commits not yet pushed, you can use:

```bash
git log --oneline --graph --decorate --all
```

This command will show a graph of the commit history, highlighting the differences between the commits on your local branch and those fetched from the remote.

### 4. View commit summary differences:
You can also get a brief summary of differences using:
```bash
git status
```
This will tell you if your branch is ahead or behind the remote branch, i.e., if there are commits on the remote that you haven’t merged yet, or if you have commits locally that haven’t been pushed.