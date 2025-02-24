### 1️⃣ What is `default` in `export default`?
- It allows one main export per file.
- When importing, you don’t need to use the same name as the exported function or variable.

### 2️⃣ Example: Named Export vs. Default Export
✅ Named Export (Must Match the Import Name)
```tsx
// file: MyComponent.tsx
export function MyComponent() {
  return <div>Hello</div>;
}

// Importing (must match exactly)
import { MyComponent } from "./MyComponent";
```

✅ Default Export (Can Be Imported with Any Name)
```tsx
// file: MyComponent.tsx
export default function MyComponent() {
  return <div>Hello</div>;
}

// Importing (can use any name)
import Something from "./MyComponent";  // Works!
import MyComponent from "./MyComponent"; // Also works!
```
Here, export default allows flexible naming when importing.

### 3️⃣ Default Export with const
```tsx
const MyComponent = () => {
  return <div>Hello</div>;
};

export default MyComponent;
```
This assigns the function to a variable and then exports it as the default export.

### 4️⃣ Default Export vs. Named Export: When to Use Which?
| Feature                      | Default Export ✅ | Named Export ✅ |
|------------------------------|------------------|----------------|
| Import flexibility           | ✅ Any name      | ❌ Must match  |
| Multiple exports per file    | ❌ No           | ✅ Yes         |
| Better for large projects    | ❌ No           | ✅ Yes         |
| Better for single-component files | ✅ Yes  | ❌ No         |


### 5️⃣ Can You Mix Both?
Yes! A file can have one default export and multiple named exports.

```tsx
export default function MyComponent() {
  return <div>Hello</div>;
}

export const helperFunction = () => {
  return "I am a helper!";
};
```
Then import like:
```tsx
import MyComponent, { helperFunction } from "./MyComponent";
```

### 💡 Summary
- `export default` → Use for one main thing in a file.
- **Named exports** → Use when exporting multiple items from a file.
- You can mix both in a file but should use `default` for the primary export



## What is a Primary Export?
A primary export refers to the main export of a module. In JavaScript and TypeScript, this is typically done using `export default`, which allows a module to have a single, main item that is meant to be the most important thing exported from that file.

### 1️⃣ Example of a Primary Export
Let's say you have a file called Button.tsx, and the main purpose of this file is to provide a button component.

```tsx
// Button.tsx
export default function Button() {
  return <button>Click Me</button>;
}
```
Here, `Button` is the primary export because it's the main thing this file provides.

You can import it like this:
```tsx
import Button from "./Button"; // No need for curly braces
```
This works because there is only one default export in the file.

### 2️⃣ When Would You Use a Named Export Instead?
If a file contains multiple exports, it often makes sense to use named exports instead of a single primary export.

```tsx
// utils.ts
export function formatDate(date: Date) {
  return date.toISOString().split("T")[0];
}

export function capitalize(word: string) {
  return word.charAt(0).toUpperCase() + word.slice(1);
}
```

Here, there is no primary export because the file provides multiple functions.

To import:
```tsx
import { formatDate, capitalize } from "./utils";
```

### 3️⃣ Mixing Primary (`default`) and Named Exports
A file can have one primary export (`export default`) and multiple named exports.

```tsx
// Card.tsx
export default function Card({ children }) {
  return <div className="card">{children}</div>;
}

export function CardHeader({ title }) {
  return <h2>{title}</h2>;
}

export function CardFooter({ footerText }) {
  return <div>{footerText}</div>;
}
```
Now, you can import like:
```tsx
import Card, { CardHeader, CardFooter } from "./Card";

// the name Card can be customized For example:
import MyCustomCard, {CardHeader, CardFooter} from "./Card";
```
Here:
- `Card` is the primary export (imported without `{}`).
- `CardHeader` and `CardFooter` are named exports (imported with `{}`).


💡 Summary
| Feature             | Default Export (Primary Export) | Named Export |
|---------------------|--------------------------------|-------------|
| Number per file    | Only 1                         | Multiple    |
| Import syntax      | `import Anything from "./file";` | `import { Something } from "./file";` |
| Best for           | Single main item in a module   | Multiple utilities or components |


A primary export is the main thing a file is supposed to provide, making it easy to import without worrying about names.