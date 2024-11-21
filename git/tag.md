## Q. Waht is the difference between `annotated tag` and `lightweight tag`?

If you create a tag without the -a option, it will be a lightweight tag instead of an annotated tag. Here's what happens when you do this:

What Is a Lightweight Tag?
A lightweight tag is essentially just a pointer to a specific commit. Unlike an annotated tag, it doesn't have additional metadata, such as:

- A tagger's name
- A timestamp
- A tag message
- It is simply stored as a reference to a commit, similar to a branch, but without any history or metadata.

How to Create a Lightweight Tag
To create a lightweight tag:

```bash
git tag v0.9.0
```
This command creates the tag v0.9.0 pointing to the current commit.

### Differences Between Annotated and Lightweight Tags

| Feature     | Annotated Tag                           | Lightweight Tag                       |
|-------------|-----------------------------------------|---------------------------------------|
| **Metadata** | Includes metadata (tagger, date, message) | No metadata                           |
| **Purpose**  | Ideal for releases and public tags      | Useful for temporary or internal purposes |
| **Command**  | `git tag -a v0.9.0 -m "Message"`        | `git tag v0.9.0`                      |
| **Storage**  | Stored as a Git object in the repository | Stored as a simple reference          |
| **Pushing**  | `git push --follow-tags` pushes annotated tags | Must explicitly push lightweight tags using `git push origin v0.9.0` |

To push a lightweight tag:

```bash
git push origin v0.9.0
```
How to Convert a Lightweight Tag to an Annotated Tag?
If you accidentally create a lightweight tag but want an annotated one instead:

Delete the lightweight tag locally and remotely:

```bash
git tag -d v0.9.0
git push origin :refs/tags/v0.9.0
```
Create an annotated tag with the same name:

```bash
git tag -a v0.9.0 -m "Release version 0.9.0"
git push --follow-tags # push the commit with the tag.
```

### Summary
- Lightweight tags are simple and minimal but lack metadata. Use them for temporary or private purposes.
- Annotated tags are the standard for releases because they include important details and are more descriptive.