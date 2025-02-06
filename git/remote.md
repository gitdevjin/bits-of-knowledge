# Remote Branch Workflow Scenarioes.

## Work on Others' Repo

### 1. Fork the repository that you want to work on

### 2. Clone the repository
```bash
git clone https://github.com/gitdevjin/addcom.git
```

### 3. Create a new issue branch (do this in the main branch)
```bash
git checkout -b issue-5
```

## When the forked repo is behind the upstream branch

### 1. Add the original repo as remote branch(upstream)
```bash
git remote add <name-it-whatever-you-want> <upstream-repo-url>
# For example:
git remote add upstream https://github.com/arilloid/addcom.git
```

### 2. Fetch the upstream's branches
```bash
git fetch upstream
```

### 3-a Sync your main branch to the upstream's main branch
```bash
git checkout main
git merge upstream/main --ff-only
# or you can just try merging it and solve the conflicts
# It's better to leave the main branch of the forked repo untouched,
# and always work on a new branch.
git merge upstream/main
```

### 3-a Create a new branch to work on for a new feature or a bug
```bash
git checkout -b <new-issue-name>
```

This is a slightly different scenario. This will leave your main branch behind the upstream
### 3-b. Checkout a new branch to work on from the upstream branch (Directly checkout from upstream)
```bash
git checkout -b <new-issue-name> <remote-branch-name>/<branchname>
# For example:
git checkout -b add-more-tests upstream/main
```

### 4. Push the changes into your forked origin repo
```bash
git add <changed-files>
git commit -m "your commit messages"
git push origin <your-issue-branch-name>
```



## Reviewing and Testing Pull Request

### 1. Add the remote forked repo (your repo forked by the person)
```bash
$ git remote add <name-of-student> <https://git-url-of-other-studnet-fork.git>
```

### 2. Fetch the Remote branches
```bash
$ git fetch <name-of-student>
```

### 3. Create a new branch for reviewing the branch where the Pull Request is opened
```bash
$ git checkout -b <branch-name> <name-of-student>/<branch-name>
```

### 4. Checkout to the Iusse branch and pull the latest changes
```bash
$ git checkout issue-6
$ git pull <name-of-student> issue-6
```

### 5. Merge the Pull Request into your main branch
```bash
$ git fetch <name-of-student>
$ git checkout main
$ git merge <student-name>/issue-6
$ git push origin main
```

Merging the work and pushing to the main branch should automatically close the pull request.

### Tip
```bash
git remote -v
```
This will show the remote branches and their url
