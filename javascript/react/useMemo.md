## 🧠 What is `useMemo`?
useMemo is a React Hook that caches the result of a computation so that it only re-runs when its dependencies change.

```javascript
const memoizedValue = useMemo(() => computeSomething(a, b), [a, b]);
```

- ✅ Purpose: Prevents unnecessary recalculations

- ✅ Returns: A memoized (cached) value

- ✅ Recalculates: Only if values in the dependency array change

## 💡 Why use `useMemo`?
Imagine a component re-renders often, and inside it you call a heavy function (like filtering a huge list). You don’t want to recalculate unless the inputs change. That’s where `useMemo` shines.

### 🔧 Example: Expensive Calculation
```javascript
import { useMemo, useState } from "react";

function ExpensiveComponent({ number }: { number: number }) {
  const [otherState, setOtherState] = useState(0);

  const expensiveResult = useMemo(() => {
    console.log("Computing...");
    let result = 0;
    for (let i = 0; i < 1e7; i++) {
      result += number;
    }
    return result;
  }, [number]);

  return (
    <div>
      <div>Result: {expensiveResult}</div>
      <button onClick={() => setOtherState((prev) => prev + 1)}>
        Re-render
      </button>
    </div>
  );
}
```

#### 🔍 Without `useMemo`, the expensive loop runs every time you click the button.

#### ✅ With `useMemo`, it only re-runs when `number` changes.



## 📌 `useMemo` vs `useCallback`
|Hook |	Returns |	Use Case |
|-----|--------- | ---------|
| `useMemo` |	A value	| Memoize result of a calculation |
| `useCallback`	| A function |	Memoize function identity (e.g., for props) |

## 🚨 When NOT to use `useMemo`
Don’t overuse it. It adds complexity and sometimes takes more memory than just recalculating.

### Use it when:

- The calculation is expensive

- The component re-renders frequently

- The inputs rarely change


## ✅ Summary
- What: `useMemo(() => compute(), [deps])` memoizes a computed value

- Why: To avoid recalculating unless needed

- When: Use for performance optimization in heavy or frequent computations