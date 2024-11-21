### Createting a `patch` file.
Patch file is basically a file that shows difference between your currnt commit and one selected commit.
```bash
git diff <commit-to-compare-with> > hello.patch
```

If you just use the following, then it will just compare HEAD with previous commit
```bash
git diff > mypatch.patch
```

### Hot to apply patch file
``` bash
git apply hello.patch
```

### You can use `format-patch`

```bash
git format-patch dcc544f > hello-format.patch
```

For example, you have the following commits

```sql
c7  #<-- current
c6
c5
c4
```

```bash
git format-patch c4 > new-version.patch
```
This will create a patch file set, you can apply to a project which is sitting on `c4` commit.
The patch file set contains multiple `.patch` files, one is meta file for information of other commits and its order that will be applied. The others are actually `diff` result of each commits.


#### how to apply
```bash
git am < 0001-git-remote-workflow-secnarios-added.patch
```
You must not apply the meta file such as `hello-format.patch`





