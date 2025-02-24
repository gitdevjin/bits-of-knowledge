## Input Tag

```javascript
const input = document.createElement("input");
    input.setAttribute("type", "file");
    input.setAttribute("accept", "image/*");
    input.click();
```

### Breakdown of What Happens:
### 1. document.createElement("input") → Creates an input element in memory.
### 2. input.setAttribute("type", "file") → Sets it as a file input field.
### 3. input.setAttribute("accept", "image/*") → Restricts the selection to image files only.
### 4. input.click() → Opens the file selection dialog.

## Input Tag with File Handling
```javascript
const file = input.files?.[0];
```
### 1. `input.files`
- `input.files` is a FileList object that contains the selected files.
- If the user selects a single file, input.files contains one file.
- If the user selects multiple files (when using multiple attribute), input.files contains multiple files.

Example:
```js
console.log(input.files); 
// FileList { 0: File, length: 1 }
```

### 2. `?.` (Optional Chaining)
- `?.` is optional chaining, which prevents errors if `input.files` is null or undefined.
- If no file is selected, `input.files` will be null, and `input.files?.[0]` will return undefined instead of throwing an error.

Example:

```js
const file = input.files?.[0];
console.log(file); // If no file is selected, it logs: undefined
```

### 3. `[0]` (Accessing the First File)
- files is like an array (though technically it's a FileList).
- `[0]` retrieves the first file in the list.

Example:

```js
<input type="file" id="upload" />
<script>
  const input = document.getElementById("upload");
  input.addEventListener("change", () => {
    const file = input.files?.[0]; // Get first file if it exists
    console.log(file?.name); // Logs the selected file's name
  });
</script>
```

### Summary
- ✅ `input.files` → Retrieves the list of selected files.
- ✅ `?.` → Prevents errors if no file is selected.
- ✅ `[0]` → Gets the first file from the list.

## how do the files exist and how can they be accessed
When a file is selected using `<input type="file">`, it is stored in memory (RAM), not permanently on disk. You can then create **a temporary URL (Blob URL)** that acts as a pointer to this in-memory file.

### How `URL.createObjectURL(file)` Works
- The browser keeps the file in memory when selected.
- `URL.createObjectURL(file)` creates a temporary URL (a pointer) to that file.
- This Blob URL can be used as an src for `<img>`, `<video>`, or `<audio>`, allowing you to preview the file without uploading it.


