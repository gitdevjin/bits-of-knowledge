## Visualize the merging of second branch into main branch

let's imagine the following scenario:

Initial state:
main has two commits: `M1`, `M2`.
second was branched off main at `M2` and has two additional commits: `S1`, `S2`.
Before Merge:
```sql
main:
M1 --- M2
```

```sql
second:
      M2 --- S1 --- S2
```

Fast-Forward Merge:
If main has no new commits after the branch was created, the merge is fast-forwarded. The commits from second (`S1`, `S2`) are simply added to main:

After Fast-Forward Merge:
```sql
main:
M1 --- M2 --- S1 --- S2
```

second and main now point to the same commit, and the history is linear.

Three-Way Merge:
If main has new commits after second branched off, a three-way merge will happen. Let's say main gets a new commit `M3` before merging second.

Before Merge:
```sql
main:
M1 --- M2 --- M3
```
```sql
second:
      M2 --- S1 --- S2
```
Now, when you merge second into main, Git will create a merge commit (let's call it M4) that has two parents: the last commit from main (`M3`) and the last commit from second (`S2`).

After Three-Way Merge:
```lua
main:
M1 --- M2 --- M3 --- M4 (merge)
          \         /
           S1 --- S2
```

The commits from second (`S1`, `S2`) are still part of the history, but M4 is the actual merge point.
The history is no longer linear but forms a "merge graph."
This is what you would typically see when running `git log --graph`. You would see a branching structure, with one line representing main's commits and another showing the commits from second, both joining at the merge commit.


## example logs on main branch after merge?

### 1. Fast-Forward Merge Example
In a fast-forward merge, the commit history on main after merging second will look as though all commits from second were directly part of main's history.

Branch history before merge:

main: M1, M2
second: M2, S1, S2

After fast-forward merge, git log --oneline on main:

```bash
S2 Merge commit from branch 'second'
S1 Commit from second branch
M2 Commit from main before branching
M1 Initial commit on main branch
```

Here, `S1` and `S2` from the second branch now appear in main as if they were committed directly to main. The history is linear because no new commits were made on main after branching.

### 2. Three-Way Merge Example
In a three-way merge, the history is more complex because there are commits on both main and second.

Branch history before merge:

main: M1, M2, M3
second: M2, S1, S2

After a three-way merge, `git log --oneline --graph` on main:
```bash
*   M4 Merge branch 'second' into 'main'
|\  
| * S2 Commit from second branch
| * S1 Commit from second branch
* | M3 Commit on main after branching
|/  
* M2 Commit from main before branching
* M1 Initial commit on main branch
```
`M4` is the merge commit that merges the two branches.
`S1` and `S2` are the commits from second, while `M3` is a commit on main after the branch was created.
The history forms a "Y" shape, reflecting the two branches merging.
In this case, both main and second made changes, so Git creates a merge commit (`M4`) to combine them. This merge commit has two parent commits: `M3` (from main) and `S2` (from second).

You can view this structure in your own repository with:

```bash
git log --oneline --graph
```