## 🔁 What is `useCallback`?
```javascript
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);
```

### ✅ Purpose:
- Returns a memoized function

- Prevents unnecessary re-creation of a function between renders

- Helps with performance optimization, especially when passing callbacks to child components


<Br>

## 🔍 Why do we need it?
In React, **functions are redefined every time a component renders**. If you're passing functions to child components as props, this can trigger unnecessary re-renders even when the logic hasn’t changed.


<Br>

## ⚠️ Problem Without `useCallback`
```javascript
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log("Clicked!");
  };

  return (
    <Child onClick={handleClick} />
  );
}
```

🔄 Every render of `Parent` creates a new version of `handleClick`.
If `Child` is memoized (via `React.memo()`), it will still re-render because `onClick` is "new".

<Br>

## ✅ Fix With `useCallback`
```javascript
const handleClick = useCallback(() => {
  console.log("Clicked!");
}, []);
```
Now `handleClick` is the same function instance unless dependencies change — preventing unnecessary re-renders.

<Br>

## 💡 `useCallback` vs `useMemo`
| Hook |	Returns	| Use Case |
|------|------------|----------|
| `useMemo` |	A value |	Cache result of a calculation |
| useCallback |	A function |	Cache a function reference |

<Br>

## 🧠 Real-World Example with `React.memo`
```javascript
const Button = React.memo(({ onClick }: { onClick: () => void }) => {
  console.log("Button rendered");
  return <button onClick={onClick}>Click Me</button>;
});

function App() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Button onClick={handleClick} />
    </>
  );
}
```
✅ Without `useCallback`, the Button would re-render every time you increment, even though `onClick` didn't change logically.

## 🔄 When to Use useCallback
Use it when:

- You pass functions to memoized child components

- You use functions inside a dependency array (like `useEffect`)
(More detailed Explanation down below)

- You're optimizing render performance

## 🚫 When NOT to Use `useCallback`
- If you're not passing the function as a prop

- If your component doesn't re-render often

- If the function is cheap to recreate


<br>

## 📝 Summary

| Feature |	Description |
|---------|-------------|
| Hook |	`useCallback(fn, deps)` |
| Returns |	Memoized version of the function |
| Benefit |	Prevents unnecessary re-creations of functions |
| Common Use |	Props to React.memo children, useEffect deps |


## Tips
`useCallback` (and also useMemo) compares dependencies using strict equality (===), which for objects and arrays means it's comparing memory addresses (references), not their contents.

<Br>
<Br>

# 🔁 Using functions inside dependency arrays (like in `useEffect`, `useCallback`, etc.)
When you put a function inside a dependency array, React tracks whether that exact function reference changes between renders — not whether its contents change.

Let’s break it down:

<Br>

## ✅ Example 1: Function created outside the component (stable)
```javascript
function logSomething() {
  console.log("Hello");
}

function MyComponent() {
  useEffect(() => {
    logSomething();
  }, [logSomething]); // ✅ No re-run unless logSomething changes
}
```
`logSomething` is defined outside the component.

Its reference doesn’t change between renders, so React doesn’t re-run the effect.

<Br>

## 🔁 Example 2: Function created inside the component (changes every render)
```javascript
function MyComponent() {
  const logSomething = () => {
    console.log("Hello");
  };

  useEffect(() => {
    logSomething();
  }, [logSomething]); // ⚠️ logSomething is recreated on every render
}
```

Even though the body is the same, `logSomething` is a new function object on every render.

So the effect will run on every render.

To fix this, you’d wrap it in `useCallback`:

```javascript
const logSomething = useCallback(() => {
  console.log("Hello");
}, []);
```

Now React can stabilize the function reference, and the effect won’t rerun unless its dependencies change.

<Br>

## 🧠 Why does React treat functions as dependencies?
Because functions can close over values (closures). If you write:

```javascript
const log = () => console.log(counter);
```

Then whether `log` changes or not matters, because it may be closing over an older `counter` value. That's why React wants you to explicitly manage that by tracking the real function reference.

<Br>

## ✅ Real-world example
```javascript
function MyComponent({ user }) {
  const fetchData = useCallback(() => {
    fetch(`/api/data?userId=${user.id}`);
  }, [user.id]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);
}
```
`fetchData` is declared using `useCallback` and only depends on `user.id`.

The `useEffect` only re-runs when `user.id` changes, not every render.

<Br>

## 🛑 Anti-pattern: Ignoring function dependencies
Some people try this:

```javascript
useEffect(() => {
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, []);
```

This can create bugs due to stale closures. Prefer using `useCallback` to stabilize the function and include it in the dependencies.

<Br>

## TL;DR
| Function location |	React behavior in dependency array |
|----|-----|
| Outside component	| ✅ Reference is stable |
| Inside component	| ❌ Re-created on every render |
| Wrapped in `useCallback`	| ✅ Stable (as long as deps don’t change) |

Always track the real function reference, and if the function is declared inside the component and used in effects or callbacks, use `useCallback` to stabilize it.