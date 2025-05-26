## 🧠 What is `useRef`?
`useRef` is a React Hook that gives you a way to store a mutable reference to:

#### 1. A DOM element → similar to `document.getElementById(...)`

#### 2. A mutable value that doesn't trigger re-renders when it changes

It's part of React’s way to manage imperative operations and persistent values.

### ✅ Syntax
```tsx
const ref = useRef(initialValue);
```
- `ref.current` holds the actual value.

- Updating `ref.current` does NOT cause a re-render.

- Unlike `useState`, `useRef` values persist across re-renders.

### 1. Accessing DOM elements
This is the most common use case — like getting a direct reference to an HTML element.

#### Example: focusing an input field
```tsx
import { useRef, useEffect } from "react";

function FocusInput() {
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    inputRef.current?.focus(); // focus the input on mount
  }, []);

  return <input ref={inputRef} placeholder="Focus me on load" />;
}
```

### 2. Storing mutable values across renders
Sometimes you want to store a value that persists but should not cause re-renders (like a timer ID, previous value, etc.).

#### Example: storing previous props
```tsx
import { useRef, useEffect } from "react";

function CountLogger({ count }: { count: number }) {
  const prevCount = useRef<number>(count);

  useEffect(() => {
    console.log("Previous count:", prevCount.current);
    prevCount.current = count;
  }, [count]);

  return <div>Current count: {count}</div>;
}
```

### 3. Avoiding re-renders when tracking state-like info

#### Example: count how many times the component renders
```tsx
import { useRef } from "react";

function RenderCounter() {
  const renderCount = useRef(1);
  renderCount.current += 1;

  return <div>I have rendered {renderCount.current} times</div>;
}
```

### 🔥 Summary of `useRef`
| Use Case                          | Why use useRef?                                              |
|-----------------------------------|--------------------------------------------------------------|
| Accessing a DOM element           | Get direct access to elements (like `inputRef.current.focus()`) |
| Persisting a value without rerender | Like a timer, previous prop, render counter, etc.           |
| Bypassing reactivity              | Avoids triggering re-renders on change

### 🧪 Final Tip
If you want changes to trigger UI updates, use `useState`.
If you want to just store a value or a DOM node, use `useRef`.

Let me know if you want examples involving animations, intervals, or more!