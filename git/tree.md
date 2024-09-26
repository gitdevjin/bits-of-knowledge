## Tree structure of Git
Let’s walk through an example of what a tree object would look like in Git

Consider this simple directory structure:

```bash
root/
  ├── file1.txt
  ├── file2.txt
  └── subdir/
      └── file3.txt
```

Git Objects Breakdown:
Blobs: There will be three blob objects representing the content of `file1.txt`, `file2.txt`, and `file3.txt`.
Tree Objects: There will be two tree objects:
One for the root directory (root), which contains `file1.txt`, `file2.txt`, and the subdirectory `subdir/`.
One for `subdir/`, which contains `file3.txt`.

Tree Object Content:
Here’s what the root tree object might look like in human-readable form:

```bash
100644 blob a1b2c3d file1.txt
100644 blob e4f5g6h file2.txt
040000 tree i7j8k9l subdir
```

`100644`: This is the mode for the files `file1.txt` and `file2.txt`. It indicates that these are regular files with standard read/write permissions.
`blob a1b2c3d`: This indicates that `file1.txt` is represented by a blob with the hash `a1b2c3d`, and the same goes for `file2.txt` with the `blob e4f5g6h`.
`040000 tree i7j8k9l`: This is the mode for a directory (in this case, `subdir/`). The hash `i7j8k9l` points to another tree object that represents the contents of subdir/.
Next, the tree object for `subdir/` might look like this:

```bash
100644 blob z1x2w3v file3.txt
```

`100644`: Indicates a regular file with standard permissions.
`blob z1x2w3v`: Points to the blob object for `file3.txt`, with the hash `z1x2w3v`.

Commit Object:
When you create a commit, it references the tree object for the root directory. For example, the commit might look like this:

```bash
tree aabbccddeeff
parent 112233445566
author Your Name <you@example.com> 1609459200 +0000
committer Your Name <you@example.com> 1609459200 +0000
Initial commit
```

`tree aabbccddeeff`: The commit references the hash of the root tree object (aabbccddeeff), which represents the state of the entire project.
`parent 112233445566`: This points to the previous commit (if any).
The author and committer metadata show who made the commit and when it was made.

How This Works Internally:
The root tree contains entries for both files and directories.
Each file is represented by a blob that stores the file content.
Each directory is represented by a tree object that lists its contents (files and other directories).
Git recursively walks through these tree objects to recreate the entire directory structure for any commit.

Git Command to See Tree Object:
You can use the git cat-file command to view the contents of a tree object in your repository:

```bash
git cat-file -p
```
This command will print the tree object in a format similar to the example above. Replace with the actual hash of the tree object you want to inspect.

Summary:
Tree objects represent directories and contain references to blobs (files) and other tree objects (subdirectories).
Each entry in a tree has a mode, a name (filename or directory name), and a hash pointing to the corresponding blob or tree.

## Q. let's say I changed the content of `file3.txt` and made `file4.txt` in the subdir directory, what blobs and tree objects are created?

### ChatGPT Answer
Let's break down what happens in Git when you modify file3.txt and add file4.txt in the subdir/ directory.

Starting Point:
We have this structure before making any changes:

```bash
root/
  ├── file1.txt  (blob a1b2c3d)
  ├── file2.txt  (blob e4f5g6h)
  └── subdir/    (tree i7j8k9l)
      └── file3.txt  (blob z1x2w3v)
```
Changes:
You modify the content of `file3.txt`.
You create a new file `file4.txt` in `subdir/`.
What Happens Internally:
#### Step 1: Git Creates a New Blob for `file3.txt`
Since you modified `file3.txt`, Git will create a new blob for its updated content. Let's assume the new blob's hash is m5n6o7p.
#### Step 2: Git Creates a New Blob for `file4.txt`
Since `file4.txt` is a new file, Git will create a blob for its content. Let's assume the hash of the blob for `file4.txt` is `q9r8s7t`.
#### Step 3: Git Creates a New Tree for `subdir/`
The `subdir/` directory now has a new file (`file4.txt`) and a modified file (`file3.txt`).
Git will create a new tree object for `subdir/`, because the contents of the directory have changed. This new tree will:
Point to the new blob for `file3.txt (m5n6o7p)`.
Point to the blob for `file4.txt (q9r8s7t)`.
Let's assume the new hash for the updated `subdir/` tree object is `u8v9w0x`.

The updated `subdir/` tree object would look like this:

```bash
100644 blob m5n6o7p file3.txt
100644 blob q9r8s7t file4.txt
```

#### Step 4: Git Creates a New Tree for `root/`

The root directory now has an updated subdirectory (`subdir/`), but `file1.txt` and `file2.txt` remain unchanged.
Git will create a new tree object for the `root/` directory, because its `subdir/` entry now points to the new tree object for `subdir/`.
The new root/ tree object will look like this:
``` bash
100644 blob a1b2c3d file1.txt
100644 blob e4f5g6h file2.txt
040000 tree u8v9w0x subdir
```
`file1.txt` and `file2.txt` remain unchanged, so they still point to their original blobs.
`The subdir/` entry now points to the new tree object with hash `u8v9w0x`.
Let’s assume the new hash for the updated `root/` tree object is `abc123`.

#### Step 5: Git Creates a New Commit
Finally, Git creates a new commit object. This commit will point to the new root tree object (abc123), representing the updated state of the repository.

#### Summary of Changes:
New blob for `file3.txt`: `m5n6o7p` (because the content changed).
New blob for `file4.txt`: `q9r8s7t` (because it’s a new file).
New tree object for `subdir/`: `u8v9w0x` (because the directory now contains both `file3.txt` and `file4.txt`).
New tree object for `root/`: `abc123` (because the `subdir/` tree has been updated).
Visualization of the Updated Git Objects:

```bash
root/ (tree abc123)
  ├── file1.txt  (blob a1b2c3d)
  ├── file2.txt  (blob e4f5g6h)
  └── subdir/    (tree u8v9w0x)
      ├── file3.txt  (blob m5n6o7p)
      └── file4.txt  (blob q9r8s7t)
```

In Git, whenever a file is changed or a new file is added, Git creates new blobs and updates the relevant tree objects, but it reuses any objects (blobs or trees) that haven't changed.

## Q. Difference between `git diff` and `git diff --staged`

`git diff --staged` (or `git diff --cached`):

Shows the differences between the files in the staging area (index) and the last commit. This command lets you see what changes are staged for the next commit compared to what is currently in the repository.

`git diff`:
Shows the differences between the files in the working directory and the staging area (index). This command lets you see what changes you have made in your working directory that are not yet staged for the next commit.